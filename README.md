# MRPC
MRPC is a messaging framework for Remote Procedure Call applications that is friendly with mesh networks (Mesh RPC). This page describes the messaging framework and requirements of devices that wish to implement or support MRPC. For language specific documentation, use the following links:

* [Python](https://github.com/alex-sherman/python-mrpc#python-mrpc)
* [Java/Android](https://github.com/alex-sherman/android-mrpc)
* [ESP8266](https://github.com/alex-sherman/mrpc-esp8266#mrpc-for-the-esp8266)

# Nodes
An MRPC node is a communication end point that implements the MRPC framework. Typically there will exist one MRPC node per computing device, but it is also possible to have multiple nodes on a single device. Each node has some state associated with it as follows:

#### UUID: 36 characters (includes dashes)
  * Used to uniquely address this node
  
#### Request Number: unsigned 32 bit sequence number
  * Used to map incoming responses to previously sent requests
  
#### Aliases: list of strings
  * Used to name and group nodes
  * The first alias in the list is considered the "primary" alias. It is typically unique and used as a human readable name for the node.

Nodes communicate between each other by sending, receiving and sometimes forwarding messages as follows.

# Messages

Messages in MRPC refers to the JSON object that ultimately gets string encoded and broadcast to all nodes in the network. These messages get generated when an RPC request is made, and also when a remote node is replying with a response. Implementations of MRPC use UDP as a transport for messages, but MRPC's only requirement for message sending is that the protocol support an unreliable broadcast.

There are three fields that all messages must contain in order to be a valid MRPC message.

* **id**
  * The requesting node's incrementing request number
* **src**
  * 36 character UUID of the sending node
* **dst**
  * For requests, `dst` must be a Path
  * For responses, `dst` must be the requesting node's UUID

### Request
When invoking an RPC call, a request message is generated containing the required message fields, and an optional value field. If the value field is not included in an request, it shall be treated as `null` by any receiver. Requests may have multiple intended recipients described by the `dst` field's Path value.

* **value**
  * Represents the value with which to call the remote procedure, can be any type

### Response
Nodes will typically respond to RPC requests by sending a response message. Response messages are required to have exactly one of the following two fields. Responses must have a single intended recipient denoted by a UUID in the `dst` field.

* **result**
  * Represents the return value of the RPC, can be any type
* **error**
  * Represents an error encountered during the RPC call, typically a string message
  
# Path
Paths allow MRPC nodes to reference another node's or multiple nodes' services. Each path is a string structured as follows:

`<name>.<service>`

###Name
The name of a path specifies which node(s) should receive the request.
- 36 character UUID (single receiver)
- Wildcard '*' character (all nodes)
- An alias, any string up to 64 characters (single or group of receivers)

###Service
This part of the path specifies a service on the node(s) to invoke.
It can be any string up to 64 characters long.

###Path Matching
A node should respond to a request when all of the following conditions are met:
- The receiving node has a service with the same name as the Path's service
- The Path's name matches any of the following
  - The literal '*'
  - Any of the node's aliases
  - The node's UUID
