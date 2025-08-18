
[[how base64 work in short msg]]


Suppose we want to encode the string “Hello” in Base64.


step1
轉為 ASCII value


•	“H” = 72
•	“e” = 101
•	“l” = 108
•	“l” = 108
•	“o” = 111



step2
轉為 binary


•	“H” = 01001000
•	“e” = 01100101
•	“l” = 01101100
•	“l” = 01101100
•	“o” = 01101111

So, the binary string for “Hello” is:

01001000 01100101 01101100 01101100 01101111


step3
六個六個一組

•	Group the binary string into 6-bit chunks:

010010 000110 010101 101100 011011 000110 1111


step4
每一組轉為 decimal

•	010010 = 18
•	000110 = 6
•	010101 = 21
•	101100 = 44
•	011011 = 27
•	000110 = 6
•	1111 (4 bits, so we add two padding bits 00) = 60


step5
把 decimal 轉為 Base64 Characters

•	Using the Base64 character set (A-Z, a-z, 0-9, +, /), map each decimal value to its corresponding Base64 character:

18 -> S
6  -> G
21 -> V
44 -> s
27 -> b
6  -> G
60 -> 8


6.	Result:
•	The Base64 encoded result for “Hello” is: “SGVsbG8=”

The ”=” padding at the end is used because Base64 encoding requires that the output length be a multiple of 4 characters.