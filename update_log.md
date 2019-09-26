*Init*

This repo is for the purposes of a high-level view of React architecture and best practices. As this repo grows, more ideas will be introduced and comparisons will be made with pros and cons.

Being that this is a public repo, I hope that people will not only take what they can from the repo's information, but contribute as well. Feel free to submit pull requests and I will add your information to the document.

Forking this repo is great if you want to create your own repo with your decisions, using this as a starting point.

*1-27-19*

The repo has taken a narrowed down form from everything suggested to what I'm using in my own projects. This is a vetting process and what you see here is what I eventually chose.

To keep this as a good reference template (or guide), I'm going to keep this narrow approach. If you have any differing opinions, let me know as an issue! Or feel free to fork this document and insert your own conclusions.

*7-22-19*

I am in the process of removing `redux-orm` from my stack. I have several reasons for this, namely:

- Too much *magic* (Your data is hidden behind objects that, upon runtime inspection, reveal no useful information. This can be resolved by using a `.ref` to get it's data, but that would defeat the purpose)
- Hard to onboard new members to the library
  - Steep learning curve
  - Use of classes as interfaces with only documentation to support their usage
  - Difficult to debug
  - Community is small (currently only has 2,500 stars on Github)
- Still hasn't achieved RC status (last major release introduced breaking changes)

For these reasons, I'm going to move back towards `normalizr`, with suggestions on how to use it correctly. When used properly, you can achieve the same normalized state status that `redux-orm` achieves, while still dealing with good ole' fashioned JS objects.
`normalizr` also has a much larger community, sitting at 17,000 stars on Github, and has a long history.

I have always found that the key to longevity/usability/readability/maintainability in Javascript these days is how many people back your library/architecture/whatever... *Hence React*

I also want to explore the new hook `useReducer` and how this can be used to minimize the need for boilerplate when it comes to state managment as a whole.

More updates to come...
