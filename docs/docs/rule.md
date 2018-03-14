---
title: Rule Specification
---

# Rule Specification
You can define a rule using two styles

```yaml
# Compact
- Hello!
- How are you, {name}?

# Configurable
- message: Hello! What's your name?
  name: name
  type: String
  replyMessage: Welcome, {name}!
```

| Property | Default | Type | Description |
|----------|---------|------|-------------|
| name | - | string | Rule name used to jump between rules or to be store in output
| message | - | string | Message to be talk ([formatting message](#formattedmessage))
| output | - | string | Custom key to be stored in output (if empty, the property *name* will be used)
| type | - | string | A pre-defined or user-defined type of user answer. Pre-defined: `String`, `Number`, `SingleChoice`, `MultipleChoice`, [`Passive`]({{ site.baseurl }}/docs/passive-mode#passive-types), [`PassiveLoop`]({{ site.baseurl }}/docs/passive-mode#passive-types) or `Any`. Check out the [extensions]({{ site.baseurl }}/docs/extensions)
| next | - | string | Name of next rule
| delay | `1000` | number | Fake time to simulate a bot typing. By default, the bot calculates this time based on message length and average of typing speed. If entered, the calculation is ignored
| sleep | `0` | number | Fake time to simulate the AFK mode (await from keyboard for a defined time)
| replyMessage | - | string | Auto-reply after user's answer ([formatting message](#formattedmessage))
| options | `[]` | array[[RuleOption](#ruleoption)] | Configuration of answer options used in `SingleChoice` and `MultipleChoice` types
| maxOptions | - | number | Paginate the options above, limiting by max per page. UI bundle only.
| validators | `[]` | array[[RuleValidator](#rulevalidator)] | Configuration to validate the user answer in the first executor
| config | - | object{[[string]: any](#stringsearch-rule)} | Extra configuration needed by rule
| passive | true | boolean | Force disable Passive mode in this rule only
| actions | `[]` | array[[RuleAction](#ruleaction)] | Executes an action after bot's message
| preActions | `[]` | array[[RuleAction](#ruleaction)] | Executes an action before bot's message
| postActions | `[]` | array[[RuleAction](#ruleaction)] | Executes an action after user's answer
| skip | `false` | `boolean | (output, rule, bot) => boolean` | Skip rule based on conditional function
| multline | `true` | `boolean` | Enable multline input text, otherwise use single line input
| exit | `false` | boolean | Terminates the conversation on this rule

# FormattedMessage

YveBot has a small template engine to compile the bot messages (supported by `message` or `replyMessage` props).
You can inject the user's answers into your messages, example:

```yaml
- message: What's your name?
  name: name
  type: String

- Okay {name}, thanks
```

```
> What's your name?
> Paul
> Okay Paul, thanks
```

In Single/MultipleChoice cases, you can inject the option label instead of value, see:

```yaml
- message: Choose a number
  name: number
  type: SingleChoice
  options:
    - label: One
      value: 1
    - label: Two
      value: 2
  replyMessage: Humm.. {number} or {number.label}
```

```
> Choose a number
> 1
> Humm.. 1 or One
```


# RuleOption

```yaml
# Compact style
- message: Which colors do you like?
  options:
    - Red
    - Blue

# Configurable style
...
  options:
    - label: Red
      value: red
    - value: blue
    - label: More colors
      synonyms: more, other, no
      next: more_colors
```

| Property | Default | Type | Description |
|----------|---------|------|-------------|
| value | - | string | Value of options stored in output. If empty, returns value of `label` property
| label | - | string | A friendly name shown to user. If empty, returns value of `value` property
| synonyms | - | string or string[] | A comma-separated or an array of synonyms to be considered in answer
| next | - | string | Rule name to jump after user's answer


# RuleValidator

```yaml
- message: What is your email?
  validators:
    - email: true
    - userDefinedValidator: true
```

| Property | Expected | Description |
|----------|----------|-------------|
| required | boolean | Assert non-empty value
| regex | string | Test with regex
| minWords | number | Assert minimum of words
| maxWords | number | Assert maximum of words
| min | number | Assert minimum of characters
| max | number | Assert maximum of characters
| length | number | Assert has exactly length
| string | boolean | Assert answer is string
| number | boolean | Assert answer is number
| email | boolean | Assert answer is email
| function | function | Assert result of function

_** validators are used ONLY in the first executor from rule's type_


# RuleAction

```yaml
- message: Wait a moment
  actions:
    - timeout: 5000
    - userDefinedAction: http://api/post
```

| Property | Parameter | Description |
|----------|-----------|-------------|
| timeout | number | Executes setTimeout

[Next: Flows: Grouping rules]({{ site.baseurl }}/docs/flows){:.btn}
