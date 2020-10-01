{
"title": "YAML entity store structure",
"linkTitle": "YAML entity store structure",
"weight":"40",
"date": "2020-09-24",
"description": "Understand the structure of the YAML entity store."
}

The entity store is a generic way to handle any type of configuration. Its model is hierarchical like a file system:

* An entity has a type that gives an entity its properties.
* An entity has a set of named fields.
* Each field contains a value or a list of values.
* An entity might have other entities as children, hence the hierarchical nature of the entity store.
* Fields types are: boolean, integer, long, string, binary, encrypted & reference (a pointer to another entity).
* One or more fields are designated as key fields. The values of the key fields must be unique for an entity of the same type at a given location in the hierarchy. For example, two PDF document with the same name cannot coexist in the same directory.
* An entity is uniquely identified by a primary key (PK).

The YAML entity store has been designed to expose entities in a readable way. Folders are organized so that it looks like Policy Studio's layout.

![YAML File Structure vs Policy Studio](/Images/apim_yamles/yamles_structure_ps_vs_yaml.png)

{{< alert title="Note">}}This structure may slightly evolve in future versions, but the core principles of how it works will remain the same.{{< /alert >}}

## Files and directories

### Root directory

The root directory contains:

* `_parent.yaml` containing the unique root type entity.
* `values.yaml` is used for [Environmentalization](/docs/apim_yamles/yamles_environmentalization). (Optional).

Any other file is ignored, YAML or not.

### Top level directories

The root entity has many direct children. To sort them out, a first level of hierarchy has been setup. See [YAML Entity Store Directory Mapping](/docs/apim_yamles/apim_yamles_references/yamles_top_directories) for details.

| Policy Studio Hierarchy       | YAML configuration top level directory |
| ----------------------------- | -------------------------------------- |
| APIs                          | APIs                                   |
| Deployment package properties | META-INF/*.mf                          |
| Environment Configuration     |                                        |
| Many of non-editable settings | System                                 |
| Policies                      | Policies                               |
| Resources                     | Resources                              |
| Server Settings               | Server Settings                        |
| Policy Studio Hierarchy       | YAML configuration top level directory |
| External Connections        | External Connections                   |
| Libraries                   | Libraries                              |
| Listeners                   | Environment Configuration/Service      |

### META-INF

The META-INF folder contains the `Types.yaml` file. This file contains the definition of all the entity types in the entity store model. This it is where you can find useful information about the fields you can use for an [entity type](/docs/apim_yamles/apim_yamles_references/yamles_types).

### _parent.yaml

A `_parent.yaml` is a special type of file within the YAML entity store. It is an entity, but it is best described as a container entity. Its  purpose is to contain other child entities, hence the *parent* name. It is named `_parent.yaml` so it can be identified as a different file. Thus, every `.yaml` file at the same level are child entities of the entity defined in `_parent.yaml`.

The directory in which `_parent.yaml` is stored is named after its content by default, but it can be named differently.

Example:

![_parent.yaml example](/Images/apim_yamles/yamles_parent_file_example.png)

```yaml
# Monitoring Configuration/_parent.yaml
---
type: RealtimeMonitoring
fields:
   name: Monitoring Configuration
```

Child entity:

* `Monitoring Configuration/Metrics Metadata/_parent.yaml`

```yaml
# Monitoring Configuration/Metrics Metadata/_parent.yaml
---
type: MetricsMetadata
fields:
   name: Metrics Metadata
```

Children entities:

* `System Metric Group Types.yaml`
* `System Metric Types.yaml`

A directory containing a `_parent.yaml` is a container for other entity.

### Key fields

Each entity in the entity store is identified by one or several key fields. For most types, it is a single field called "name". For others, it can be just one field, such as ID or URL, or a combination of several. For example, RadiusServer entity type has two key fields: "host" and "port".

### Best practices

By default, after having converted your entity store to YAML:

* A directory is named after the key field value in `_parent.yaml` contained in the directory. The key field is `name` in most of the cases.
* A YAML file is named after the value of its key fields. In case of multiple key fields, it is concatenation of them separated by a coma.

{{< alert title="Note">}}File system incompatible characters such as `/\":<>*?|` will be replaced by `_` (underscores).{{< /alert >}}

These are the good practices to name your entities and files:

* Name your file after your entity. If it has several key fields, do your best to represent those key fields.
* Use short but meaningful names. If a name exceeds 40 characters, the last part of the name will be replaced by a hash code.
* Use only letters and numbers.

Sometimes you cannot respect the rules, and some key fields will contain forbidden characters. For example, for entity type `JSONSchema` key field is `URL`. If the value of URL is `http://json-schema.org/address`, then the YamlPK could be `/Resources/JSON Schemas/http://json-schema.org/address`.

The YAML entity store will handle this case as follows:

* The file will be named `http_json-schema.org_address.yaml` (with `:` and `/` replaced by `_`).
* The YamlPK is still valid even if it contains several `/`. It is used as a whole internally.

If your entity file is not named using the entity key fields, it will work. However, if the entity is modified by ES Explorer, or via the entity store API, your files will be renamed to use the default naming scheme, that is, the name of the entity or the concatenation of the key fields.

## Entity files model

You can use single or multiple entity files.

### Single entity file

The following is an example of how to list all the possible elements you can encounter.

```yaml
---
type: SomeFictionalType
fields:
  string_field_name: Hello world
  int_or_long_field_name: 42
  boolean_field_name: true
  encrypted_field_name: DKdjrenz==
  multi_line_value_field_name:
  - value 1
  - value 2
  reference_field_name: /System/some system entity
```

Multi value fields are only allowed if permitted by the type. All values in the list must be of the same type (integer, long, string, binary, encrypted, reference).

### Multiple entity file

A file can contains a parent entity and its children. This is useful when the full hierarchy make sense as a whole.

```yaml
---
type: SomeFictionalParentType
fields:
  field_1: Hello children,
  field_2: I am you parent.
children:
- type: SomeFictionalChildType1
  fields:
    field_a: Hello progenitor!
- type: SomeFictionalChildType2
  fields:
    field_b: Hi dear parent!
  children:
    type: SomeFictionalGrandChildType
    fields:
      field_b: Hi ancestors!
```

{{< alert title="Note">}} You will notice `---` at the beginning of some example YAML files, and in the YAML files converted from XML. This the default behavior of the YAML format. It is optional, and it works perfectly without it.{{< /alert >}}

## Policies

Policies are one of the most frequently used entity types. Its type is `FilterCircuit`. It contains a list of filters with over 200 derived types.

Some common fields of the super type `Filter` have been customized for the YAML entity store to make the file more readable. All the configuration of a policy is contained into one single YAML file.

Syntax such as `./First Filter` or `../Second Filter` are explain in [YamlPK and References](/docs/apim_yamles/yamles_structure/#yamlpk-and-references) .

```yaml
type: FilterCircuit
fields:
  name: My too simple policy
  description: "This is just to explain a few extra fields"
  start: ./First Filter
  fault: ./Fault Filter
children:
- type: CompareAttributeFilter
  fields:
    name: First Filter
  routing:
    success: ../Second Filter  # corresponds to successNode (labelled "Success Path" in Policy Studio)
    failure: ../Error Filter   # corresponds to failureNode (labelled "Failure Path" in Policy Studio)
- type: ChangeMessageFilter
  fields:
    name: Second Filter
    body: <status>ok</status>
    outputContentType: text/xml
  logging:
    fatal: Error in setting the message       # corresponds to logFatal (Log messages in the 2nd screen in Policy Studio when add/edit a filter)
    failure: Failed in setting the message    # corresponds to logFailure
    success: Success in setting the message   # corresponds to logSuccess
    maskType: FILTER                          # corresponds to logMaskType
    mask: 1                                   # corresponds to logMask
...
```

YAML can be written in several ways. For more information, see [YAML syntax considerations](/docs/apim_yamles/apim_yamles_references/yamles_syntax_considerations) for details.

## YamlPK and References

Each entity in the YAML entity store is uniquely identified in a YamlPK. A YamlPK takes account of:

* The position of the entity in the hierarchy.
* The value of the key fields.
* What top level directory it is located in.

### YamlPK anatomy

A YamlPK is composed of the key field values of an entity.

The format of a YamlPK is:

```
/Top-level Folder/parentKeyFieldValue-0[,parentKeyFieldValue-n]*[/.../lastChildKeyFieldValue-0[,lastChildKeyFieldValue-n]*]
```

Normally, there is only one key field:

```
/Libraries/Cache Manager/HTTP Sessions
```

An example with multiple key fields:

```
/Policies/App Policies/Core Policy/Filter DB IP/10.25.54.85,255.255.255.0
```

A YamlPK is used when a field value is not a primitive value, such as integer or string, but it points to another entity. There are two notations: *Absolute* and *Relative*.

**Absolute references**: YamlPK string representations as described above. They always starts with `/`. They work everywhere.

```yaml
type: AuthzCodePersist
fields:
    name: Authz Code Store
    cache: /Libraries/Cache Manager/OAuth AuthZ Code Cache
```

In this example, you can see the entity pointing to a cache named `OAuth AuthZ Code Cache`. The YamlPK includes the top level directory, to facilitate file navigation.

**Relative references**: Some entities can be stored in the same file, for example a parent entity and its descendants. To avoid using (sometimes very long) absolute reference, if a reference points to an entity in the same file, it can be relative:

* Child reference `./keyFieldValue-0[,keyFieldValue-1]`
* Sibling reference `../keyFieldValue-0[,keyFieldValue-1]`
* Cousin reference `../../keyFieldValue-0[,keyFieldValue-1]/keyFieldValue-0[,keyFieldValue-1]/`

Example:

```yaml
# YamlPK of this entity => /Policies/App Policies/Core Policy/Filter DB IP
type: FilterCircuit
fields:
  name: Filter DB IP
  start: ./IP Filtering.                            # relative ref
  category: /System/Policy Categories/miscellaneous # absolute ref
children:
- type: IpFilter
  fields:
    name: IP Filtering
  routing:
    success: ../Log Success message # relative (sibling)
    failure: ../Set Failure message # relative (sibling)
  children:
- type: IpNetMask
    fields:
      address: 10.52.58.9
- type: ChangeMessageFilter
  fields:
    name: Log Success message
  routing:
  failure: /Policies/App Policies/Core Policy/Filter DB IP/Set Failure message # absolute ref to a sibling (bad practice)
# ...
- type: ChangeMessageFilter
  fields:
    name: Set Failure message
# ...
```

In most cases, a YamlPK tells you where the YAML file is.

| YamlPK                                  | File Location                             | Comments                                        |
| --------------------------------------- | ----------------------------------------- | ----------------------------------------------- |
| /Policies/API Policies/Authentication   | Policies/API Policies/Authentication.yaml | Direct mapping, most common scenario            |
| /System/Policy Categories/miscellaneous | System/Policy Categories.yaml             | Entity is a child (or grandchild in some cases) |
| /Policies/API Policies                  | Policies/API Policies/_parent.yaml        | Entity is a container                           |

### YamlPK compatibility

YamlPK has equivalence to Portable ESPK and Shorthand notations.

The following key

```
/Policies/App Policies/Core Policy/Filter DB IP/10.25.54.85,255.255.255.0
```

corresponds to Portable ESPK (shorthand)

```
/[CircuitContainer]name=App Policies/[FilterCircuit]name=Core Policy/[IpFilter]name=Filter DB IP/[IpNetMask]address=10.25.54.85,mask=255.255.255.0
```

or, the following ESPK (XML):

```xml
<key type="CircuitContainer">
    <id field='name' value='App Policies'/>
    <key type="FilterCircuit">
        <id field='name' value='Core Policy'/>
        <key type="IpFilter">
            <id field='name' value='Filter DB IP'/>
            <key type="IpNetMask">
                <id field='address' value='10.25.54.85'/>
                <id field='mask' value='255.255.255.0'/>
            </key>
        </key>
    </key>
</key>
```

Portable ESPK and Shorthand notations are supported by the YAML entity store. This way you can specify an entity using a Portable ESPK, and it will work in both the XML federated configuration and the equivalent YAML configuration.

If you are using a script that calls the entity store API, it works with both formats of configuration. If the script is not running within the API Gateway runtime, you will need to change the URL used to connect to the entity store. A URL such as `federated:file:/home/user/xml-config/configs.xml` will need to change to `yaml:file:/home/user/yaml-config`.

Here is an example script that runs via a `Scripting filter` in the API Gateway runtime that uses a Portable ESPK:

```
// hide imports
def invoke(msg)         {
    String policyName=msg.get("loopPolicyGetOrganizationId");

    String[] policyParts =  policyName.split("/");
    String espkString = "<key type='FilterCircuit'><id field='name' value='"+policyParts[policyParts.length-1]+"'/></key>";
    for (int i=policyParts.length-2; i>=0; i--) {
      espkString = "<key type='CircuitContainer'>" +
          "<id field='name' value='"+policyParts[i]+"'/>" +
          espkString +
          "</key>";
    }
    PortableESPK espk = PortableESPKFactory.newInstance().createPortableESPK(espkString);
    CircuitImpl circuit = Service.getInstance().getLocalPack().cache.getCircuit(espk);
    String context = "DefaultContext";

    // do some stuff before...

    InvocationEngine.invokeCircuit(circuit, context, msg);
    // ...
}
```

### Limitations

YamlPK is not an immutable key, it is a concatenation of all the parent key fields and child key fields. This has two consequences:

**Changing a key field changes the YamlPK value**: For example, there is an entity as follows, with YamlPK `/Policies/App Policies/Core Policy/Filter DB IP`:

```yaml
---
type: FilterCircuit
fields:  
name: Filter DB IP
```

You then, edit the entity at some later point in time to be:

```yaml
---
type: FilterCircuit
fields:
name: Filter Database IP
```

It now has a YamlPK of `/Policies/App Policies/Core Policy/Filter Database IP`. This means that all other entities pointing to this policy through a reference field must be changed to reflect this.

**Two or more entities at the same level in the hierarchy should not have the same key field value regardless of their types**: This was allowed for entities of different types in the XML federated configuration, but is not allowed in the YAML configuration regardless of the types. This limitation allows the YamlPk form to stay as simple as possible by avoiding inclusion of type information.

See [Known conversion errors](/docs/apim_yamles/apim_yamles_references/yamles_known_conversion_errors/) for details.
