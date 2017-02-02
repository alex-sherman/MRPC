#Path
Paths allow MRPC nodes to address others. Each path is a string structured as follows:

`<name>.<service>`

###Name
The name of a path specifies which nodes should receive the request.
- 36 character hex UUID (with dashes)
- Wildcard '*' character
- Any other string up to 64 characters

###Service
This part of the path specifies a service on the node(s) to invoke.
It can be any string up to 64 characters long.
