# Basic Chat Protocol

The Basic Chat Protocol (BCP) is a centralized protocol that allows users to join a chat server and send messages to other users on that server. The goal of the protocol is to be as easy to implement as possible while retaining good functionality.

## Transports

All BCP messages are sent through TCP connections. Port 8018 should be used when connection to a BCP server unless the user specifies otherwise.

## Basic Framing

Every BCP message is preceded by a positive integer 0 < N < 2^15 encoded as a UTF-8 string, followed by a single newline character, then N UTF-8 characters that make up a valid JSON document, followed finally by a single newline character.

Messages are framed this way to make them simple to parse, and for traffic to be easily inspectable by humans.

For example...

```json
44
{"action":"register","name":"john","rid":10}
```

```json
29
{"action": "quit", "rid": 19}
```

## Requests and Responses

There are two types of messages: requests and responses.

### Requests

Requests always have an *action* value that is a string corresponding to the type of request it is (a list of possible requests is available later in this specification). The request may also have additional key-value pairs with additional information required to complete the action.

All requests must have an *rid* (response ID) that is reasonably unique number within the server's and client's conversation. The *rid* should be computed by taking the last request's *rid*, adding 1, and modding by 2^10 if you are the client. The server should do that and then add 2^10. The client's first *rid* of a conversation should be 0, and the server's should be 2^10.

### Responses

Responses always have a *success* value that is a boolean value signifying whether the action was performed succesfully or not. An optional *message* value can provide more information in either case.

A response always has an *rid* value that is equal to the *rid* of the request it is a response to.

### Examples

```json
43
{"action":"register","name":"john","rid":0}
24
{"success":true,"rid":0}
46
{"action":"post","message":"Hi guys.","rid":1}
24
{"success":true,"rid":1}
```

```json
63
{"action":"post","message":"Hi guys.","from":"john","rid":1024}
27
{"success":true,"rid":1024}
```

