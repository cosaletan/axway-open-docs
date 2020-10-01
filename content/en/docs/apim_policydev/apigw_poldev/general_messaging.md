{
"title": "Configure messaging services",
"linkTitle": "Configure messaging services",
"weight": 8,
"date": "2019-10-17",
"description": "Configure API Gateway to work with Apache ActiveMQ, IBM WebSphere MQ, and other messaging systems that support the JMS standard."
}

A *messaging system* is a loosely coupled, peer-to-peer facility where clients can send messages to, and receive messages from any other client. In a messaging system, a client sends a message to a messaging agent. The recipient of the message can then connect to the same agent and read the message. However, the sender and recipient of the message do not need to be available at the same time to communicate (for example, unlike HTTP). The sender and recipient need only know the name and address of the messaging agent to talk to.

Java Message Service (JMS) is an implementation of such a messaging system. It provides an API for creating, sending, receiving, and reading messages. Java-based applications can use it to connect to other messaging system implementations. A *JMS provider* can deliver messages synchronously or asynchronously, which means that the client can fire and forget messages or wait for a response before resuming processing. Furthermore, the JMS API ensures different levels of reliability in terms of message delivery. For example, it can ensure that the message is delivered once and only once, or at least once.

API Gateway uses the JMS API to connect to other messaging systems that expose a JMS interface (for example, Oracle WebLogic Server, IBM MQ, Red Hat JBoss Messaging, Apache ActiveMQ, or Progress SonicMQ). As a consumer of a JMS queue or topic, the API Gateway can read XML messages and pass them into a policy for validation. These messages can then be routed on over HTTP or dropped on to another JMS queue or topic.

API Gateway also provides a default embedded Apache ActiveMQ service, which enables it to act as a JMS server. For example, this enables API Gateway to integrate external facing REST APIs and SOAP Web services with back-end systems and applications using reliable asynchronous messaging.

You can use the **JMS Wizard** to configure an API Gateway instance to consume JMS messages from a JMS queue or topic. When a message has been consumed by the API Gateway, it can be sent to a configured policy where the full range of API Gateway message filters can run on the message. The message can then be routed onwards over HTTP or dropped back on to a JMS queue or topic. The **JMS Wizard** guides you through all of the necessary steps to configure messaging (for example, the JMS service, JMS session, and JMS consumer).

Alternatively, you can configure a global JMS service under the **Environment Configuration > External Connections** node in Policy Studio by right-clicking the **JMS Services** node, and selecting **Add a JMS Service**. The configured global JMS services can then be used by the API Gateway to drop messages on to a JMS queue or topic, or to read messages from a JMS queue or topic (for example, using the **Send to JMS** or **Read from JMS** filter).

## Prerequisites

API Gateway provides all the required third-party JAR files for IBM WebSphere MQ and Apache ActiveMQ (both embedded and external).

For other third-party JMS providers only, you must add the required third-party JAR files to the API Gateway classpath for messaging to function correctly. If the provider's implementation is platform-specific, copy the provider JAR files to `INSTALL_DIR/ext/PLATFORM`.

`INSTALL_DIR` is your API Gateway installation, and `PLATFORM` is the platform on which API Gateway is installed (`Linux.x86_64`). If the provider implementation is platform-independent, copy the JAR files to `INSTALL_DIR/ext/lib`.

## Configure a JMS service

You can configure a global JMS service under the **Environment Configuration** > **External Connections**
node in Policy Studio by right-clicking the **JMS Services** node, and selecting **Add a JMS Service**. The details entered in the **JMS Service** dialog can then be used by the API Gateway to drop messages on to a JMS queue or topic, or to read messages from a JMS queue or topic using the **Send to JMS** and **Read from JMS** filters.

Alternatively, you can configure a JMS service at the API Gateway instance level, and configure the API Gateway to consume a JMS queue or topic. Right-click the instance under the **Environment Configuration** > **Listeners**
node in the Policy Studio, and select **JMS Wizard**.

### General JMS Service settings

Configure the following fields on the **JMS Service**
tab:

**Name**: Enter a descriptive name for the JMS provider in the **Name**
field.

**Service type**: Select one of the following from the list:

* **Embedded Apache ActiveMQ**: The default Apache ActiveMQ service that is embedded in the API Gateway.
* **Apache ActiveMQ**: An external Apache ActiveMQ service that is not embedded in the API Gateway.
* **IBM MQ**: An IBM WebSphere MQ service.
* **Standard JMS**: Other systems that support the JMS standard (for example, Oracle WebLogic Server, IBM MQSeries, JBoss Messaging, or Progress SonicMQ).

### Apache ActiveMQ and Standard JMS settings

The following settings are displayed when you select a **Service Type**
of Embedded Apache ActiveMQ, Apache ActiveMQ, or Standard JMS:

**Provider URL**: Enter the URL of the JMS provider. For example, a URL for a JBoss application server might be `jnp://localhost:1099`. Defaults to `local` for Embedded Apache ActiveMQ.

**Initial Context Factory**: API Gateway uses a connection factory to create a connection with a JMS provider. A connection factory encapsulates a set of connection configuration parameters that have been defined by the administrator. The following are some example default values:

* Embedded Apache ActiveMQ: `com.vordel.ama.jndi.InitialContextFactory`
* External Apache ActiveMQ: `com.vordel.jms.apache.activemq.InitialContextFactory`
* JBoss application server: `org.jnp.interfaces.NamingContextFactory`

**Connection Factory**: Enter the name of the connection factory to use when connecting to the JMS provider. The name of the connection factory is vendor-specific. For example, the connection factory for the JBoss application server is `org.jnp.interfaces:javax.jnp`. Defaults to `connectionFactory`
for embedded and external ActiveMQ.

{{< alert title="Note" color="primary" >}}Since version 5.15.6 of **Apache ActiveMQ JMS API**, client connections using SSL are checking that the server certificate common name (CN) matches the host name provided in the connection URL. You can disable this feature by setting the `socket.verifyHostName` parameter to `false` in the provider URL. For example, `ssl://activemq.server.name:61616?socket.verifyHostName=false`.{{< /alert >}}

### IBM WebSphere MQ settings

The following settings are displayed when you select a **Service Type**
of **IBM MQ**:

**Host name**: Enter the host name of the JMS provider (for example, `localhost`).

**Port number**: Enter the port number of the JMS provider (for example, `1414`).

**Queue manager**: Enter the virtual queue manager name by which IBM WebSphere Application Server is known to WebSphere MQ (for example, `TEST_BUS`).

**Channel**: Enter the IBM WebSphere MQ channel name on the WebSphere MQ system (for example, `MY_QM.TO.TEST_BUS`).

**Initial Context Factory**: The API Gateway uses a connection factory to create a connection with a JMS provider. A connection factory encapsulates a set of connection configuration parameters that have been defined by the administrator. Defaults to `com.vordel.jms.ibm.mq.InitialContextFactory`.

**Connection Factory**: Enter the name of the connection factory to use when connecting to the JMS provider. Defaults to `connectionFactory`.

### Settings for all service types

The following optional settings are common to all service types:

**Username**: If a user name is required to connect to this JMS provider, enter it here.

**Password**: Enter the password for this user.

**Custom Message Properties**: You can add JNDI context settings by clicking **Add**, and entering name and value properties in the fields.

For the **Embedded Apache ActiveMQ**
service type, you can define Apache ActiveMQ URI parameters using JNDI properties. For example, see the following:

* <http://activemq.apache.org/tcp-transport-reference.html>
* <http://activemq.apache.org/connection-configuration-uri.html>
* <http://activemq.apache.org/redelivery-policy.html>
* <http://activemq.apache.org/ssl-transport-reference.html>
* <http://activemq.apache.org/what-is-the-prefetch-limit-for.html>

### Configure advanced settings

You can configure the following options on the **Advanced Settings**
tab:

#### JMS service settings

The advanced JMS service settings are as follows:

**JMS Client ID**: Enter the client ID required by JMS durable topic subscriptions to consume messages from the service.

**Max sessions for JMS filters**: Enter the maximum number of sessions that are created for the JMS filters (**Send To JMS** and **Read From JMS**) using this JMS service. The default value is 20.

**Automatic reconnection**: Select whether a reconnection to the JMS server is performed when the configured JMS provider raises a connection error. This setting is selected by default.

**Start first connection asynchronously**: Select whether the first connection attempt is detached from the API Gateway startup sequence. When this setting is selected, API Gateway starts even if the JMS connection cannot be established.

#### SSL settings

{{< alert title="Note" color="primary" >}}SSL settings are available only for the **IBM MQ** and external **Apache ActiveMQ** JMS service types.{{< /alert >}}

You can configure the following SSL settings:

**Cipher suite**: Click the browse button on the right, and select SSL cipher suites from the list of JSSE or IBM cipher suites in the dialog (for example, **SSL_RSA_WITH_RC4_128_MD5**).

{{< alert title="Note" color="primary" >}}When using an **IBM MQ** JMS service type, you can select only one SSL cipher suite. For more details, see your IBM WebSphere MQ documentation. When using an **Apache ActiveMQ** JMS service type, you can select multiple cipher suites.{{< /alert >}}

**Trusted certificates**: When a cipher suite is selected, you can select SSL trusted certificates and authorities from the list. The selected certificates are used to check the JMS server certificate.

**Client certificate (SSL mutual authentication)**: Click **Client Certificate**
to select the SSL client certificate and key to use. This setting is required only for SSL mutual authentication.

### Next steps

When the JMS service has been configured, you can configure the API Gateway to drop messages on to a queue or topic exposed by this service. You can do this when configuring a policy by selecting the service in the **Send to JMS**
or **Read from JMS** filters.

You can also configure JMS sessions for the newly added JMS service at the API Gateway instance level. For more details, see the next section.

## Configure a JMS session

JMS services have JMS sessions, which can be shared by multiple JMS consumers, or used by a single JMS consumer only. To configure a JMS session, right-click an API Gateway instance under the **Environment Configuration** > **Listeners**
node in the Policy Studio tree, and select **Messaging System** > **Add JMS Session**. Alternatively, you can configure a JMS session using **Messaging System** > **JMS Wizard**.

{{< alert title="Note" color="primary" >}}You must have first configured a JMS service before you can configure a JMS session.{{< /alert >}}

### JMS session configuration

The JMS session settings that are displayed on the **Session**
tab depend on whether you selected **Add JMS Session**
or **JMS Wizard**.

#### Add JMS session only

If you selected **Messaging System** > **Add JMS Session**, configure the following fields:

**JMS service**: Click the browse button on the right, and select a preconfigured JMS service. To add a service, right-click **JMS Services**, and select **Add a JMS Service**.

**Listener Count**: Specify the number of listeners permitted for this JMS session. Defaults to 1. If the volume of messages arriving at the queue is more than a single thread can process, you can increase the number of threads listening on the queue by increasing the listener count.

#### Common configuration

In both cases (**Add JMS Session** and **JMS Wizard**), configure the following fields:

**Remove message from source**: Select one of the following options from the list:

* **Immediately when message is read**: Message is removed immediately after it is read.
* **Lazily which will allow for duplicate message**: Message is removed lazily, which allows possible duplicate messages and compatibility with previous versions of API Gateway.
* **When policy completes without error**: Message is removed if the configured policy completes, either with a succeess or failure. If the configured policy does not complete due to an error, the message is not removed. This option allows possible duplicate messages and compatibility with previous versions of API Gateway. This is the default option.
* **When policy completes and property below evaluates to true**: Message is removed if the message attribute configured in **Message removal property** evaluates to `true`. This attribute is set to `true` by default.

After the configured policy executes, if a message is not removed, it is then rolled back. You may need to configure an error path for the messages to prevent a poison message loop.

**Message removal property**: Enter the message attribute name used by the **When policy completes and selector below evaluates to true** option.

### Monitoring options

The **Traffic Monitor** tab enables you to configure traffic monitoring settings for the JMS session. To override the system-level traffic monitoring settings, select **Override system-level settings**, and configure the relevant options.

## Configure a JMS consumer

You can configure multiple JMS consumers under a single JMS session, which share that session. Alternatively, you can configure a single JMS consumer per JMS session. Consumers sharing a JMS session access that session serially. Each consumer blocks until a response (if any) is received. Consumers with their own session do not encounter this problem, which may improve performance.

You can configure JMS consumers using **Messaging System > JMS Wizard**, or by right-clicking an existing JMS session, and selecting **Add JMS Consumer**.

{{< alert title="Note" color="primary" >}}You must first configure a JMS service and a JMS session before you can configure a JMS consumer.{{< /alert >}}

### JMS Message source

On the **General**
tab, configure the following fields in the **Message source**
section:

**Source type**: Select one of the following from the list:

* **Queue**
* **Topic**
* **JNDI lookup**

Defaults to **Queue**.

**Source Name**: Enter the name of the JMS queue, JMS topic, or JNDI lookup to specify where you want read the messages from.

{{< alert title="Note" color="primary" >}}The source name to use depends on the configured **Source type**. For example, for IBM WebSphere MQ, this name may need to use a format of `queue://`. For details on source name requirements, please see the user documentation for your third-party JMS provider tool.{{< /alert >}}

**Selector**: Enter an SQL selector expression that specifies a response message. The expression entered specifies the messages that the consumer is interested in receiving. By using a selector, the task of filtering the messages is performed by the JMS provider instead of by the consumer.

The selector is a string that specifies an expression whose syntax is based on the SQL92 conditional expression syntax. The API Gateway instance only receives messages whose headers and properties match the selector. For example, the following expression gets the selected message from the queue:

```
JMSCorrelationID='${params.query.id}'
```

### JMS consumer type

The **JMS consumer type**
settings enable you to configure the following:

**Durable subscription**: Select this setting to use a durable topic subscription to consume messages from the server. This option is available only for **Topic**
and **JNDI lookup**
source types.

**Topic subscriber name**: Enter the JMS subscriber name used to identify the durable subscription.

{{< alert title="Note" color="primary" >}}The JMS service used must have a **JMS Client ID** configured. If a **JNDI lookup** source is configured, the name must not point to a topic. Only one durable subscriber (described by the JMS client ID and subscriber name) can be active at a time. {{< /alert >}}

### Message processing

The **Message processing**
settings include the following:

**Extraction Method**: Specify how to extract the data from the JMS message from the drop-down list:

* Create a `content.body` attribute based on the SOAP over JMS draft specification (the default)
* Insert the JMS message directly into the attribute named below
* Populate the attribute below with the value inferred from message type to Java

**Attribute Name**: The name of the API Gateway message attribute that holds the data extracted from the JMS message. Defaults to the `jms.message` message attribute.

**Policy**: Select the appropriate policy from the list to run on the JMS message after it has been consumed by the API Gateway. This setting is required.

**Send Response to Configured Destination**: Specifies whether the API Gateway sends a reply to the response queue named in the incoming message (in the `ReplyTo`
header). This option is selected by default. Deselecting this option means that the API Gateway never sends a reply to the response queue named in the `ReplyTo`
header.

### Logging settings

The **Logging Settings**
tab enables you to configure the logging level for all filters in policies executed on this JMS consumer, and to configure when message payloads are logged.

#### Transaction Audit Logging Level

You can configure the following settings on all filters executed on the JMS consumer:

| Logging level | Description                                                                                    |
|---------------|------------------------------------------------------------------------------------------------|
| **Fatal**     | Logs Fatal log points that occur on all filters executed.                                      |
| **Failure**   | Logs Failure log points that occur on all filters executed. This is the default logging level. |
| **Success**   | Logs Success log points that occur on all filters executed.                                    |

#### Transaction Audit Payload Logging

You can configure the following settings for this JMS consumer:

| Payload logging                            | Description   |
|--------------------------------------------|--------------------------------|
| **On receive request from client**         | Log the message payload when a request arrives from the client.|
| **On send response to client**             | Log the message payload before the response is sent back to the client.|
| **On send request to remote server**       | Log the message payload before the request is sent using any **Connection** or **Connect to URL** filters deployed in policies. |
| **On receive response from remote server** | Log the message payload when the response is received using any **Connection** or **Connect to URL** filters deployed in a policies. |

## Configure embedded Apache ActiveMQ in API Gateway settings

You can use the API Gateway server settings to configure the default embedded Apache ActiveMQ broker available in API Gateway, which enables it to act as a JMS service provider.

In the Policy Studio tree, select **Environment Configuration > Server Settings > Messaging > Embedded ActiveMQ**. For example, you can enable embedded ActiveMQ, and configure location and SSL security settings.

{{< alert title="Note" color="primary" >}}Apache ActiveMQ 5.14.3 restricts serializing object message types. For more details, see the [Embedded ActiveMQ settings](/docs/apim_reference/general_activemq_settings/).{{< /alert >}}

## Monitor messaging using API Gateway Manager

You can use the API Gateway Manager web console to monitor messaging at runtime. For example, you can create, delete, and view JMS topics, queues, and messages at runtime.
