# Arrays

Arrays are an ordered collection of NASP values.
They can contain zero or more elements, where each element can be of any valid NASP data type (including other arrays).

Arrays are intended for grouping related values, such as command arguments or multi-value responses.

The arrays have a memory limit payload of **64 mega-bytes**.

### Array Structure

Arrays must follow the following:

- must begin with the asterisk (`*`) prefix
- must then be followed by the number of elements in the array, encoded in base-10, followed by a CRLF (`\r\n`) terminator
- the number of elements must be zero or greater
- each element follows immediately after the array length declaration
- the array length specifies the number of elements, **not** the number of bytes
- arrays can be nested within other arrays
- must not exceed 64 mega-bytes total (including all element prefixes, lengths, payloads, and CRLF characters)
- must not contain more than the maximum allowed element count of **512 items**

### Examples

An array containing three simple strings:

```
*3\r\n
+foo\r\n
+bar\r\n
+baz\r\n
```

An empty array:

```
*0\r\n
```

A nested array:

```
*2\r\n
+outer\r\n
*2\r\n
+inner1\r\n
+inner2\r\n
```

NASP encodes arrays as follows:

```
*<number-of-elements>\r\n<element-1><element-2>...<element-N>
```
