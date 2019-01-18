# redux-orm

This is a repository for my own understanding of redux-orm and Redux structuring in general. I will be using this to document my notes while reading:  

[Practical Redux](https://blog.isquaredsoftware.com/series/practical-redux/)

[Practical Redux Training Course](https://blog.isquaredsoftware.com/2017/11/practical-redux-educative-course/)

**Assumed knowledge:** ORM Libraries (at least the concept) and relational table schemas

## Basics

### Define your model classes

```javascript
// models.js
import {Model, fk, oneToOne, many} from "redux-orm";

export class Pilot extends Model{}
Pilot.modelName = "Pilot";
Pilot.fields = {
  mech : fk("Battlemech"),
  lance : oneToOne("Lance")
};

export class Battlemech extends Model{}
Battlemech.modelName = "Battlemech";
Battlemech.fields = {
    pilot : fk("Pilot"),
    lance : oneToOne("Lance"),
};

export class Lance extends Model{}
Lance.modelName = "Lance";
Lance.fields = {
    mechs : many("Battlemech"),
    pilots : many("Pilot")
}
```

### Register your Schema

```javascript
import {Schema} from "redux-orm";
import {Pilot, Battlemech, Lance} from "./models";

const schema = new Schema();
schema.register(Pilot, Battlemech, Lance);
export default schema;
```

### Set up Store and Reducer

```javascript
// entitiesReducer.js
import schema from "models/schema";

// This gives us a set of "tables" for our data, with the right structure
const initialState = schema.getDefaultState();

export default function entitiesReducer(state = initialState, action) {
    switch(action.type) {
        case "PILOT_CREATE": {
            const session = schema.from(state);
            const {Pilot} = session;

            // Queue up a "creation" action inside of Redux-ORM
            const pilot = Pilot.create(action.payload.pilotDetails);

            // Applies the queued actions and returns an updated
            // "tables" structure, with all updates handled immutably
            return session.reduce();
        }
        // Other actual action cases would go here
        default : return state;
    }
}

// rootReducer.js
import {combineReducers} from "redux";
import entitiesReducer from "./entitiesReducer";

const rootReducer = combineReducers({
    entities: entitiesReducer
});

export default rootReducer;
```

## Core Concepts

### Sessions

A class that provides an API to interact with your data. `schema.reducer()` creates a Session class internally, however you can create Sessions by calling `schema.from(entities)` that works with the schema in an immutable way.

### Models

An encapsulated representation of the underlying state structure. Getters and Setters are used to abstract away all interaction.

Once a Model object has been created, it turns into a denormalized state. That means it is a O(1) complexity for access.

### Updates

Uses an internal state that queues up actions in a "local redux". The underlying implementation creates actions that are passed in a reducer-like way. These changes are applied immutably, in-sequence to produce a final result.

### Managing Relations

Uses a QuerySet class to manage collections. A QuerySet knows the Model type and contains a list of Ids.

For many-to-many relationships, a QuerySet is created to manage this relationship.

E.g. `Lance` with `pilots: many("Pilot")` creates a LancePilot class and table.

#### Examples

Using Reselect to return an entities Session:

```javascript
import {createSelector} from "reselect";
import schema from "./schema";

export const selectEntities = state => state.entities;

export const getEntitiesSession = createSelector(
    selectEntities,
    entities => schema.from(entities),
);
```

Creating a generic updateEntity reducer:

```javascript
export function updateEntity(state, payload) {
    const {itemType, itemID, newItemAttributes} = payload;

    const session = schema.from(state);
    const ModelClass = session[itemType];

    let newState = state;

    if(ModelClass.hasId(itemID)) {
        const modelInstance = ModelClass.withId(itemID);

        modelInstance.update(newItemAttributes);

        newState = session.reduce();
    }

    return newState;
}
```

Helper functions:

```javascript
export function getModelByType(session, itemType, itemID) {
    const modelClass = session[itemType];
    const model = modelClass.withId(itemID);
    return model;
}

export function getModelIdentifiers(model) {
    return {
        itemID : model.getId(),
        itemType : model.getClass().modelName,
    };
}
```

Modifying a Session instance (using internal state):

```javascript
const session = schema.from(entities);
const {Pilot} = session;

const pilot = Pilot.withId(pilotId);
pilot.name = "Natasha Kerensky";

// Immutably apply updates, then point the session at the updated state object.
session.state = session.reduce();

// All field/model lookups now use the updated state object
```

Note: Do not use this on a shared session, as it does create side-effects. If you need to access a shared session, create a new one instead:

```javascript
export function getUnsharedEntitiesSession(state) {
    const entities = selectEntities(state);
    return schema.from(entities);
}
```

**Reducer pattern:**

Reducers typically move in this order:

1. Extract parameters from incoming data
2. Create a Session
3. Queue updates
4. Apply updates using `session.reduce()`
5. return state