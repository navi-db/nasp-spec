# Navi Serialization Protocol (NASP)

The Navi (pronounced NAH-vee) Serialization Protocol, or NASP, is the wire protocol used to communicate with Navi key-value database servers. Inspired by the [Redis Serialization Protocol (RESP)](https://redis.io/docs/latest/develop/reference/protocol-spec/), NASP builds upon RESP's strengths while removing ambiguity between **requests** and **responses**.

NASP is designed to be:

- 🧠 **Unambiguous** – Clear separation between requests and responses
- 🔁 **Traceable** – Every response explicitly includes the request ID it corresponds to
- ⚡️ **Simple to implement**
- 🚀 **Fast to parse**
- 👀 **Human-readable**

> [!NOTE]
> The last three goals are also shared by RESP. NASP aims to match or exceed RESP in simplicity, performance, and clarity.

NASP supports serialization of:

- Integers
- Floats
- Strings (length-prefixed)
- Arrays (nested or flat)

## Why NASP?

While RESP is powerful and easy to implement, it lacks some protocol-level metadata such as request tracking and explicit message types. NASP introduces:

- `REQ` / `RES` headers for clear directionality
- Request IDs for asynchronous/multiplexed communication
- Optional message types (e.g. `COMMAND`, `PING`, `AUTH`)

## Message Format

NASP messages consist of multiple lines, each terminated by `\r\n` (the CRLF terminator). The lines follow this structure:

1. **Message type:** e.g., `REQ` or `RES`
2. **Request ID:** an integer identifying the request being sent or responded to
3. **Payload or status:** the actual command, status, or data associated with the message

### Example response:

```
RES\r\n
1\r\n
PONG\r\n
```

This indicates a response (`RES`) to request ID `1` with status `PONG`. This type of response is commonly returned for a `PING` request, though other commands may expect an `OK` response.

## Examples

Below are examples of NASP requests and responses using the `COMMAND` message type.

### Example: `SET apple banana`

**Client sends:**

```
REQ\r\n
1\r\n
COMMAND\r\n
*3\r\n
$3\r\n
SET\r\n
$5\r\n
apple\r\n
$6\r\n
banana\r\n
```

**Server replies:**

```
RES\r\n
1\r\n
OK\r\n
```

---

### Example: `GET apple`

**Client sends:**

```
REQ\r\n
2\r\n
COMMAND\r\n
*2\r\n
$3\r\n
GET\r\n
$5\r\n
apple\r\n
```

**Server replies:**

```
RES\r\n
2\r\n
VALUE\r\n
$6\r\n
banana\r\n
```

---

### Example: `PING` request

**Client sends:**

```
REQ\r\n
3\r\n
PING\r\n
```

**Server replies:**

```
RES\r\n
3\r\n
PONG\r\n
```

---

> Each `REQ` and `RES` message begins with a header line, followed by an optional payload.
> (Bulk) Strings are encoded as length-prefixed using the format: `$<length>\r\n<value>\r\n`

## Serialization Formats

NASP serializes data using a length-prefixed, line-delimited format inspired by RESP. Some of the supported data types are:

- Simple strings
- Simple errors
- Integers
- Floats
- Bulk strings
- Bulk errors
- Arrays
- Tagged Strings (Explicitly encoded strings)

Future versions may add support for booleans, nulls, and raw binary blobs to enhance protocol expressiveness.

---

## Status

🚧 **This protocol is under active design and may change.**

---
