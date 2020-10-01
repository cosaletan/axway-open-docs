{
"title": "Edit the YAML configuration",
"linkTitle": "Edit the YAML configuration",
"weight":"70",
"date": "2020-09-24",
"description": "Learn how to edit the YAML configuration."
}

The file explorer on your operating system can be used to navigate the user friendly directory structure of a YAML configuration. Any standard text editor can be used to view and edit the individual YAML files.

Ideally, a standard IDE can be used to create a project and edit the files of a YAML configuration. The externalized files for scripts, JSON, etc allow you to edit these files in an editor that is syntax aware.

## Use ES Explorer to edit a YAML configuration

[ES Explorer](/docs/apigtw_devguide/entity_store/#use-the-es-explorer) can be used with some limitations to view, navigate, and edit a YAML configuration. To connect to a YAML configuration, enter a URL of the form `yaml:file:/root/dir/of/yaml`.

## Create a new policy

Policy Studio is not supported for YAML configuration as yet. Making minor changes to an existing policy or other configuration using an IDE of your choice should be straightforward using an IDE with YAML syntax checking. The `yamles` validate option should also be used to check the validity of the configuration.

Building a new complex policy without an existing YAML format example may prove challenging. One way to simplify this level of development would be to use the ES Explorer tool.

Otherwise you could still:

* Use Policy Studio and develop the structure of the policy.
* Convert that XML federated configuration to YAML in another directory using `yamles fed2yaml` command.
* Copy the policy's YAML file from that conversion into your main YAML project.

It is recommended to develop that new complex policy in an XML project that contains all the configuration you previously converted to YAML, so that you can reference other existing configuration. You will need to ensure all referenced entities that need to be resolvable can be resolved. This might mean copying other files into the YAML directory structure.

## Add a new certificate and private key to a YAML configuration

This section covers the steps required to add a new certificate and private key to an existing YAML configuration. Note that certificates and private keys that exist in the XML federated configuration, which then get converted to YAML format will be formatted as required with no additional steps required. In some cases you might only need to add a new certificate to the YAML configuration, in this case the steps for the private key can be ignored.

You must have a certificate in a `PEM` file and its related private key in separate `DER` file, for example, `Axway-cert.pem` and `Axway-key.der`. The private key should be unencrypted.

For testing purposes you can generate an unencrypted private key and self-signed certificate PEM files as follows using openssl:

```
> openssl req -nodes -new -x509 -keyout Axway-key.pem -out Axway-cert.pem
Generating a RSA private key
................+++++
...............................................................................+++++
writing new private key to 'Axway-key.pem'
-----
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [AU]:IE
State or Province Name (full name) [Some-State]:Dublin
Locality Name (eg, city) []:
Organization Name (eg, company) [Internet Widgits Pty Ltd]:Axway
Organizational Unit Name (eg, section) []:
Common Name (e.g. server FQDN or YOUR name) []:Axway
Email Address []:axway@axway.com
```

Convert the `Axway-key.pem` to an `Axway-key.der` as follows:

```
openssl pkcs8 -topk8 -inform PEM -outform DER -in Axway-key.pem -out Axway-key.der -nocrypt
```

Copy the `Axway-cert.pem` and `Axway-key.der` files into the `/Environment Configuration/Certificate Store` directory in your YAML entity store.

The `Axway-cert.pem` file needs a minor modification to allow it to be loaded by the YAML entity store. Edit the `Axway-cert.pem` file so that the header and footer lines are removed and only the base64 content remains. Remove the following lines from `/Environment Configuration/Certificate Store/Axway-cert.pem`:

```
-----BEGIN CERTIFICATE-----
-----END CERTIFICATE-----
```

so that the file content looks like this:

```
MIIDnTCCAoWgAwIBAgIUW3ChbRS4eInQE0rgqp3YkBHZBXIwDQYJKoZIhvcNAQEL
BQAwXjELMAkGA1UEBhMCSUUxDzANBgNVBAgMBkR1YmxpbjEOMAwGA1UECgwFQXh3
YXkxDjAMBgNVBAMMBUF4d2F5MR4wHAYJKoZIhvcNAQkBFg9heHdheUBheHdheS5j
b20wHhcNMjAwOTE2MDk1MTM2WhcNMjAxMDE2MDk1MTM2WjBeMQswCQYDVQQGEwJJ
RTEPMA0GA1UECAwGRHVibGluMQ4wDAYDVQQKDAVBeHdheTEOMAwGA1UEAwwFQXh3
YXkxHjAcBgkqhkiG9w0BCQEWD2F4d2F5QGF4d2F5LmNvbTCCASIwDQYJKoZIhvcN
AQEBBQADggEPADCCAQoCggEBAL0l1qotIi2GdkEpevbNtoy3oDKozzz6n9aN2eae
aaqxqNtXCx2n0upFh4Ey3DWPd72HwKG0pvK39cZNpfos1Rb15l173slx8zwfMNQ8
NVvjOt3OIuGjPL+Pjb/ihG+dLGcKI/GTG7E/DsVgUhQ2Cu/rGVfdrwcvmhbUdLjq
uZNlovbnCvJcSUmsQUIbWBEtI4bTmCK4ma0UxMi5rExNOM+HQscNkr75RqgkgB5U
QSrEXyh8UzRz7oSqpaKcWtYQzbw5OhMq8WhL2+Zj2s8M2I0vzvlqi2dbv/gc3eWy
/7MC1ZuZikO5EBbmeoMB57GBnS8UexjJtcZ373t9IdWqjEUCAwEAAaNTMFEwHQYD
VR0OBBYEFCZYaBqwqQtHTcPNJqsRkQs2d7A4MB8GA1UdIwQYMBaAFCZYaBqwqQtH
TcPNJqsRkQs2d7A4MA8GA1UdEwEB/wQFMAMBAf8wDQYJKoZIhvcNAQELBQADggEB
AFQStTP3jB6Y2hKLxsSvtESqgsGOUUWwhqrXu5ee8ff1ZuiRhgkFhkQ9PF8gkJ81
G4BdWP2V8c23OPCNSi0AMjyXKxeH8uqgvfByI9fxKNpQlhM/bgHm77KDBjymPTiF
YNDhF1B120AZxLlO9GqVwjSpNSipswjNZezVoOQZLuO9FYQG2l2VhwY6/OAfLwAv
nZK3sWVKAPb7yXgahYLkHvpTc3YkQIBAvApkSOYUmvoaosf9nvBIYUizVajdGNuN
paRQIpiNy+JF67qBlJWsvdipyMHYyWyyaD/llI1X+QRLhbCiTsTXuanVFuxNEIBz
73nMdCLFLS8zBgflu6bajI4=
```

Create the following `/Environment Configuration/Certificate Store/Axway.yaml` file in your YAML entity store:

```yaml
---
type: Certificate
fields:
  dname: Axway
  key#refbinary: Axway-key.der
  content#ref: Axway-cert.pem
```

Edit another entity that requires a certificate and private key, for example an `XML Signature filter` (see the `signingCert` field below). It now points to the new certificate and private key via its YamlPK `/Environment Configuration/Certificate Store/Axway`.

```yaml
---
type: FilterCircuit
fields:
  name: cert
  start: ./XML Signature Generation
children:
- type: GenerateSignatureFilter
  fields:
    signingCertAttribute: ""
    keyWrapAlgorithm: http://www.w3.org/2001/04/xmlenc#rsa-oaep-mgf1p
    symmetricKeyEncryptionCertAttribute: ""
    name: XML Signature Generation
    signingCert: /Environment Configuration/Certificate Store/Axway   # <-- certificate ref
    symmetricKeyAttribute: ""
    wsuIdElementSpecification:
    - /System/Element Specifiers/http://schemas.xmlsoap.org/soap/envelope/,Body,1
    - /System/Element Specifiers/http://www.w3.org/2003/05/soap-envelope,Body,1
    attachmentTransform: ""
  routing:
    success: ../XML Signature Verification
  logging:
    fatal: 'Error during message signing. Error: ${circuit.exception}'
    failure: Failed to sign message
    success: Signed message successfully
  children:
  - type: KeyInfoFormat
    fields:
      keyNameValue: CN
      publicKeyInfoMask: 15
      keyNameType: 2
      certAttachmentId: ""
```

If the YAML configuration you are adding the certificate and private key into is encrypted with a passphrase, you will need to encrypt the private key file. To encrypt the private key file `Axway-key.der`, follow all the steps above and complete the procedure with the following:

```shell
cd apigateway/posix/bin
./yamles encrypt --file ~/yaml/Environment\ Configuration/Certificate\ Store/Axway-key.der --passphrase changeme
The file `/home/user/yaml/Environment Configuration/Certificate Store/Axway-key.der` has been encrypted
```

When edits are complete on the YAML configuration, you must create a `.tar.gz` file and deploy it to your running API Gateway.

{{< alert title="Note">}}Encrypting a private key via openssl and adding it to the YAML configuration is not supported. This must be done before packaging the `.tar.gz` and deploying to a gateway group that has a matching passphrase.{{< /alert >}}