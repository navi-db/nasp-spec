# Messages

NASP messages are the fundamental units of communication between clients and servers.
Each message is a sequence of lines terminated by the CRLF (`\r\n`) sequence.

---

## Message Structure

Every NASP message consists of **at least three lines**:

1. **Message Type**
   Indicates the kind of message and its direction. Must be either:

   - `REQ` — Request from client to server
   - `RES` — Response from server to client

2. **Request ID**
   A positive integer uniquely identifying the request within the connection.
   For requests, this is client-generated; for responses, this echoes the corresponding request ID.

3. **Payload or Status**
   The actual command, status, or data associated with the message.
   Examples include:
   - Command types like `COMMAND`, `PING`, `AUTH`
   - Status strings like `OK`
   - Data types such as arrays or bulk strings encoded using NASP serialisation rules

---

## Simple Payloads Without Explicit Prefix

When a `REQ` or `RES` message **does not include a multi-line bulk string or array payload**, and the payload consists of a **single line** after the request ID, this line should be treated as a **simple string or simple error** without an explicit `+` prefix.

- These payload lines:
  - **Do NOT** begin with the `+` character.
  - Are limited to **512 bytes** maximum (excluding the CRLF).
  - Must **not** contain CR (`\r`) or LF (`\n`) characters.
  - Use ASCII encoding.
  - Can represent either a successful status (e.g., `OK`) or an error message (e.g., `ERR Unknown command`).

**Examples:**

```
RES\r\n
1\r\n
OK\r\n
```

```
RES\r\n
2\r\n
ERR Unknown command\r\n
```

This approach keeps simple responses concise and readable without extra prefix characters, while preserving the 512-byte size limit to prevent abuse.

---

## Message Format Example

### Request Example (`SET apple banana`):

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

- Message type: `REQ`
- Request ID: `1`
- Payload: Command array of 3 elements

---

### Response Example (`OK`):

```
RES\r\n
1\r\n
OK\r\n
```

- Message type: `RES`
- Request ID: `1` (corresponds to request ID)
- Payload: Simple string status `OK` (no leading `+`)

---

## Message Type Details

| Message Type | Direction       | Description                              |
| ------------ | --------------- | ---------------------------------------- |
| `REQ`        | Client → Server | A request message with unique request ID |
| `RES`        | Server → Client | Response matching a specific request ID  |

---

## Request IDs

Each NASP request contains a client-generated `request_id` that uniquely identifies the request within that connection. The server’s responsibility is to echo this `request_id` back in the corresponding response, enabling clients to match replies to their requests.

By default, the server does **not** enforce any specific ordering or sequencing of request IDs. This allows clients to send multiple concurrent or pipelined requests without waiting for previous responses. Clients must ensure their request IDs are unique for outstanding requests and may reuse IDs only after receiving the corresponding response.

For increased safety, debugging and in production environments, servers can optionally enable a strict mode to enforce sequential ordering of request IDs and reject out-of-order or duplicate IDs. This feature helps detect and prevent client misbehavior but is not required by the core protocol and may be disabled to maximize performance and throughput.

---

## Payload Encoding

Payloads in NASP messages follow the serialization rules described in the protocol:

- **Simple strings** (prefixed with `+` in bulk or array elements) for short statuses like `OK`
- **Bulk strings** (prefixed with `$`) for length-prefixed ASCII strings
- **Integers** (prefixed with `:`)
- **Floats** (prefixed with `;`)
- **Arrays** (prefixed with `*`) for multiple elements and commands

When the message payload is a single line following the request ID and not part of a bulk string or array, treat it as a simple string or error without a leading `+`.

---

## Notes

- Each line must be terminated by `\r\n`.
- Bulk and array elements follow NASP’s length-prefixed format as described in their respective specs.
- The request ID allows out-of-order responses and pipelining.
- NASP aims to be human-readable and easy to debug; clients and servers SHOULD log messages respecting the `REQ`/`RES` headers and request IDs.

---

## Default Port

By convention, NASP servers listen on **TCP port 6380** by default, which is chosen close to Redis's 6379 for familiarity but distinct to avoid conflicts. This default port can be customized during server configuration to fit deployment environments.

Clients connecting to a NASP server should specify the appropriate host and port, with 6380 being the default unless otherwise configured.

---

## Summary

| Field        | Description                   | Format                     | Example               |
| ------------ | ----------------------------- | -------------------------- | --------------------- |
| Message Type | Request or Response           | ASCII string: `REQ`/`RES`  | `REQ\r\n`             |
| Request ID   | Unique integer per connection | ASCII decimal number       | `42\r\n`              |
| Payload      | Command, status, or data      | NASP serialized data types | `OK\r\n`, `*3\r\n...` |
