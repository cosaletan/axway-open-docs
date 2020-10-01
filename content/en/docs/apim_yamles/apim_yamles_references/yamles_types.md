{
"title": "Entity types in YAML configuration",
"linkTitle": "Entity types in YAML configuration",
"weight":"110",
"date": "2020-09-25",
"description": "Learn how entity types are described in a YAML configuration."
}

The `types.yaml` located under the `META-INF` directory of a YAML configuration contains the definition of all the entity types in the Entity Store model. An [entity type](/docs/apigtw_devguide/entity_store/#entity-types) is a description of an entity in the Entity Store.

The YAML Entity Store supports all entity types but custom types.

## Simple type

```yaml
name: JMSSession                       # name used in YAML entity file
version: 5
constants:
  descriptorClass:
    type: string
    value: com.vordel.client.manager.filter.jms.JMSTransportDescriptor
fields:
  cloneCount:
    type: integer
    defaultValues:
    - data: 1                          # an example a defaulted field (mandatory but having a default value)
    cardinality: 1
  duplicatesOK:
    type: boolean
    defaultValues:                     # this is an optional field.
    - {}
    cardinality: '?'
  messageRemovalPolicy:
    type: string
    defaultValues:
    - data: UNLESS_EXCEPTION           # if you do specify this field in you YAML file, value will be 'UNLESS_EXCEPTION'
    cardinality: 1
  messageRemovalProperty:
    type: string
    defaultValues:
    - data: jms.message.remove
    cardinality: '?'
  name:                                # this one a mandatory field -- it is actually a key field
    type: string
    defaultValues:
    - {}
    cardinality: 1
  servicePK:                           # this fields must contain a reference to another entity of type 'JMSService'
    type: '@JMSService'                # '@' char tells it is a reference
    cardinality: 1
components:
  JMSConsumer: '?'                     # an entity of this type JSMSession can have 1 children of type JMSConsumer
keyFields:
- name
class: com.vordel.dwe.jms.JMSSession
loadorder: 1000100
```

{{% pageinfo color="primary" %}}
`{}` is YAML syntax for an empty list.
{{% /pageinfo %}}

## Types with inheritance

```yaml
name: Process
version: 0
abstract: true                 # abstract means you cannot use it in YAML entity file
keyFields:
- name
fields:
  name:                        # only field the type
    type: string
    defaultValues:
    - {}
    cardinality: 1
children:
- name: JavaProcess            # JavaProcess inherits from Process hence its key field "name"
  version: 0
  abstract: true
  children:
  - name: NetService           # NetService inherits from JavaProcess hence its key field "name"
    version: 5
    constants:
      executableImage:        # this an immutable field user cannot change.
        type: string
        value: vshell
    components:               # NetService is a concrete type, with other fields than "name (key field)
      LoadableModule: '?'     # It is a container for a LoadableModule or ClassLoader entity
      ClassLoader: '?'
```

## Cardinality

| Symbol | Min | Max | Mandatory |
|:------:|:---:|:---:|:---------:|
|   1    |  1  |  1  |    Yes (if no default value) |
|   +    |  1  |  ∞  |    Yes (if no default value) |
|   ?    |  0  |  1  |    No     |
|   *    |  0  |  ∞  |    No     |

## Navigate in Types.yaml

To create a 'NetService' type

* Search for `"name: NetService"`
* In order to know what fields can can used, move up the type hierarchy.
* Search for components (note that some can be defined in the ancestor).
* `NetService` has two components.
    * Search for `"name: LoadableModule"` and/or for `"name: ClassLoader"`.
    * Do first steps again to get all required and optional fields for each entity type.

{{% alert color="warning" %}}Despite what is in the model, some fields are said to be mandatory (cardinality=1 and no default value) are not mandatory. Double check with Policy Studio if in doubt.{{% /alert %}}