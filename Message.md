Messages in MRPC refers to the JSON object that ultimately gets string encoded and sent in a UDP packet. These messages get generated when an RPC request is made, and also when the remote node is replying with a response.

There three fields that all messages must contain in order to be a valid MRPC message.

* **id**
  * Incremented integer uniquely identifying a message sent from a specific node
* **src**
  * 32 character hex UUID of the sending node
* **dst**
  * [Path](/Path.md) for the destination of message

### Request
When invoking an RPC call, a request message is generated containing the required message fields, and optionally some others. All extra fields in a request are optional, so a message containing only the required message fields is also a valid request.

* **value**
  * Represents the value with which to call the remote procedure, can be any type

### Response
Nodes will typically respond to RPC requests by sending a response message. Response messages are required to have exactly one of the following two fields.

* **result**
  * Represents the return value of the RPC, can be any type
* **error**
  * Represents an error encountered during the RPC call, typically a string message
