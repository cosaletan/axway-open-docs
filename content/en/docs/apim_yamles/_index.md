{
    "title": "YAML configuration",
    "linkTitle": "YAML configuration",
    "weight":"87",
    "date": "2020-09-24",
    "hide_readingtime": "true",
    "description": "Learn how to use YAML-based configuration with AMPLIFY API Management solution."
}

## Introduction

API Gateway main configuration is created through Policy Studio in XML format. This configuration can now be converted into a YAML format, edited in an IDE of your choice, and deployed to the API Gateway runtime. The YAML-based configuration allows for a human-friendly format to manage configuration, which facilitates the use of industry standard tooling and enhance the collaboration and operational options. YAML-based configuration enables:

* A better experience with Software Configuration Management (SCM) tooling for managing diffs and merging.
* Easier use of standard tooling in DevOps pipelines.
* Team collaboration with multiple developers working on the same configuration project.
* Environmentalization of any piece of configuration. For example, passwords, certificates.
* Externalization of configuration that naturally belongs in a separate file. For example, scripts, schemas, keys, certificates.
* Usage of standard code editing tools. For example, IDE for JavaScript, Groovy.
* Use of standard certificate and private key generation tools, and certificate viewing tools.

## Glossary

| Term             | Description |
| ---------------- | ----------- |
| **Entity Store** | The API Gateway's configuration is also called [Entity Store](/docs/apigtw_devguide/entity_store/). It is a store of all the entities it takes to configure the API Gateway runtime, for example, the policies and filters. There are now two formats available for the Entity Store: the XML federated configuration and the YAML configuration. |
| **XML federated configuration** | Consists of a small set of XML files. This can be packaged into a `.fed` file, or a set of `.pol` and `.env` files for deployment. |
| **YAML** | YAML Ain't Markup Language (YAML) is a descriptive language based on JSON, widely used to describe configurations. |
| **YAML configuration** | The YAML configuration or **YAML Entity Store** consists of a large set of YAML files in an easy-to-navigate directory structure. This can be packaged into a standard `.tar.gz` file for deployment.
| **yamles** | The CLI client tool for converting, validating, and encrypting YAML configuration. |
