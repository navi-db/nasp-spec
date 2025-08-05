# Navi Serialization Protocol (NASP)

**NASP (Navi Serialization Protocol)** is the wire protocol used to communicate with Navi key-value database servers. Inspired by the [Redis Serialization Protocol (RESP)](https://redis.io/docs/latest/develop/reference/protocol-spec/), NASP builds upon RESP's strengths while removing ambiguity between **requests** and **responses**.

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
OK\r\n
```

This indicates a response (`RES`) to request ID `1` with status `OK`. This type of response is commonly returned for a `PING` request, though other commands may also expect an `OK` response.

## Examples

Below are examples of NASP requests and responses using the `COMMAND` message type.

### Example: `SET apple banana`

**Client sends:**
```
REQ\r\n
1\r\n
COMMAND\r\n
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
OK\r\n
```

---

> Each `REQ` and `RES` message begins with a header line, followed by an optional payload.
> (Bulk) Strings are encoded as length-prefixed using the format: `$<length>\r\n<value>\r\n`

## Request IDs

Each NASP request contains a client-generated `request_id` that uniquely identifies the request within that connection. The server’s responsibility is to echo this `request_id` back in the corresponding response, enabling clients to match replies to their requests.

By default, the server does **not** enforce any specific ordering or sequencing of request IDs. This allows clients to send multiple concurrent or pipelined requests without waiting for previous responses. Clients must ensure their request IDs are unique for outstanding requests and may reuse IDs only after receiving the corresponding response.

For increased safety, debugging and in production environments, servers can optionally enable a strict mode to enforce sequential ordering of request IDs and reject out-of-order or duplicate IDs. This feature helps detect and prevent client misbehavior but is not required by the core protocol and may be disabled to maximize performance and throughput.

## Default Port

By convention, NASP servers listen on **TCP port 6380** by default, which is chosen close to Redis's 6379 for familiarity but distinct to avoid conflicts. This default port can be customized during server configuration to fit deployment environments.

Clients connecting to a NASP server should specify the appropriate host and port, with 6380 being the default unless otherwise configured.

## Serialization Formats

NASP serializes data using a length-prefixed, line-delimited format inspired by RESP. Some of the supported data types are:

- **Integers:** Base-10 encoded, prefixed with a colon (`:`) followed by an optional explicit sign (`+` or `-`)  
  Examples: `:0\r\n` or `:-5\r\n`

- **Floats:** Decimal floating-point numbers encoded similarly to integers but prefixed with a semicolon (`;`)  
  Examples: `;0.0\r\n` or `;-3.14\r\n`

- **Strings:** UTF-8 encoded strings, either as simple strings prefixed with a plus sign (`+`) or bulk strings prefixed with a dollar sign (`$`)  
  Examples: `+OK\r\n` or `$4\r\nHello\r\n`

> [!WARNING]
> Simple strings cannot contain **either** the CR (\r) **or** LF (\n) characters

- **Arrays:** Collections of elements, each recursively encoded in NASP format. Arrays are prefixed with an asterisk (`*`) and length-prefixed as well. Arrays can be nested to represent complex commands and structured data efficiently.

---

Future versions may add support for booleans, nulls, and raw binary blobs to enhance protocol expressiveness.

## Status
🚧 **This protocol is under active design and may change.**

---
