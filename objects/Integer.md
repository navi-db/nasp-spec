# Integer

This is a signed base-10, **64 bit** integer.

Integers are intended for numeric responses, such as the value of an integer key.

Integers must follow the following:

- must begin with the colon (`:`) prefix
- must end with the CRLF (`\r\n`) terminator
- must be no greater or smaller than a 64 bit signed integer
- must be base-10 encoded
- can optionally be prefixed with a plus (`+`) or minus (`-`) sign (after the colon prefix)

For example:

```
:1234567890\r\n
```

```
:-1234567890\r\n
```

```
:+1234567890\r\n
```

NASP encodes integers as followed:

```
:[<+|->]<number>\r\n
```
