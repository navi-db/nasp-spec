# Bulks

The bulk strings are the most common data type used by NASP.

The bulks have a memory limit payload of **2 mega-bytes** which is enough for large and complex strings or error messages.

### Bulk Strings

Bulk strings are intented for potentially large responses.
For example, a value of a string key

Bulk strings must follow the following:

- must begin with the dollar (`$`) prefix
- must then follow by the length of the string followed by a CRLF (`\r\n`) terminator
- must end with the CRLF (`\r\n`) terminator
- must not exceed 2 mega-bytes (including the `$`, length and CRLF charcters)
- must use ASCII encoding

For example:

```
$4\r\napple\r\n
```

NASP encodes bulk strings as followed:

```
$<length>\r\n<data>\r\n
```

### Bulk Errors

Bulk errors, like bulk strings, are intented for large error responses with a prefixed error type.
For example, an ERR response

Bulk errors must follow the following:

- must begin with the exclamation mark (`!`) prefix
- must then follow by the length of the string followed by a CRLF (`\r\n`) terminator
- must end with the CRLF (`\r\n`) terminator
- must not exceed 2 mega-bytes (including the `!`, length and CRLF charcters)
- must use ASCII encoding
- must be prefixed (after the first CRLF) with an error type, then optionally followed by a space and a description

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
!19\r\nERR Unknown command\r\n
```

```
!73\r\nREADONLY Unable to perform a write command in a replica, read-only server\r\n
```

NASP encodes bulk errors as followed:

```
!<length>\r\n<data>\r\n
```
