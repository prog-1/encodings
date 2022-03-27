# Encodings

## UTF-8

See https://en.wikipedia.org/wiki/UTF-8

> UTF-8 is a variable-width character encoding used for electronic communication. Defined by the Unicode Standard, the name is derived from Unicode (or Universal Coded Character Set) Transformation Format – 8-bit.[1]
>
> UTF-8 is capable of encoding all 1,112,064[nb 1] valid character code points in Unicode using one to four one-byte (8-bit) code units. Code points with lower numerical values, which tend to occur more frequently, are encoded using fewer bytes. It was designed for backward compatibility with ASCII: the first 128 characters of Unicode, which correspond one-to-one with ASCII, are encoded using a single byte with the same binary value as ASCII, so that valid ASCII text is valid UTF-8-encoded Unicode as well. Since ASCII bytes do not occur when encoding non-ASCII code points into UTF-8, UTF-8 is safe to use within most programming and document languages that interpret certain ASCII characters in a special way, such as / (slash) in filenames, \ (backslash) in escape sequences, and % in printf.

Since the restriction of the Unicode code-space to 21-bit values in 2003, UTF-8 is defined to encode code points in one to four bytes, depending on the number of significant bits in the numerical value of the code point. The following table shows the structure of the encoding. The x characters are replaced by the bits of the code point.

+------------------+-----------------+----------+----------+----------+----------+
| First code point | Last code point | Byte 1   | Byte 2   | Byte 3   | Byte 4   |
+------------------+-----------------+----------+----------+----------+----------+
| U+0000           | U+007F          | 0xxxxxxx	|          |          |          |
| U+0080           | U+07FF          | 110xxxxx | 10xxxxxx |	        |          |
| U+0800           | U+FFFF          | 1110xxxx | 10xxxxxx | 10xxxxxx |	         |
| U+10000          | U+10FFFF        | 11110xxx | 10xxxxxx | 10xxxxxx | 10xxxxxx |
+------------------+-----------------+----------+----------+----------+----------+
