# VariableJson

[![GitHub](https://img.shields.io/github/license/variablejson/variablejson)](https://github.com/variablejson/variablejson/blob/main/LICENSE)

VariableJson is a JSON parser that adds support for variables.

### Supported languages
| Language | in-JSON Variables | Environment Variables|
| :---: | :---: | :---: |
| [C#](https://github.com/variablejson/variablejson-csharp) | :white_check_mark: | :white_check_mark: |
| [JavaScript](https://github.com/variablejson/variablejson-js) | :white_check_mark: | :x: |
| [Python](https://github.com/variablejson/variablejson-python) | :white_check_mark: | :x: |

> **Note**: Features are based off of the latest package version for that language. If you see a feature not available in a language, please know that we fully plan to support it in the future.

### Examples

The simplest example is

```json
{
  "$vars": {
    "name": "John Doe"
  },
  "johndoe": "$(name)"
}
```

which gets converted to

```json
{
  "johndoe": "John Doe"
}
```

Variables can also reference other variables

```json
{
  "$vars": {
    "name": "John Doe",
    "greeting": "Hello $(name)"
  },
  "johndoe": "$(greeting)"
}
```

which generates

```json
{
  "johndoe": "Hello John Doe"
}
```

You can nest objects and arrays with each other and all other non-complex data types. Here is a complex sample

```json
{
  "$vars": {
    "name": "John Doe",
    "greeting": "Hello $(name)",
    "age": 42,
    "address": {
      "street": "123 Main St",
      "city": "Anytown",
      "state": "CA",
      "zip": "12345"
    },
    "phone": ["000-123-4567", "000-123-4568"]
  },
  "johndoe": {
    "name": "$(name)",
    "greeting": "$(greeting)",
    "age": "$(age)",
    "address": "$(address)",
    "phone": "$(phone)"
  }
}
```

which would give you

```json
{
  "johndoe": {
    "name": "John Doe",
    "greeting": "Hello John Doe",
    "age": 42,
    "address": {
      "street": "123 Main St",
      "city": "Anytown",
      "state": "CA",
      "zip": "12345"
    },
    "phone": ["000-123-4567", "000-123-4568"]
  }
}
```

You can reference objects and values that are in an array by specifying the index

```json
{
  "$vars": {
    "array": [1, false, true, "hello, world!", null]
  },
  "first": "$(array.0)"
}
```

which would produce

```json
{
  "first": 1
}
```

You cannot reference objects that are not stored in the variable container. For example, this will not work

```json
{
  "$vars": {
    "hello": "world!"
  },
  "fizz": "$(buzz)",
  "buzz": "$(hello)"
}
```

This will throw an exception because `fizz` references a variable that does not exist in the variable container.

Lastly, you can also reference environment variables using `$[EnvironmentVariableName]`

```json
{
  "PATH": "$[PATH]"
}
```

When referencing **only** environment variables you do not need to specify a variable container, it won't be used. You may use both environment variables and variables in the same JSON file.

```json
{
  "$vars": {
    "name": "John Doe"
  },
  "johndoe": "$(name)",
  "PATH": "$[PATH]"
}
```

which produces

```json
{
  "johndoe": "John Doe",
  "PATH": "..." // varies by system
}
```

### Usage

Check the `examples` folders for information on how to use the library in your project for a given language.

### VariableJsonOptions

You can specify some options when parsing JSON using the `VariableJsonOptions` class.

The following options are available:

`VariableKey` - The name of the variable container. Defaults to `$vars`.

`Delimiter` - The delimiter to use when parsing variables. Defaults to `.` (period). This string should not appear in any of your JSON key names.

`MaxRecurse` - The maximum number of times to recurse when resolving variables. Defaults to 1024.

`KeepVars` - Whether or not to keep the variable container in the output. Defaults to `false`. The variable container will be identical to the one in the input. It's value will not be resolved.

`EmittedName` - The name of the variable container in the output. Defaults to `$vars`. Only used if `KeepVars` is `true`.

### JSON Schema

While VariableJson itself is valid JSON, it uses special markers to denote variables. This means that you can't use these same markers in string-type values. To identify that you want to use the variable value instead, you should wrap the variable name in `$(variableName)` and set it as a string value.

```json
{
  "$vars": {
    "name": "John Doe"
  },
  "johndoe": "$(name)"
}
```

If you don't want to use `$vars` as the variable container, you can use the `VariableJsonOptions` class to specify a different variable container name. For example if set set the container name to `myVars` this will cause variable lookups to be performed in the `myVars` object instead of the `$vars` object.

```json
{
  "myVars": {
    "name": "John Doe"
  },
  "johndoe": "$(name)"
}
```
