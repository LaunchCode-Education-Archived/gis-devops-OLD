---
title: "Walkthrough: ESLint Ariwaze"
---

## Setup

- Open the `airwaze` project/repository that you have used previously.
- Do a `git status` to see if you had any unfinished business, if so please add and commit
- Once you have a *clean* `git status`, checkout and create a new branch with `git checkout -b add-eslint`
- Run `bootRun` and view your map in the browser to confirm a working state before adding any new functionality

## Let's Add ESLint
- Open your `.gitignore` file and add `**/node_modules`
- In terminal go to `airwaze/src/main/resources`
- Run `$ npm init` and wait for instructor to help you answer the questions
- Review the `package.json` file that was just created
- Run `$ npm install eslint --save-dev`
- Run `$ npm install eslint-plugin-import --save-dev`
- Run `$ ./node_modules/.bin/eslint --init` and wait for instructor to help you answer the questions
  - We want to 1) Use a popular style guide. 2) Pick Airbnb 3) NO to using React
- Review the file that was just created `.eslintrc.js`
- Add this to your `package.json` file OR if `"scripts"` is already defined, simply update the value
```
  "scripts": {
        "test": "eslint static/js/script.js"
  },
```

## Let's Do Some Linting!
- Run `$ npm test` and cringe at the "errors" that are found
  - Remember that errors in this case are lines of code that break an eslint rule
- Alter a line, save your file, and run `$ npm test` again
- It's a good idea to check that your map still loads in the browser, before fixing too many linting errors at one time

## Resources
- [ESLint Rules](https://eslint.org/docs/rules/)
