# Tagged Strings

Tagged strings are strings with an explicit encoding or format tag.
They are intended for transmitting values where the content type must be known to the receiver, such as plain text, JSON, XML, or other encoded formats.

The tagged strings have a memory limit payload of **128 kilo-bytes**, which is enough for large and complex typed data.

### Tagged String Structure

Tagged strings must follow the following:

- must begin with the equals (`=`) prefix
- must then be followed by the length of the string (including the tag and a separating colon), encoded in base-10, followed by a CRLF (`\r\n`) terminator
- the data must begin with the encoding or format tag, followed by a colon (`:`), then the string content
- the encoding or format tag must be exactly 3 characters long and consist of ASCII letters and/or digits
- must end with the CRLF (`\r\n`) terminator
- must not exceed 128 kilo-bytes total (including the `=`, length, tag, colon, content, and CRLF characters)
- the format tag and content must use ASCII encoding unless the tag specifies an alternate encoding understood by the application

### Recommended Format Tags

The following 3-character tags are recommended for common formats:

| Tag     | Meaning                       |
| ------- | ----------------------------- |
| **txt** | Plain ASCII/UTF-8 text        |
| **bin** | Binary data (arbitrary bytes) |
| **jsn** | JSON text                     |
| **xml** | XML text                      |
| **htm** | HTML text                     |
| **csv** | Comma-separated values        |
| **b64** | Base64-encoded data           |
| **hex** | Hexadecimal-encoded data      |
| **mkd** | Markdown text                 |

Other tags may be defined by applications using NASP, but it is recommended to keep them unique and documented.

### Examples

A tagged string containing ASCII text:

```
=9\r\ntxt:Hello\r\n
```

A tagged string containing JSON data:

```
=11\r\njsn:{"a":1}\r\n
```

A tagged string containing HTML:

```
=16\r\nhtm:<p>Hello</p>\r\n
```

A tagged string containing binary data (hex-encoded):

```
=14\r\nhex:48656C6C6F\r\n
```

NASP encodes tagged strings as follows:

```
=<length>\r\n<tag>:<data>\r\n
```

> [!NOTE]
> The `<length>` is the total number of bytes in`<tag>:<data>`, essentially it will always be the length of the data + 3 bytes.
