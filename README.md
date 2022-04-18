# No Closures

[![npm](https://img.shields.io/npm/v/eslint-plugin-no-closure)](https://www.npmjs.com/package/eslint-plugin-no-closure)
[![tests](https://github.com/erikbrinkman/eslint-plugin-no-closure/actions/workflows/tests.yml/badge.svg)](https://github.com/erikbrinkman/eslint-plugin-no-closure/actions/workflows/tests.yml)
[![license](https://img.shields.io/github/license/erikbrinkman/eslint-plugin-no-closure)](LICENSE)

An eslint plugin to prevent unwanted function closures.

## Usage

To use this plugin, first install it
```
yarn add -D eslint-plugin-no-closure
```

Then add it to your eslintrc
```
{
  ...,
  "plugins": [ ..., "no-closure" ],
  "rules": {
    ...,
    "no-closure/no-tagged-closures": "error"
  }
}
```

## disallow closing around variables for tagged functions (no-tagged-closures)

Sometimes you want to prevent a function from referencing variables outside of its scope, for example if you intend to serialize the function.
Since this is allowed in javascript, it's not possible to outright ban, but this rule will report where this is happening for tagged functions.

### Rule Details

This rule disallows closures in functions that are tagged.
A tagged function must have a comment that starts with `eslint-no-closure` immediately before the function, or as a line comment on the line immediately preceeding it.

### Options

By default, the rule will report the declarations of variables that are closed in tagged function, tagged functions that close any variables, and any references to closed variables within tagged functions.
Since this produces up to three reports for a single violation, each class of reports can be independently disabled.

- `declaration` set to `"never"` to not report the declaration of variables that are closed (Default `"always"`).
- `function` set to `"never"` to not report tagged functions that close variables (Defalt `"always"`).
- `reference` set to `"never"` to not report references to closed variables in tagged functions (Default `"always"`).

### Examples

Examples of **incorrect** code for this rule:

```js
/*eslint no-closure/no-tagged-closures: "error"*/

const x = 5;
// eslint-no-closure
function foo() {
  void x;
}
```

Examples of **correct** code for this rule:

```js
/*eslint no-closure/no-tagged-closures: "error"*/

const x = 5;
function foo() {
  void x;
}
```

```js
/*eslint no-closure/no-tagged-closures: "error"*/

// eslint-no-closure
function bar() {
  const x = 5;
  function foo() {
    void x;
  }
}
```

```ts
/*eslint no-closure/no-tagged-closures: "error"*/

type X = number;
// eslint-no-closure
function foo() {
  const x: X = 5;
}
```

### When Not To Use It

This rule only checks functions that are tagged, so there's little harm in using it.
However, this current doesn't support selective closures, so this rule won't work if you want to verify that you only close a set of specific variables.
