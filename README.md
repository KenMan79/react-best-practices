# React Best Practices

This repo is for the purposes of a high-level view of React architecture and best practices. As this repo grows, more ideas will be introduced and comparisons will be made with pros and cons.

Being that this is a public repo, I hope that people will not only take what they can from the repo's information, but contribute as well. Feel free to submit pull requests and I will add your information to the document.

Forking this repo is great if you want to create your own repo with your decisions, using this as a starting point.

*Updated 1-27-19*

The repo has taken a narrowed down form from everything suggested to what I'm using in my own projects. This is a vetting process and what you see here is what I eventually chose.

To keep this as a good reference template (or guide), I'm going to keep this narrow approach. If you have any differing opinions, let me know as an issue! Or feel free to fork this document and insert your own conclusions.

## Architecture

### Patterns

**Flux Pattern**  
https://code-cartoons.com/a-cartoon-guide-to-flux-6157355ab207  

**Render Props Pattern**  
https://medium.freecodecamp.org/how-to-develop-your-react-superpowers-with-the-render-props-pattern-b74e68c6d053

### Functional Paradigm

#### Functional Programming

- [Immutability](https://medium.com/dailyjs/the-state-of-immutability-169d2cd11310)
  - No side-effects
  - Predictable data
- [Functional methods](https://medium.com/dailyjs/the-state-of-immutability-169d2cd11310)
  - map/filter/reduce
    - [w3Schools array functions](https://www.w3schools.com/jsref/jsref_obj_array.asp)
- [Higher Order Components/Functions](https://medium.freecodecamp.org/higher-order-components-the-ultimate-guide-b453a68bb851)
  - Reusability and Composition

#### Pure Components

- fn(state, props) == UI
- Local state suggestions from: https://medium.com/@robftw/characteristics-of-an-ideal-react-architecture-883b9b92be0b
  - No local state other than:
    - Animation/triggering of CSS Classes
    - UI control that doesn't involve business logic or dependent on other components
    - Extremely trivial UI related variables that won't cause side effects
    - Standalone libraries

### Routing

**React Router**  
https://github.com/ReactTraining/react-router

Guide:  
https://reacttraining.com/react-router/web/guides/quick-start

### State Management

**Redux**  
https://redux.js.org/

**Redux DevTools**  
[Chrome Extension](https://chrome.google.com/webstore/detail/redux-devtools/lmhkpmbekcpmknklioeibfkpmmfibljd?hl=en)

This is a must have for Redux development.

Great article if you want to hone your skills:  
https://codeburst.io/redux-devtools-for-dummies-74566c597d7

#### Middleware

**Redux Thunk**  
https://github.com/reduxjs/redux-thunk

"Thunks are the recommended middleware for basic Redux side effects logic, including complex synchronous logic that needs access to the store, and simple async logic like AJAX requests."

**Redux-Sagas**  
https://github.com/redux-saga/redux-saga

Similar concept to thunk using Middleware to control side-effects in a React-Redux application, but with the added bonus of using ES6 Generators to control async flow and increase readability.

[More on Generators](https://redux-saga.js.org/docs/ExternalResources.html)

### State Structure

Normalized data structures by Id (Relational Table Model)

```javascript
{
  entities: {
    Post: {
      itemById: {
        "post1" : {
          id: "post1"
          author: "author1"
          body: "..."
          comments: ["comment1", "comment2"]
        },
        "post2" : {
          id: "post2"
          author: "author2"
          body: "..."
          comments: ["comment3", "comment4", "comment5"]
        },
      },
      items: ["post1", "post2"],
      meta: {},
    },
    User: {
      itemsById: {
        "user1": {
          username: "user1",
          name: "User 1",
        },
        "user2": {
          username: "user2",
          name: "User 2",
        },
        "user3": {
          username: "user3",
          name: "User 3",
        },
      },
      items: ["user1", "user2", "user3"],
      meta: {},
    },
    Comment: {
      itemsById: {
          "comment1": {
            userId: "user1",
            postId: "post1",
            body: { ... },
          },
          "comment2": {
            userId: "user2",
            postId: "post2",
            body: { ... },
          },
          "comment3": {
            userId: "user3",
            postId: "post3",
            body: { ... },
          },
          "comment4": {
            userId: "user4",
            postId: "post4",
            body: { ... },
          },
          "comment5": {
            userId: "user5",
            postId: "post5",
            body: { ... },
          },
        },
        items: ["comment1", "comment2", "comment3", "comment4", "comment5"],
        meta: {}
    },
  },
  
  
  simpleDomainData1: {...},
  simpleDomainData2: {...},
}
```

Removes the deeply nested structures as your state grows in size and keeps components from re-rendering accidentally.

### Folder Structure

```
.
├── /android/
├── /ios/
|
├── /assets
│   ├── /fonts/
│   ├── /images/
│   ├── /videos/
|
├── /app or /src
│   ├── /ducks/  // See "Ducks"
│   ├── /scenes/
│   |   ├── /App
│   |   |   ├── /_/
│   |   |   ├── /App.js
│   |   |   └── /index.js
|   |   ├── /MyScene/
|   |   |   ├── /__tests__/
|   |   |   ├── /_
|   |   |   |   ├── /MySubcomponent
|   |   |   |       ├── /__tests__/
|   |   |   |       ├── /MySubcomponent.js
│   |   |   |       └── /index.js
│   |   |   ├── /MyScene.js
│   |   |   └── /index.js
│   ├── /shared/
|   |   ├── /SharedAppSpecificComponent
|   |   |   ├── /__tests__/
|   |   |   ├── /SharedAppSpecificComponent.js
|   |   |   └── /index.js
│   |   └── /types.js
|   ├── /store/ // configureStore.js + middleware
|   └── /utils/ // helper functions, etc.
|
├── /lib
|   ├── /Button
|       ├── /__tests__/
|       ├── /Button.js
|       └── /index.js
|
├── /tools/  //Build scripts, other tools
├── /node_modules/
└── package.json
```

https://github.com/kylpo/react-playbook/blob/master/component-architecture/5_Example-App-Structure.md

**/lib/ explanation:**
Every component that is not "app specific" or a *primitive component* can be a candidate to be moved to the /lib/ folder. This allows for the possible side effect of hand-rolling your own component library as you code!

Primitive components are those that take in only primitives as their props and are functionally pure.

#### Redux "Ducks"

```
.
├── /ducks/
    ├── /entities/ // See redux-orm
    └── /MyDuck/
    |   ├── /__tests__/
    |   ├── /models  // See redux-orm
    |   |   ├── index.js
    |   |   ├── MyDuckModel1.js
    |   |   └── MyDuckModel2.js
    |   ├── actions.js
    |   ├── index.js
    |   ├── reducers.js
    |   ├── selectors.js
    |   ├── types.js
    |   └── utils.js
    ├── /utils/ // Reducer Utils + more (see redux-orm)
    ├── rootReducer.js
    ├── orm.js  // See redux-orm
    └── commonTypes.js
```

https://medium.freecodecamp.org/scaling-your-redux-app-with-ducks-6115955638be

## Best Practices and Coding Style

### Naming Convention

#### Components

**From:** https://hackernoon.com/react-components-naming-convention-%EF%B8%8F-b50303551505

[Domain]|[Page/Context]|ComponentName|[Type]

Examples:

ACommunityAddToShortListButton - [Domain][ComponentName][Type]  
SideBar - [ComponentName]  
SideBarSwitch - [ComponentName][Type]  
ChatConversation - [Page/Context][ComponentName]  
ChatConversationName - [Page/Context][ComponentName]  

### Component Creation

- Write a stateless functional component first
  - if (component requires state or life-cycle) Consider Hooks
  - if (component is comprised of multiple components or smaller pieces of functionality) Create container component
  - Move shared components out
    - if (app dependent) Keep in shared/MyComponent
    - if (!app dependent or primitive) Move to lib folder
- Avoid re-rendering component if possible

## Styling

**Semantic UI React**  
https://github.com/Semantic-Org/Semantic-UI-React

A JQuery-free version of Semantic UI that comes with React-based components. Semantic UI is great for themeing as well.

## Testing

### Libraries

**Jest**  
https://jestjs.io/

Most popular React testing library. Comes with snapshot comparisons which is very useful for testing pure components.

**Sinon**  
https://sinonjs.org/

Spies, stubs and mocks

**Cypress**  
https://www.cypress.io/

Testing suite that provides a click-and-check API for automated in-browser smoke tests.

### Strategies

**From:** https://github.com/kylpo/react-playbook/blob/master/best-practices/react.md

Write component tests that accomplish the following goals (from [Getting Started with TDD in React](https://semaphoreci.com/community/tutorials/getting-started-with-tdd-in-react?utm_source=javascriptweekly&utm_medium=email))

- It renders
- It renders the correct thing
  - Default props
  - Varied props
- It renders the different states
- Test events
- Test edge cases
  - e.g. something that uses an array should be thrown an empty array

## Build and Deploy

### Starter kits

https://reactjs.org/community/starter-kits.html

This list contains many popular choices, but your choice is yours. Getting a good starter kit depends entirely on what you're looking to get out of your project and team.

### Build Libraries

**Babel**  
https://babeljs.io/

Transpiles your javascript down from the latest standards to backwards compatible, browser friendly source. Used for transpiling JSX to JS.

**Webpack**  
https://webpack.js.org/

Bundles your files into static assets that are ready for production deploy. Many extensible plugins available.

**ESLint**  
https://eslint.org/

Keeps your code nice and tidy by yelling at you with squigglies. You can link this to your IDE.

Great ESLint configs:  
[prettier-eslint](https://github.com/prettier/prettier-eslint)  
[eslint-config-airbnb](https://www.npmjs.com/package/eslint-config-airbnb)  

## Source Control

### Git

#### Repositories

[Github](https://github.com/)

[BitBucket](https://bitbucket.org/)

#### Branch Naming Strategies

**From:** http://www.guyroutledge.co.uk/blog/git-branch-naming-conventions/  

`type/component-name/title`

Common Types:

- feature
- fix
- hot-fix
- refactor
- update
- upgrade
- remove

Component Name example:  
`login-form`

Title example:  
`hoc-formik-input`

## Libraries

### Form Helpers

**Formik**  
https://github.com/jaredpalmer/formik

Creates an ephemeral state held by its components that abstracts away all the boilerplate that goes into creating and maintaining forms.

### State Management Helpers

**Reselect**  
https://github.com/reduxjs/reselect

Creates a selector where the first functions passed in compute props for a final function. If none of those props have changed, then that function is not run and the result from the previous invocation is returned.

This keeps the state from needlessly causing components to re-render.

**redux-orm**
https://github.com/tommikaikkonen/redux-orm

Library that creates Object Relational Mapping (ORM) using the aforementioned structure.

Guide to using redux-orm and Redux structuring as a whole:  
https://blog.isquaredsoftware.com/series/practical-redux/

**Recommended Course:**  
[Practical Redux Course by Mark Erikson](https://www.educative.io/collection/5687753853370368/5707702298738688?authorName=Mark%20Erikson)

### Styling

**classnames**  
https://github.com/JedWatson/classnames

Takes in conditionals that returns a space-delimited string for className.

```javascript
import classnames from 'classnames';

let classes = classnames('sd-date', {
  current: date.month() === this.props.month,
  future: date.month() > this.props.month,
  past: date.month() < this.props.month
});

return (
  <button className={classes} />

  vs

  <button className={`button-default-style ${isActive ? "active" : ""}`} />
);
```

### Functional Programming Helpers

**Recompose**  
https://github.com/acdlite/recompose

A library of Higher-Order-Functions and a compose function to ease your move towards more reusable, functional components.

Note: This library is no longer being updated with new features by its creator. With Hooks on the horizon, the React Development team is suggesting a move to that API.

### Miscellaneous Helpers

**date-fns**  
https://date-fns.org/

Date/Time formatting with a functional paradigm and localization.

## IDEs

**Visual Studio Code**  
https://code.visualstudio.com/

Microsoft has opensourced this light-weight and highly extensible IDE. My personal favorite.

**Atom**  
https://atom.io/

Another IDE that is also very popular amoung web devs.

**Webstorm**  
https://www.jetbrains.com/webstorm/

A JetBrains product who also make IntelliJ and ReSharper. It's not free, but has a mix of monthly and yearly subscription options for the license.


## Resources, Articles and Podcasts

### Podcasts

**React Native Radio w/ Nadir Dabit**  
https://devchat.tv/react-native-radio/

**Syntax w/ Wes Bos and Scott Tolinski**  
https://syntax.fm/

**The React Podcast w/ Chantastic**  
https://reactpodcast.simplecast.fm/

### Publications

**freeCodeCamp**  
https://www.freecodecamp.org/

**HackerNoon**  
https://hackernoon.com/

**React.js Newsletter**  
http://reactjsnewsletter.com/

### Articles

**Good Starting Points:**  
https://github.com/markerikson/react-redux-links

**Folder Structure**  
https://github.com/kylpo/react-playbook/blob/master/component-architecture/3_Multiple-Components.md

**Presentational vs Container Components**  
https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0

**Routing**  
https://medium.com/@algfry12/react-router-best-practices-9c564388f4d3