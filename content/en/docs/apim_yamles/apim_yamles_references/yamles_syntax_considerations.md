{
"title": "YAML syntax considerations",
"linkTitle": "YAML syntax considerations",
"weight":"80",
"date": "2020-09-25",
"description": "Syntax considerations when editing a YAML configuration."
}

When modifying your YAML configuration, consider the following:

## Strings fields

For strings literal we chose the "plain mode" of YAML as it is more readable, here are some caveat you want to avoid :

| Syntax                                 | Status    |
|----------------------------------------|-----------|
| `name: Foo`                            | OK        |
| `name: "Foo"`                          | OK        |
| `name: ${system.host}`                 | Error     |
| `name: "${system.host}"`               | OK        |
| `name: '${system.host}'`               | OK        |
| `name: "Please avoid trailing spaces "`| OK        |
| `name: " Please avoid leading spaces"` | OK        |
| `name:     So many whitespaces !`      | Warning   |

For this last case leading and trailing spaces will be eluded --> Value will be "`So many whitespaces !`"

## int, long, boolean fields

For int, long, boolean, null types quoting is not recommended (it tolerated today, may not always will)

| Syntax                          | Status                      |
|---------------------------------|-----------------------------|
|`integerField: 0`                | OK                          |
|`integerField: -1`               | OK                          |
|`integerField: 9999999999999999` | Error (it a long)           |
|`integerField: 100_000`          | Error                       |
|`integerField: "1"`              | Avoid (though it will work) |
|`longField: 21546873548687`      | OK                          |
|`longField: 1_000_000_000_000`   | Error                       |
|`longField: "21546873548687"`    | Avoid (though it will work) |
|`booleanField: true`             | OK                          |
|`booleanField: false`            | OK                          |
|`booleanField: 0`                | Avoid (though it will work) |
|`booleanField: 1`                | Avoid (though it will work) |
|`booleanField: yes`              | Avoid (though it will work) |
|`booleanField: no`               | Avoid (though it will work) |

## Lists

YAML Entity Store exports lists without indentation.

This is how YAML Entity Store writes a YAML file (this is done by tools such as ES Explorer when updating or creating and Entity)

```yaml
type: FilterCircuit
fields:
  name: ...
# some other fields
children:
- type: ...
  fields:
    name: ...
    multi:
    - value 1
    - value 2
  children:
  - type: ...
    fields:
# some other stuff
```

But this is also supported :

```yaml
type: FilterCircuit
fields:
  name: ...
# some other fields
children:
  - type: ...
    fields:
      name: ...
      multi:
        - value 1
        - value 2
    children:
      - type: ...
        fields:
# some other stuff
```

{{% alert title="Note" color="primary" %}}`refs: {}` <-- this is an empty list{{% /alert %}}