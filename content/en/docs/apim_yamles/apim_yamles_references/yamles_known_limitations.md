{
"title": "Known limitations",
"linkTitle": "Known limitations",
"weight":"50",
"date": "2020-09-25",
"description": "List of known limitations related to the YAML configuration."
}

## Policy Studio

There is no support for YAML configurations in Policy Studio.

## API Gateway Manager web UI

Deployment of YAML configurations via API Gateway Manager web UI is not supported.

## Custom Entity types

Custom entity types are not supported in YAML configuration.

## Team Development

Limited support of Team development. No tooling support to look for merge conflicts. Ordering of tar-level merges needs to be considered.
See [Team Development with YAML configuration](/docs/apim_yamles/apim_yamles_references/yamles_team_development) section for details.

## ESExplorer

Support of ES Explorer limited to viewing and editing YAML configurations.

When an entity store is edited via ES Explorer or the entity store API, some fields in other entities may get reordered causing more diffs than are really required.

## Config Fragment

There is no YAML equivalent of a configuration fragment for export or import purposes.

## Environmentalized key fields

YAML configuration does not support Environmentalized key fields.

## Windows OS

`yamles` CLI cannot run on Windows. Consider use of docker image with Linux-based OS.

## API Gateway Group Instance

An API Gateway instance cannot be added to a group when the group has a YAML configuration deployed to it.

## Automated Upgrade

Automated upgrade of YAML configurations created by the September 2020 release will not be upgradeable in the November 2020 release.  
Future changes may introduce breaking changes that will require manual upgrade until automated upgrade is supported.

## Certificate

Private keys are in external files in DER format, there is no support for PEM format.
Certificates are in external files in PEM format, with the PEM header and footer lines removed. No support for DER format.

## API Manager

You cannot run `setup-apimanager` on an instance that has a YAML configuration deployed to it.
See [Creating a new instance in a group where YAML is deployed](/docs/apim_yamles/yamles_packaging_deployment/#creating-a-new-instance-in-a-group-where-yaml-is-deployed) section.

## Node Manager

YAML configuration for Node Manager is not supported

## API Gateway Analytics

YAML configuration for Analytics is not supported

## YAML factory configuration

A YAML factory configuration is not provided out-of-the-box, but can be created by converting the XML federated factory configuration using `yamles fed2yaml`.

## Deployment archive

Updating deployment archive package properties using `managedomain` option 22.
See [Updating Deployment Archive Properties](/docs/apim_yamles/yamles_packaging_deployment/#updating-deployment-archive-properties) section.