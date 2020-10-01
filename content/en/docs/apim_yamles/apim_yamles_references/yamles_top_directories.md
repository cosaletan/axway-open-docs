{
"title": "YAML Entity Store Directory Mapping",
"linkTitle": "YAML Entity Store Directory Mapping",
"weight":"100",
"date": "2020-09-25",
"description": "Mapping between entity types and subdirectories."
}

During conversion, the YAML Entity Store sorts the entities directly under root entity through subdirectories depending on their types.

The Mapping is presented as such:

## Top level directory name

* Type 1 (direct children of Root entity)
* Type 2
* ...

## Policies

* `FilterCircuit`
* `CircuitContainer`

## APIs

* `WebServiceGroup`
* `WebServiceRepository`

## Resources

* `JSONSchemaGroup`
* `ScriptGroup`
* `StylesheetGroup`
* `XmlSchemaGroup`
* `XPathGroup`
* `ResourceRepository`

## Environment Configuration

* `KPSRoot`
* `EnvironmentalizedEntities`
* `Certificates`
* `PGPKeyPairs`
* `NetService`
* `UserStore`

## Libraries

* `CacheManager`
* `AlertManager`
* `RegularExpressionGroup`
* `OAuth2StoresGroup`
* `CronExpressionGroup`
* `CORSGroup`
* `WAFProfileGroup`

## Server Settings

* `AccessLogger`
* `CassandraSettings`
* `EventLog`
* `LogManager`
* `RealtimeMonitoring`
* `PortalCallbackGroup`
* `MimeTypeGroup`
* `OPDbConfig`
* `SystemSettings`
* `OpenTrafficEventLog`
* `PortalConfiguration`
* `ZeroDowntime`

## External Connections

* `AWSSettings`
* `AuthnRepositoryGroup`
* `ConnectionSetGroup`
* `AuthProfilesGroup`
* `DbConnectionGroup`
* `ICAPServerGroup`
* `JMSServiceGroup`
* `LdapDirectoryGroup`
* `ProxyServerGroup`
* `SentinelServerGroup`
* `SiteMinderConnectionSet`
* `SMTPServerGroup`
* `SyslogServerGroup`
* `RendezvousDaemonGroup`
* `UrlSetGroup`
* `XkmsConfigGroup`
* `RadiusClients`
* `SunAccessManagerSettings`
* `PassPortKeyStoreManager`
* `TivoliActionGroupGroup`
* `TivoliSettingsGroup`

## System

* Any other types (children of Root)