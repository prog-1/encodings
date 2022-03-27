# Encodings

## UTF-8

See https://en.wikipedia.org/wiki/UTF-8

UTF-8 is a variable-width character encoding used for electronic communication. Defined by the Unicode Standard, the name is derived from Unicode (or Universal Coded Character Set) Transformation Format – 8-bit.

UTF-8 is capable of encoding all 1,112,064 valid character code points in Unicode using one to four one-byte (8-bit) code units. Code points with lower numerical values, which tend to occur more frequently, are encoded using fewer bytes. It was designed for backward compatibility with ASCII: the first 128 characters of Unicode, which correspond one-to-one with ASCII, are encoded using a single byte with the same binary value as ASCII, so that valid ASCII text is valid UTF-8-encoded Unicode as well. Since ASCII bytes do not occur when encoding non-ASCII code points into UTF-8, UTF-8 is safe to use within most programming and document languages that interpret certain ASCII characters in a special way, such as / (slash) in filenames, \ (backslash) in escape sequences, and % in printf.

Since the restriction of the Unicode code-space to 21-bit values in 2003, UTF-8 is defined to encode code points in one to four bytes, depending on the number of significant bits in the numerical value of the code point. The following table shows the structure of the encoding. The x characters are replaced by the bits of the code point.

| First code point | Last code point | Byte 1   | Byte 2   | Byte 3   | Byte 4   |
|------------------|-----------------|----------|----------|----------|----------|
| U+0000           | U+007F          | 0xxxxxxx	|          |          |          |
| U+0080           | U+07FF          | 110xxxxx | 10xxxxxx |          |          |
| U+0800           | U+FFFF          | 1110xxxx | 10xxxxxx | 10xxxxxx |	         |
| U+10000          | U+10FFFF        | 11110xxx | 10xxxxxx | 10xxxxxx | 10xxxxxx |

https://goplay.tools/snippet/8LXkmaio6gJ

```go
// EncodeRune writes into p (which must be large enough) the UTF-8 encoding of the rune.
// If the rune is out of range, it writes the encoding of RuneError.
// It returns the number of bytes written.
func EncodeRune(p []byte, r rune) int {
	switch i := uint32(r); {
	case i < 1<<7:
		p[0] = byte(r)
		return 1
	case i < 1<<11:
		p[0] = 0b11000000 | byte(r>>6)
		p[1] = 0b10000000 | byte(r)&0b00111111
		return 2
	case i < 1<<16:
		p[0] = 0b11100000 | byte(r>>12)
		p[1] = 0b10000000 | byte(r>>6)&0b00111111
		p[2] = 0b10000000 | byte(r)&0b00111111
		return 3
	default:
		p[0] = 0b11110000 | byte(r>>18)
		p[1] = 0b10000000 | byte(r>>12)&0b00111111
		p[2] = 0b10000000 | byte(r>>6)&0b00111111
		p[3] = 0b10000000 | byte(r)&0b00111111
		return 4
	}
}

const RuneError = '\uFFFD' // the "error" Rune or "Unicode replacement character"

// DecodeRune unpacks the first UTF-8 encoding in p and returns the rune and
// its width in bytes. If p is empty it returns (RuneError, 0). Otherwise, if
// the encoding is invalid, it returns (RuneError, 1). Both are impossible
// results for correct, non-empty UTF-8.
func DecodeRune(p []byte) (r rune, size int) {
	n := len(p)
	if n < 1 {
		return RuneError, 0
	}
	p0 := p[0]
	if p0 < 1<<7 {
		return rune(p0), 1
	}
	switch {
	case p0&0b11100000 == 0b11000000:
		return rune(p0&0b00011111)<<6 | rune(p[1]&0b00111111), 2
	case p0&0b11110000 == 0b11100000:
		return rune(p0&0b00001111)<<12 | rune(p[1]&0b00111111)<<6 | rune(p[2]&0b00111111), 3
	case p0&0b11111000 == 0b11110000:
		return rune(p0&0b00000111)<<18 | rune(p[1]&0b00111111)<<12 | rune(p[2]&0b00111111)<<6 | rune(p[3]&0b00111111), 4
	default:
		return RuneError, 0
	}
}
```

## Base64

See https://en.wikipedia.org/wiki/Base64

In computer programming, Base64 is a group of binary-to-text encoding schemes that represent binary data (more specifically, a sequence of 8-bit bytes) in sequences of 24 bits that can be represented by four 6-bit Base64 digits.

Common to all binary-to-text encoding schemes, Base64 is designed to carry data stored in binary formats across channels that only reliably support text content. Base64 is particularly prevalent on the World Wide Web where one of its uses is the ability to embed image files or other binary assets inside textual assets such as HTML and CSS files.

Base64 is also widely used for sending e-mail attachments. This is required because SMTP—in its original form—was designed to transport 7-bit ASCII characters only. This encoding causes an overhead of 33–36% (33% by the encoding itself; up to 3% more by the inserted line breaks).

### Base64 table from RFC 4648

See https://datatracker.ietf.org/doc/html/rfc4648#section-4

This is the Base64 alphabet uses A-Z, a-z, 0-9 for the first 62 characters, and + with / as the last two characters. Padding is specified as =.

Please note that there are multiple variant tables as shown at https://en.wikipedia.org/wiki/Base64#Variants_summary_table.
