# Float

This is a **64 bit** double-precision floating point.

Floats are intended for numeric responses, such as the value of a float key.

Floats must follow the following:

- must begin with the semi-colon (`;`) prefix
- must end with the CRLF (`\r\n`) terminator
- must be base-10 encoded in ASCII decimal notation
- can optionally be prefixed with a plus (`+`) or minus (`-`) sign (after the semi-colon prefix)
- must contain either a fractional part (a decimal dot) or an exponent part (`e` or `E`)
- must represent a valid IEEE 754 double-precision float

### Examples:

```
;1234567890.0\r\n
```

```
;-1234567890.0\r\n
```

```
;+1234567890.0\r\n
```

```
;-1.23e+10\r\n
```

```
;+3.14E-2\r\n
```

NASP encodes floats as follows:

```
;[<+|->]<integral>[.<fractional>]<E|e>[+|-]<exponent>]\r\n
```
