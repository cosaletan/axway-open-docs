{
"title": "Environmentalize your YAML configuration",
"linkTitle": "Environmentalize your YAML configuration",
"weight":"50",
"date": "2020-09-24",
"description": "Learn how to environmentalize your YAML configuration."
}

The XML federated configuration provides several ways to environmentalize parts of the configuration. See [Environmentalize configuration](/docs/apigtw_devops/promotion_arch/#environmentalize-configuration) for details.

The YAML configuration format replaces any environmentalization done via Policy Studio, which is enabled in Policy Studio through **Preferences > Environmentalization > Allow environmentalization** with an improved method of environmentalization using the `{{ env "XYZ" }}` syntax. The conversion process will look after the replacement of this type of environmentalization. Selectors such as `${environment.XYZ}` and `${env.XYZ}` will continue to work as before.

YAML environmentalization capabilities can be applied to:

* All entities.
* A field's value. This is possible for all types of fields: numeric, string, references, passwords.
* Part of a string field's value.
* Some or all of the content in externalized files such as Scripts, Messages, and Json Schemas. See column **Environmentalization possible inside file content** of table [Externalized files naming Scheme](/docs/apim_yamles/yamles-externalized_files#externalized-files-naming-scheme)

The following table describes all of the supported environmentalization options for the YAML format:

| Feature / File | Syntax | Description |
| -------------- | ------ | ----------- |
| YAML style           | `{{ env "XYZ" }}` | This syntax is allowed anywhere, and replaces the Policy Studio Environmentalization feature. Evaluated by API Gateway at load time using system environment variables, which means that you are warned about missing system environment variables sooner. |
| Environment selector | `${environment.XYZ}` | Only supported for fields that support selectors in the runtime. Evaluated by API Gateway at runtime using system environment variables. This can be replaced by the {{ env "XYZ" }} syntax. |
| envSettings.properties | ${env.xyz} | Evaluated by API Gateway at load time using envSettings.props content specific to an instance. It is likely these settings will remain as they are as they are instance-specific as opposed to environment-specific. |
| system.properties | `${system.xyz}` | Evaluated by API Gateway at load time using `system.properties` file content. |

## Enable environmentalization

To enable this feature, create a file named `values.yaml` in the root directory of your directory of the YAML entity store.

The following is a simple example of environmentalization:

```yaml
# Environmentalized entity in the YAML configuration
---
type: "DbConnection"
fields:
   name: MySQL
   url: "jdbc:mysql://{{ db.host }}:3306/DefaultDb"
   password: {{ db.password }}
   username: "{{ db.username }}"

db:
  host: localhost
  username: scott
  password: HDidnjekj=

```

```yaml
# Content of the value.yaml
---
type: "DbConnection"
fields:
   name: MySQL
   url: "jdbc:mysql://localhost:3306/DefaultDb"
   password: HDidnjekj=
   username: "scott"
```

```yaml
# Resolved YAML Entity (in memory at runtime)
---
type: "DbConnection"
fields:
   name: MySQL
   url: "jdbc:mysql://localhost:3306/DefaultDb"
   password: HDidnjekj=
   username: "scott"
```

`{{ ... }}` placeholders are replaced when the configuration is loaded by the API Gateway or in ES Explorer.

References can also be environmentalized. This is how certificates are environmentalized. For example:

```yaml
# Environmentalized FilterCircuit entity in the YAML configuration
---
type: FilterCircuit
fields:
  name: cert
  start: ./XML Signature Generation
  description: ""
children:
- type: GenerateSignatureFilter
  fields:
    name: XML Signature Generation
    signingCert: {{ certs.sign }}
# ...
```

```yaml
# Content of the value.yaml
---
certs:
  sign: /Environment Configuration/Certificate Store/Samples Test CA
```

```yaml
# Resolved YAML Entity (in memory at runtime)
---
type: FilterCircuit
fields:
  name: cert
  start: ./XML Signature Generation
  description: ""
children:
- type: GenerateSignatureFilter
  fields:
    name: XML Signature Generation
    signingCert: /Environment Configuration/Certificate Store/Samples Test CA
# ...
```

The `values.yaml` above could also point to a system environment variable `CERT_DNAME` that would determine the certificate to be used at runtime.

```yaml
# Content of the value.yaml
certs:
  sign: /Environment Configuration/Certificate Store/{{ env "CERT_DNAME" }}
```

Your CI/CD pipeline must ensure that any certificates and keys are packaged up correctly for each environment in the `.tar.gz` that gets deployed. If you attempt to validate a YAML configuration that uses a system environment variable on a system that does not have a value set for that environment variable, you will need to use the `--allow-invalid-ref` option. See [Disabling entity reference check](/docs/apim_yamles/yamles_cli/#disabling-entity-reference-check) for details.

In a nutshell, the YAML configuration is a template where you can environmentalize all types of values.

## Environmentalization syntax

The following are elements of the environmentalization sintax.

### Variables

**Scope:** YAML Entity files or compatible externalized files.

`{{ expression }}` or `{{expression}}`

Expression can contain:

* Simple properties: `{{ foo }}`
* Nested properties: `{{ foo.bar }}`
* Indexed properties: `{{ foo.bar.[0] }}`. or the equivalent `{{ foo.bar.0}}`. This syntax is useful for multiple values of field.

### Conditions

**Scope:** YAML Entity files or compatible externalized files.

Blocks of data can be added or suppressed based on boolean expression.

```
{{#if hideExpression}}
  hide: true
{{/if}}
```

```
{{#unless hideExpression}}
  hide: false
{{/unless}}
```

Example to setup a block of config at once.

```yaml
---
type: DbConnection
fields:
  url: jdbc:mysql://127.0.0.1:3306/DefaultDb
  password: ""
  name: Default Database Connection
  username: root
{{#unless db.useDefaultDbPoolConfig}}
  initialSize: 10
  maxActive: 50
  maxIdle: 5
  maxWait: 5
  minEvictableIdleTimeMillis: 5000
  numTestsPerEvictionRun: 2
  timeBetweenEvictionRunsMillis: 10000
{{/unless}}
```

### Environment and JVM variables

**Scope**:

* YAML entity files
* `values.yaml` files

You can environmentalize the settings using system environment variables, like `${environment.XYZ}`, except that it can be done anywhere; and JVM properties `-Dxyz` when starting a java process such as the API Gateway.

Environmentalize with no default value:

`{{ env "XYZ" }}` or `{{ env 'XYZ' }}`

Environmentalize with a default value:

`{{ env "XYZ" "The end of the alphabet"}}` or `{{ env "XYZ" default="The end of the alphabet"}}`

The value of `XYZ` is evaluated by the first true condition from this list:

* An environment variable with name XYZ exists.
* An environment variable with name xyz exists (lowercase).
* The JVM was started with `-DXYZ`.
* The JVM was started with `-Dxyz` (lowercase).
* A default value is set.

If none of the above conditions are true, an error is raised in the logs when the API Gateway loads the YAML configuration.

### Base64 encoding

**Scope**:

* `values.yaml` files
* YAML Entity files (not recommended)

You can encode a string value in base64 using:

```{{ base64 "changeme" }}```

This is very useful for development or local environment, when your YAML configuration does not required to be encrypted. This will not work when your YAML configuration is encrypted as recommended for production environments.

### Sub expression

**Scope**:

* `values.yaml` files
* YAML Entity files (not recommended)
* You can combine several expression together

```{{ base64 (env "DB_PASSSWORD" "s3cr3t") }}```

This `base64` example encodes the content of the environment variable `DB_PASSSWORD`, or `s3cr3t` if unset.

### Reserved words

The following words are not allowed at the beginning of a `{{ ... }}` expression:

* `null`
* `true` and `false`
* `undefined`
* Digits as first characters. For example, `{{ 42_foo.bar }}`

In `values.yaml`, you can only use letters (including `_`) and digits (not at starting position).

Examples:

```yaml
# Invalid because of reserved word
null_user:
  name: ""
  
false_expressions:
  - F
  - N
  - 0

#idem for 'true' or 'undefined' reserved words

# Invalid because digit at starting position
1_Services:
   registration: Registration Service

# Valid examples
user_is_null_if:
  name: ""
  
falsy_expressions:
  - F
  - N
  - 0

_1_Services:
   registration: Registration Service

# Valid because the resolved expression {{ validation_schemas.1_user }} does not start with a digit
validation_schemas:
   1_user: http://acme.com/schemas/users
   2_group: http://acme.com/schemas/group
```

## Environmentalization in `values.yaml`

You can use the `{{ env "XYZ" }}` syntax in the `values.yaml` file so you can decouple where the value is coming from and where it is used as follows:

```yaml
---
db:
  host: {{ env "DB_HOST" }} # you set a value an environment variable per environment
```

Later in your development life cycle, you could decide that it is a fixed value that does not change between all environments and change the `values.yaml` to:

```yaml
---
db:
  host: db.com
```

Alternatively, you can choose to use a different `values.yaml` for each environment. So for the staging environment you could have:

```yaml
# values.yaml for staging
---
db:
  host: staging.db.acme.com
```

and for the production environment you could have:

```yaml
# values.yaml for prod
---
db:
  host: prod.db.acme.com
```

This can all be done without changing the YAML file for the entity that always point to `db.host` environmentalized property:

```yaml
#Some Entity.yaml
---
type: DBConnection
fields:
    # just showing e18n fields
    host: {{ db.host }}
```

Refer to [Environmentalization example](/docs/apim_yamles/apim_yamles_references/yamles_environmentalization_example) for a complete example of the different capabilities offered by the YAML configuration environmentalization.

## Convert Policy Studio environmentalization for XML federated configurations to YAML syntax

When XML federated configurations that contain fields environmentalized via Policy Studio are converted to YAML format:

* A `values.yaml` is created.
* Each environmentalized field with a value yields an entry.
    * In `values.yaml`, with the environmentalized values.
    * In `values-original.yaml`, with the value of the field before being environmentalized in Policy Studio.
* A placeholder replaces the value in entity YAML file. The naming follows the YamlPK logic with sanitization that is compliant with the environmentalization syntax.

The following is an example for entity with an environmentalized URL, username, and password:

**Entity in XML federated configuration**:

* Type: `LdapDirectory` (LDAP Connection)
* Name: `api-env LDAP`
* Environmentalized fields: `url`, `userName`, `password`
* Contained in `LDAP Connections`

**YAML configuration after conversion**:

```yaml
---
type: LdapDirectory
fields:
  name: api-env LDAP
  url: {{ LDAP_Connection.api_env_LDAP.url }}
  userName: {{ LDAP_Connection.api_env_LDAP.userName }}
  password: {{ LDAP_Connection.api_env_LDAP.password }}
```

**Content of values.yaml**:

```yaml
LDAP_Connection:
  api_env_LDAP:
    url: ldap://api-env:389
    userName: cn=Administrator,dc=demo.axway,dc=com
    password: KLJH95dHS7djshkjas54sa45s4d==
```

The following is an example for an entity with an environmentalized certificate:

**Entity in XML federated configuration**:

* Type: `ConnectToURLFilter`
* Name: `name: Connect to URL`
* Environmentalized Fields: `sslUsers`
* Contained in `Policies/YAML Demo/Connect with SSL`

**YAML configuration after conversion**:

```yaml
---
type: FilterCircuit
fields:
  name: Connect with SSL
  start: ./Connect to URL
  description: ""
children:
- type: ConnectToURLFilter
  fields:
    name: Connect to URL
    sslUsers: '{{ YAML_Demo.Connect_with_SSL.Connect_to_URL.sslUsers }}'
    url: https://localhost:5555/cert-verifier
    name: Connect to URL
# ...
```

**Content of values.yaml**:

```yaml
---
YAML_Demo:
  Connect_with_SSL:
    Connect_to_URL:
      sslUsers: /Environment Configuration/Certificate Store/O=DEV,CN=localhost
```