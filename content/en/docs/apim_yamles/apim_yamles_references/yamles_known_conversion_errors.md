{
"title": "Known conversion errors",
"linkTitle": "Known conversion errors",
"weight":"40",
"date": "2020-09-25",
"description": "Learn how to fix errors that might occur when converting XML Federated configuration to YAML configuration."
}

This section covers examples of issues that may occur when using the `fed2yaml` option of the `yamles` CLI tool.

## Entities of different type with same PK at same level where one has children

The entity store has two or more entities of different types at the same level with the same PK where one has children, this is not allowed in the YAML Entity Store.

**Severity**: ERROR

Exception raised

`ERROR: EntityStoreException: com.vordel.es.EntityStoreException: The entity store has two or more entities of different types at the same level with the same PK where one has children, this is not allowed in the YAML entity store. The entity '{ CircuitContainer ESPK = null with parent pk as null and fields as {name=[name{Test}]} }' has the same PK as these entities with children '[{ FilterCircuit ESPK = /Policies/Some Policies/(FilterCircuit)Test with parent pk as /Policies/Some Policies and fields as {name=[name{Test}], start=[start{/Policies/Some Policies/(FilterCircuit)Test/(JSONPathFilter)JSON Path}], description=[description{}], logMask=[logMask{3}], category=[category{/System/Policy Categories/miscellaneous}]} }]'`

You cannot convert an XML configuration to YAML if there are two entities of different entity types at the same level with the same key value, where one of them has child entities. For example this could occur if you have:

* a policy container named `Test` which has a child policy,
* and at the same level as the container, there is a a policy named `Test`.

So we have a `CircuitContainer` and a `FilterCircuit` clashing.

![Conversion Error](/Images/apim_yamles/yamles_conversion_error_case1_1.png)

To fix this issue, rename either the policy or the policy container as follows and rerun the conversion:-

![Conversion Error](/Images/apim_yamles/yamles_conversion_error_case1_2.png)

## Entities of different types at same level with same PK for parent PK (case 1)

**Severity**: WARNING

`WARNING: Found entities of different types at same level with same PK for parent PK :'/Server Settings/Logging Configuration' PK end with: (XMLRollOverLogger)Text Rollover File Logger
WARNING: Found entities of different types at same level with same PK for parent PK :'/Server Settings/Logging Configuration' PK end with: (TextRollOverLogger)Text Rollover File Logger`

The factory configuration has two entities one of type `TextRollOverLogger` and one of type `XMLRollOverLogger`. They reside at the same level but have the same name. The conversion process will compensate for this by adding the type to the filename e.g. `(TextRollOverLogger)Text Rollover File Logger.yaml` and `XmlRollOverLogger)Text Rollover File Logger.yaml`.

In order to fix you must rename the `XmlRollOverLogger` to `XML Rollover Logger` using ESExplorer and rerun. Leaving unfixed will not cause any issues.

## Entities of different types at same level with same PK for parent PK (case 2)

`WARNING: Found entities of different types at same level with same PK for parent PK :'/Policies/Some Policies/Test' PK end with: (ChangeMessageFilter)Same name filter
WARNING: Found entities of different types at same level with same PK for parent PK :'/Policies/Some Policies/Test' PK end with: (Reflector)Same name filter`

In this case the conversion process has found two filters in the same policy that are different types of filters, but have the same key i.e. name. In Policy Studio the policy would look like this :

![Conversion Error](/Images/apim_yamles/yamles_conversion_error_case1_3.png)

The conversion process compensates for this by including the type in the YamlPk when either filter is referred to:

```yaml
---
type: FilterCircuit
fields:
  name: Test
  start: ./(ChangeMessageFilter)Same name filter
  description: ""
children:
- type: Reflector
  fields:
    name: Same name filter  #### conflict issue here
  logging:
    fatal: 'Error occurred while echoing the message. Error: ${circuit.exception}'
    failure: Failed to echo back the message
    success: Successfully echoed back the message
- type: ChangeMessageFilter
  fields:
    name: Same name filter  #### conflict issue here
    body: <body>Test!</body>
    outputContentType: text/html
  routing:
    success: ../(Reflector)Same name filter
  logging:
    fatal: 'Error in setting the message. Error: ${circuit.exception}'
    failure: Failed in setting the message
    success: Success in setting the message
```

To fix, rename one of the filters in Policy Studio and rerun the conversion. This is preferable as you can refer to both filters using the usual YamlPK form without the entity type included. The validate option will generate the same WARNING. The configuration will work without any fix.
