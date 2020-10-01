{
"title": "Use the CLI to convert API Gateway configuration",
"linkTitle": "Use the CLI to convert configuration",
"weight":"30",
"date": "2020-09-24",
"description": "Learn how to use the YAML configuration CLI tool."
}

The `yamles` CLI tool is available in the `apigateway/posix/bin` directory. It is installed with a standard server-side install or with client tooling, when the **Package & Deploy Tools only** option is selected during the installation.

You can run the `yamles` script with `--help` to see the available options:

```
./yamles --help
```

There are four options available:

* `fed2yaml`: Convert an XML federated configuration to a YAML configuration.
* `validate`: Validate a YAML configuration.
* `encrypt`: Encrypt an unencrypted YAML configuration, a string, or a file.
* `change-passphrase`: Change the passphrase of a YAML entity store.

To get help with a specific option, run:

```
./yamles <option> --help
```

All script parameters have shorthand parameter names, check the relevant help for more information.

All script options generate a trace file in the current directory by default. You can use `--tracedir` to write trace to another directory, or `--tracelevel` to change the level of tracing.

## Convert your XML configuration to a YAML configuration

The starting point for using the YAML format is to convert an existing valid upgraded XML federated configuration, which can be stored in a `.fed` file, a `.pol`, or a `.pol` and `.env`, or in the usual format of a set of XML files.

{{< alert title="Note">}}Converting XML configuration fragments to YAML is not supported directly. To convert an XML configuration fragment, import the fragment into a factory XML federated configuration, then convert it to YAML.{{< /alert >}}

Before converting your XML federated configuration you must upgrade it using [upgradeconfig](/docs/apim_installation/apigw_upgrade/upgrade_analytics/#upgradeconfig-options) or [projupgrade](docs/apim_reference/devopstools_ref/#projupgrade-command-options).

The `--output-dir` must always be specified. This is the location where the YAML configuration will be written. Optionally, if the `--targz` option is used, the conversion will also create a `.tar.gz` file that is ready for deployment. The `.tar.gz` is created in addition to the content in the `--output-dir`.

The following are examples of the various usages of the `fed2yaml` option in the `yamles` CLI. Use the following help command for more detail on each parameter.

```
yamles fed2yaml --help
```

* `./yamles fed2yaml --fed-url federated:file:/home/user/apiprojects/myfed/configs.xml --output-dir ~/yaml`:

    Convert an XML federated configuration by specifying a URL for the XML and place the YAML configuration into the `~/yaml directory`. Make sure to include the `configs.xml` when using `--fed-url`.
* `./yamles fed2yaml --fed-url federated:file:/home/user/apiprojects/myfed/configs.xml --output-dir ~/yaml --targz /home/user/archives/myconfig.tar.gz`:

    Convert an XML federated configuration by specifying a URL for the XML and place the YAML configuration into the `~/yaml` directory, and create a `.tar.gz` file of the content of `~/yaml`.
* `./yamles fed2yaml --fed /home/user/archives/config.fed --output-dir ~/yaml`:

    Convert an XML federated configuration by specifying a `.fed` file for the XML and place the YAML configuration into the `~/yaml` directory.
* `./yamles fed2yaml --fed-dir /home/user/apiprojects/myconf --output-dir ~/yaml`:

    You can use the `--fed-dir` parameter to convert multiple XML federated configurations in one run. For example, if you point to your Policy Studio `apiprojects` directory, all projects will be converted. A sub-directory of the same name is created in the `--output-dir` to contain the converted YAML configuration. If used in this way, the `--targz` should be set to a directory name, not a `tar.gz` filename, to create a `.tar.gz` for each converted configuration.

    You can set the `--targz` parameter to the same value as the `--output-dir`. This can also be used to convert a single XML federated configuration by specifying a directory that contains a single XML federated configuration. In this case, ensure your `--output-dir` does not already exist. The `--targz` is not supported when used with `--fed-dir`. For a single XML federated configuration, use `--fed-url` instead if you want to convert and create the `--targz` in one command.
* `./yamles fed2yaml --fed /home/user/archives/mypol.pol --env /home/user/archives/myenv.env --output-dir ~/yaml`:

    Create an XML federated configuration by merging a `.pol` and `.env` file, and then convert it to YAML. A `.pol` file may be specified on its own, but a `.env` file must be combined with a `.pol`.

{{< alert title="Note">}}There is no facility to convert a YAML configuration back to XML, and this will not be supported in the future.{{< /alert>}}

To learn how to solve conversion errors, see [known conversion errors](/docs/apim_yamles/apim_yamles_references/yamles_known_conversion_errors).

## Validate configuration changes in the YAML configuration

To make changes to your YAML configuration file, use an IDE or editor of your choice. Your editor can assist with ensuring that the YAML is valid, and that it is indented properly. The `yamles` validate option can assist to ensure that the content you have added or modified is valid according to the Entity Store model. The error content will point towards the YAML file where the problem is located.

The following are examples of the various usages of the validate option in the `yamles` CLI. Use the following help command for more detail on each parameter.

```
yamles validate --help
```

* `./yamles validate --yaml-url yaml:file:/home/user/apiprojects/myyaml`:

    Validate a YAML configuration by specifying a URL.
* `./yamles validate --yaml-url yaml:file:/home/user/apiprojects/myyaml --passphrase changeme`:

    Validate an encrypted YAML configuration by specifying a URL and passphrase.
* `./yamles validate --targz /home/user/archives/myconfig.tar.gz`:

    Validate a YAML configuration in a .tar.gz file.
* `./yamles validate --yaml-dir /home/user/yaml`:

    Validate a YAML configuration by specifying a directory.

The `--yaml-dir` parameter may also be used to valid multiple YAML configurations in one run. For example, if you converted all your XML Policy Studio projects in the `~/apiprojects` directory to YAML with an `--output-dir` of `~/yaml-apiprojects`, you could point to your `~/yaml-apiprojects` directory, and all projects will be validated.

### Disable entity reference check

In some cases you may have a valid configuration that points to an entity that exists in another configuration. This is the case for [Team Development](/docs/apim_yamles/apim_yamles_references/yamles_team_development) when you wish to keep common configuration in a separate project for reuse purposes. You may need to validate each configuration separately before merging without generating errors for fields that point to entities that exist elsewhere. Use the `--allow-invalid-ref` option to ensure that an error is not generated in this case. With this parameter enabled, warnings are traced but no error is generated.

This section covers examples of issues that may be found using the validate option. ERRORs are shown on stdout. Note that WARNINGs will only be listed on stdout if there are other ERRORs. If only WARNINGs are found, they are listed in the trace file only.

To learn how to solve conversion errors, see [known validation errors](/docs/apim_yamles/apim_yamles_references/yamles_known_validation_errors).

## Encrypt an unencrypted YAML configuration

Encryption of XML or YAML configuration means that sensitive fields of type `encrypted` in the Entity Store model are encrypted. All other data remains in the clear. If the XML federated configuration you converted to YAML was not encrypted, it remains unencrypted after conversion. If you wish to deploy the configuration to an API Gateway group that has a group passphrase set, you must encrypt the YAML configuration before deployment.

If you get an error when attempting to encrypt the YAML configuration, that indicates that the passphrase for the configuration is incorrect, it means that the configuration is already encrypted. Use the `change-passphrase` option to change the passphrase.

It is also possible to encrypt a YAML configuration at deployment time via [projdeploy](/docs/apim_reference/devopstools_ref/#projdeploy-command-options).

The following are examples of the various usages of the `encrypt` option in the `yamles` CLI to encrypt an unencrypted YAML configuration. Use the following help command for more detail on each parameter:

```yamles encrypt --help```

* `./yamles encrypt --yaml-url yaml:file:/home/user/apiprojects/myyaml --passphrase changeme`: Encrypt a YAML configuration by specifying a URL with passphrase `changeme`.
* `./yamles encrypt --targz /home/user/archives/myconfig.tar.gz --passphrase changeme`: Encrypt a YAML configuration in a `.tar.gz` file with passphrase `changeme`.
* `./yamles encrypt --yaml-dir /home/user/yaml --passphrase changeme`: Encrypt a single YAML configuration by specifying a directory with passphrase `changeme`.

## Encrypt strings and files to add to an encrypted YAML configuration

If the XML federated configuration you converted to YAML was encrypted with a passphrase, it remains encrypted with the same passphrase after conversion to YAML format. If you wish to add new configuration that includes fields of type `encrypted`, you need to be able to encrypt that data with the same entity store passphrase so that it can be read by the API Gateway when deployed. Say for example you wish to add an entity of type `DbConnection` which has a field `password` which is of type `encrypted`. You must put the encrypted value of the password string into the YAML file for the `DbConnection`. If your database password is `dbpassword` and your passphrase for the YAML configuration is `changeme`.

Use the following command to encrypt the database password string value:

```./yamles encrypt --text "dbpassword" --passphrase "changeme"```

The output will be as follows:

```
Your encrypted base64 encoded string content is:-
wWVn7dS/ycwDg7Miqd1TU0YKTiOY//5i
```

Copy and paste the string `wWVn7dS/ycwDg7Miqd1TU0YKTiOY//5i` into your yaml file for your `DbConnection`:

```yaml
---
type: DbConnection
fields:
  url: jdbc:mysql://127.0.0.1:3306/DefaultDb
  name: MySQL/local
  username: root
  password: wWVn7dS/ycwDg7Miqd1TU0YKTiOY//5i
```

A private key is sensitive data that is externalized into a separate file in the YAML configuration. Its data is also of type `encrypted` and so the file content must be encrypted if the YAML configuration is encrypted. A private key file can be encrypted as follows:

```
./yamles encrypt --file /home/user/private-key.der --passphrase "changeme"
```

See section [Add a new certificate and private key to a YAML configuration](/docs/apim_yamles/yamles_edit/#add-a-new-certificate-and-private-key-to-a-yaml-configuration) for more information regarding adding a certificate and private key to a YAML configuration. Note that `encrypt --file` option may be used to encrypt the content of any type of file. The operation will update the file which will always have binary content after encryption. Note that encrypting a private key via `openssl` and adding it to the YAML configuration is not supported.

## Change the encryption passphrase of a YAML configuration

If you have encrypted your YAML configuration you may wish to encrypt it with a different passphrase. You may also need to decrypt it by setting the `--new-passphrase` to `""`. In order to do this you need to know what the current passphrase is.

It is also possible to change the passphrase of a YAML configuration at deployment time via [projdeploy](/docs/apim_reference/devopstools_ref/#projdeploy-command-options).

The following are examples of the various usages of the `change-passphrase` option in the `yamles` CLI.

Use the following help command for more detail on each parameter.

```yamles change-passphrase --help```

* `./yamles change-passphrase --yaml-url yaml:file:/home/user/apiprojects/myyaml --old-passphrase changeme --new-passphrase newpassphrase`:

    Change the passphrase of a YAML configuration by specifying a URL from `changeme` to `newpassphrase`.
* `./yamles change-passphrase --yaml-url yaml:/home/user/apiprojects/myyaml --old-passphrase changeme --new-passphrase ""`:

    Decrypt a YAML configuration currently encrypted with passphrase `changeme`.
* `./yamles change-passphrase --targz /home/user/archives/myconfig.tar.gz --old-passphrase changeme --new-passphrase newpassphrase`:

    Change the passphrase of a YAML configuration in a `.tar.gz` file from `changeme` to `newpassphrase`.
* `./yamles change-passphrase --yaml-dir /home/user/yaml --old-passphrase changeme --new-passphrase newpassphrase`:

    Change the passphrase of a single YAML configuration by specifying a directory from `changeme` to `newpassphrase`.
* `./yamles change-passphrase --yaml-url yaml:file:/home/user/apiprojects/myyaml --old-passphrase changeme --new-passphrase`:
    Encrypt an unencrypted YAML configuration, this is equivalent to the "encrypt" option.