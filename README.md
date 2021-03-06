# ember-composable-helpers [![Build Status](https://travis-ci.org/DockYard/ember-composable-helpers.svg?branch=master)](https://travis-ci.org/DockYard/ember-composable-helpers) [![npm version](https://badge.fury.io/js/ember-composable-helpers.svg)](https://badge.fury.io/js/ember-composable-helpers) [![Ember Observer Score](http://emberobserver.com/badges/ember-composable-helpers.svg)](http://emberobserver.com/addons/ember-composable-helpers)

composable helpers for Ember that enables more declarative templating. These helpers can be _composed_ together to form powerful ideas:

```hbs
{{#each (map-by "fullName" users) as |fullName|}}
  <input type="text" value={{fullName}} onchange={{action (mut newName)}}>
  <button {{action (pipe updateFullName saveUser) newName}}>
    Update and save {{fullName}} to {{newName}}
  </button>
{{/each}}
```

To install:

```no-highlight
ember install ember-composable-helpers
```

## Configuration
This addon performs optional tree-shaking – you can specify which helpers to whitelist or blacklist using `only` or `except` within your `config/environment.js`:

```js
module.exports = function(environment) {
  var ENV = {
    'ember-composable-helpers': {
      only: ['inc', 'dec', 'pipe'],
      except: ['pipe', 'filter-by']
    }
  };
```

Both `only` and `except` can be safely used together (the addon computes the diff), although it's best if you only use one for your own sanity.

```js
except: ['pipe'] // imports all helpers except `pipe`
only: ['pipe'] // imports only `pipe`
```

## Argument ordering

This addon is built with _composability_ in mind, and in order to faciliate that,
the ordering of arguments is somewhat different then you might be used to.

For all non-unary helpers, the subject of the helper function will always be the last argument.
This way the arguments are better readable if you compose together multiple helpers:

```hbs
{{take 5 (sort "lastName" "firstName" (filter-by "active" array))}}
```

For action helpers, this will mean better currying semantics:

```hbs
<button {{action (pipe (action "closePopover") (toggle "isExpanded")) this}}>
  {{if isExpanded "I am expanded" "I am not"}}
</button>
```

## Available helpers

* [Action](#action-helpers)
  + [`pipe`](#pipe)
  + [`compute`](#compute)
  + [`toggle`](#toggle)
* [String](#string-helpers)
  + [`camelize`](#camelize)
  + [`capitalize`](#capitalize)
  + [`classify`](#classify)
  + [`dasherize`](#dasherize)
  + [`underscore`](#underscore)
  + [`w`](#w)
* [Array](#array-helpers)
  + [`map-by`](#map-by)
  + [`sort-by`](#sort-by)
  + [`filter-by`](#filter-by)
  + [`reject-by`](#reject-by)
  + [`find-by`](#find-by)
  + [`intersect`](#intersect)
  + [`union`](#union)
  + [`take`](#take)
  + [`drop`](#drop)
  + [`repeat`](#repeat)
  + [`range`](#range)
  + [`join`](#join)
  + [`compact`](#compact)
  + [`contains`](#contains)
  + [`append`](#append)
* [Object](#object-helpers)
  + [`group-by`](#group-by)
* [Math](#math-helpers)
  + [`inc`](#inc)
  + [`dec`](#dec)

## Usage

### Action helpers

#### `pipe`
Pipes the return values of actions in a sequence of actions. This is useful to compose a pipeline of actions, so each action can do only one thing.

```hbs
<button {{action (pipe addToCart purchase redirectToThankYouPage) item}}>
  1-Click Buy
</button>
```

**[⬆️️ back to top](#available-helpers)**

#### `compute`
Calls an action as a template helper.

```hbs
The square of 4 is {{compute (action "square") 4}}
```

**[⬆️ back to top](#available-helpers)**

#### `toggle`
Toggles a boolean value.

```hbs
<button {{action (toggle "isExpanded" this)}}>
  {{if isExpanded "I am expanded" "I am not"}}
</button>
```

**[⬆️ back to top](#available-helpers)**

---

### String helpers

#### `camelize`
Camelizes a string using `Ember.String.camelize`.

```hbs
{{camelize "hello jim bob"}}
{{camelize stringWithDashes}}
```

**[⬆️ back to top](#available-helpers)**

#### `capitalize`
Capitalizes a string using `Ember.String.capitalize`.

```hbs
{{capitalize "hello jim bob"}}
{{capitalize fullName}}
```

**[⬆️ back to top](#available-helpers)**

#### `classify`
Classifies a string using `Ember.String.classify`.

```hbs
{{classify "hello jim bob"}}
{{classify stringWithDashes}}
```

**[⬆️ back to top](#available-helpers)**

#### `dasherize`
Capitalizes a string using `Ember.String.dasherize`.

```hbs
{{dasherize "whatsThat"}}
{{dasherize phrase}}
```

**[⬆️ back to top](#available-helpers)**

#### `underscore`
Capitalizes a string using `Ember.String.underscore`.

```hbs
{{underscore "whatsThat"}}
{{underscore phrase}}
```

**[⬆️ back to top](#available-helpers)**

#### `w`
Splits a string on whitespace and/or turns multiple words into an array

```hbs
{{#each (w "First" "Second" "Last") as |rank|}}
  Our {{rank}} place winner is ...
{{/each}}
```

or:

```hbs
{{#each (w "First Second Last") as |rank|}}
  Our {{rank}} place winner is ...
{{/each}}
```

See also: [Ember `w` documentation](http://emberjs.com/api/classes/Ember.String.html#method_w)


**[⬆️ back to top](#available-helpers)**

---

### Array helpers

#### `map-by`
Maps an array on a property.

```hbs
{{#each (map-by "fullName" users) as |fullName|}}
  {{fullName}}
{{/each}}
```

**[⬆️ back to top](#available-helpers)**

#### `sort-by`
Sort an array by given properties.

```hbs
{{#each (sort-by "lastName" "firstName" users) as |user|}}
  {{user.lastName}}, {{user.firstName}}
{{/each}}
```

You can append `:desc` to properties to sort in reverse order.

```hbs
{{#each (sort-by "age:desc" users) as |user|}}
  {{user.firstName}} {{user.lastName}} ({{user.age}})
{{/each}}
```

**[⬆️ back to top](#available-helpers)**

#### `filter-by`
Filters an array by a property.

```hbs
{{#each (filter-by "isActive" true users) as |user|}}
  {{user.name}} is active!
{{/each}}
```

If you omit the third argument it will test if the property is truthy.

```hbs
{{#each (filter-by "address" users) as |user|}}
  {{user.name}} has an address specified!
{{/each}}
```

You can also pass an action as third argument:

```hbs
{{#each (filter-by "age" (action "olderThan" 18) users) as |user|}}
  {{user.name}} is older than eighteen!
{{/each}}
```

**[⬆️ back to top](#available-helpers)**

#### `reject-by`
The inverse of filter by.

```hbs
{{#each (reject-by "isActive" true users) as |user|}}
  {{user.name}} is not active!
{{/each}}
```

If you omit the third argument it will test if the property is falsey.

```hbs
{{#each (reject-by "address" users) as |user|}}
  {{user.name}} does not have an address specified!
{{/each}}
```

You can also pass an action as third argument:

```hbs
{{#each (reject-by "age" (action "youngerThan" 18) users) as |user|}}
  {{user.name}} is older than eighteen!
{{/each}}
```

**[⬆️ back to top](#available-helpers)**

#### `find-by`
Returns the first entry matching the given value.

```hbs
{{#with (find-by 'name' lookupName people) as |person|}}
  {{#if person}}
    {{#link-to 'person' person}}
      Click here to see {{person.name}}'s details
    {{/link-to}}
  {{/if}}
{{/with}}
```

**[⬆️ back to top](#available-helpers)**

#### `intersect`
Creates an array of unique values that are included in all given arrays.

```hbs
<h1>Matching skills</h1>
{{#each (intersect desiredSkills currentSkills) as |skill|}}
  {{skill.name}}
{{/each}}
```

**[⬆️ back to top](#available-helpers)**

#### `union`
Joins arrays to create an array of unique values.

```hbs
{{#each (union cartA cartB cartC) as |cartItem|}}
  {{cartItem.price}} x {{cartItem.quantity}} for {{cartItem.name}}
{{/each}}
```

**[⬆️ back to top](#available-helpers)**

#### `take`
Returns the first `n` entries of a given array.

```hbs
<h3>Top 3:</h3>
{{#each (take 3 contestants) as |contestant|}}
  {{contestant.rank}}. {{contestant.name}}
{{/each}}
```

**[⬆️ back to top](#available-helpers)**

#### `drop`
Returns an array with the first `n` entries omitted.

```hbs
<h3>Other contestants:</h3>
{{#each (drop 3 contestants) as |contestant|}}
  {{contestant.rank}}. {{contestant.name}}
{{/each}}
```

**[⬆️ back to top](#available-helpers)**

#### `repeat`
Repeats `n` times. This can be useful for making an n-length arbitrary list for iterating upon (you can think of this form as a times helper, a la Ruby's `5.times { ... }`):

```hbs
{{#each (repeat 3) as |empty|}}
  I will be rendered 3 times
{{/each}}
```

You can also give it a value to repeat:

```hbs
{{#each (repeat 3 "Adam") as |name|}}
  {{name}}
{{/each}}
```

**[⬆️ back to top](#available-helpers)**

#### `range`
Generates a range of numbers between a `min` and `max` value.

```hbs
{{#each (range 10 20) as |number|}}
  {{! `number` will go from 10 to 19}}
{{/each}}
```

It can also be set to `inclusive`:

```hbs
{{#each (range 10 20 true) as |number|}}
  {{! `number` will go from 10 to 20}}
{{/each}}
```

And works with a negative range:

```hbs
{{#each (range 20 10) as |number|}}
  {{! `number` will go from 20 to 11}}
{{/each}}
```

**[⬆️ back to top](#available-helpers)**

#### `join`
Joins the given array with an optional separator into a string.

```hbs
{{join ', ' categories}}
```

**[⬆️ back to top](#available-helpers)**

#### `compact`
Removes blank items from an array.

```hbs
{{#each (compact arrayWithBlanks) as |notBlank|}}
  {{notBlank}} is most definitely not blank!
{{/each}}
```

**[⬆️ back to top](#available-helpers)**

#### `contains`
Checks if a given value or sub-array is contained within an array.

```hbs
{{contains selectedItem items}}
{{contains 1234 items}}
{{contains "First" (w "First Second Third") }}
{{contains (w "First Second") (w "First Second Third")}}
```

**[⬆️ back to top](#available-helpers)**

#### `append`
Appends the given arrays and/or values into a single flat array.

```hbs
{{#each (append catNames dogName) as |petName|}}
  {{petName}}
{{/each}}
```

**[⬆️ back to top](#available-helpers)**
---

### Object helpers

#### `group-by`
Returns an object where the keys are the unique values of the given property, and the values are an array with all items of the array that have the same value of that property.

```hbs
{{#each-in (group-by "category" artists) as |category artists|}}
  <h3>{{category}}</h3>
  <ul>
    {{#each artists as |artist|}}
      <li>{{artist.name}}</li>
    {{/each}}
  </ul>
{{/each-in}}
```

**[⬆️ back to top](#available-helpers)**

---

### Math helpers

#### `inc`
Increments by `1` or `step`.

```hbs
{{inc numberOfPeople}}
{{inc 2 numberOfPeople}}
```

**[⬆️ back to top](#available-helpers)**

#### `dec`
Decrements by `1` or `step`.

```hbs
{{dec numberOfPeople}}
{{dec 2 numberOfPeople}}
```

**[⬆️ back to top](#available-helpers)**

### See also:

* [ember-truth-helpers](https://github.com/jmurphyau/ember-truth-helpers)

## Legal

[DockYard](http://dockyard.com/ember-consulting), Inc &copy; 2016

[@dockyard](http://twitter.com/dockyard)

[Licensed under the MIT license](http://www.opensource.org/licenses/mit-license.php)
