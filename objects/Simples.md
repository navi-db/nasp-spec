# Simples

The simple string and simple errors are the most basic and primative data type used by NASP.

The simples have a memory limit payload of **515 bytes** which is enough for small and simple strings or error messages.

### Simple Strings

Simple strings are intented for short responses to a request.
For example, an OK response to the PING command

Simple strings must follow the following:

- must begin with the plus (`+`) prefix
- must end with the CRLF (`\r\n`) terminator
- must not contain either the CR (`\r`) or LF (`\n`) characters within the payload
- must not exceed 512 characters or 512 bytes (excluding the `+` and CRLF charcters)
- must use ASCII encoding

For example:

```
+OK\r\n
```

NASP encodes simple strings as followed:

```
+<data>\r\n
```

### Simple Errors

Simples errors, like simple strings, are intented for short error responses with a prefixed error type.
For example, an ERR response

Simple errors must follow the following:

- must begin with the minus (`-`) prefix
- must end with the CRLF (`\r\n`) terminator
- must not contain either the CR (`\r`) or LF (`\n`) characters within the payload
- must not exceed 512 characters or 512 bytes (excluding the - and CRLF charcters)
- must use ASCII encoding
- must be prefixed (after the minus) with an error type, then optionally followed by a space and a description

#### Error types

- ERR - Generic error
- RPREFIX - REQ or RES prefix missing from a request or response
- UNKNOWN - Unknown request or response format
- WRONGTYPE - Operation against a value with the wrong type
- NOAUTH - Authentication required
- NOPERM - Permission denied
- READONLY - Operation against a read-only server

For example:

```
-ERR Unknown command\r\n
```

```
-READONLY Unable to perform a write command in a replica, read-only server\r\n
```

NASP encodes simple errors as followed:

```
-<data>\r\n
```
