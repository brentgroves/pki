https://www.hostinger.com/tutorials/uri-vs-url#:~:text=URI%20identifies%20a%20resource%20and,location%20of%20a%20unique%20resource.
URI is the superset of a URN and a URL.
The URL is a more specific instance of a URI meant to locate resources on the web.

https://www.urlencoder.io/learn/
A URL (Uniform Resource Locator) is the address of a resource in the world wide web. URLs have a well-defined structure which was formulated in RFC 1738 by Tim Berners-Lee, the inventor of the world wide web.
Every URL confirms to a generic syntax which looks like this -

scheme:[//[user:password@]host[:port]]path[?query][#fragment]
Some parts of the URL syntax like [user:password@] are deprecated and seldom used due to security reasons. Following is an example of a URL that you see more often on the internet -

https://www.google.com/search?q=hello+world#brs
There have been many improvements done to the initial RFC defining the syntax of Uniform Resource Locators (URLs). The current RFC that defines the Generic URI syntax is RFC 3986. This post contains information from the latest RFC document.

URL Encoding (Percent Encoding)
A URL is composed from a limited set of characters belonging to the US-ASCII character set. These characters include digits (0-9), letters(A-Z, a-z), and a few special characters ("-", ".", "_", "~").

ASCII control characters (e.g. backspace, vertical tab, horizontal tab, line feed etc), unsafe characters like space, \, <, >, {, } etc, and any character outside the ASCII charset is not allowed to be placed directly within URLs.

Moreover, there are some characters that have special meaning within URLs. These characters are called reserved characters. Some examples of reserved characters are ?, /, #, : etc. Any data transmitted as part of the URL, whether in query string or path segment, must not contain these characters.

So what do we do when we need to transmit any data in the URL that contain these disallowed characters? Well, we encode them!

URL Encoding converts reserved, unsafe, and non-ASCII characters in URLs to a format that is universally accepted and understood by all web browsers and servers. It first converts the character to one or more bytes. Then each byte is represented by two hexadecimal digits preceded by a percent sign (%) - (e.g. %xy). The percent sign is used as an escape character.

URL encoding is also called percent encoding since it uses percent sign (%) as an escape character.

URL Encoding Example
Space: One of the most frequent URL Encoded character you’re likely to encounter is space. The ASCII value of space character in decimal is 32, which when converted to hex comes out to be 20. Now we just precede the hexadecimal representation with a percent sign (%), which gives us the URL encoded value - %20.

ASCII Character Encoding Reference
The following table is a reference of ASCII characters to their corresponding URL Encoded form.

Note that, Encoding alphanumeric ASCII characters are not required. For example, you don’t need to encode the character '0' to %30 as shown in the following table. It can be transmitted as is. But the encoding is still valid as per the RFC. All the characters that are safe to be transmitted inside URLs are colored green in the table.

The following table uses rules defined in RFC 3986 for URL encoding.

Decimal	Character	URL Encoding (UTF-8)
0	NUL(null character)	%00
1	SOH(start of header)	%01
2	STX(start of text)	%02
3	ETX(end of text)	%03
4	EOT(end of transmission)	%04
5	ENQ(enquiry)	%05
6	ACK(acknowledge)	%06
7	BEL(bell (ring))	%07
8	BS(backspace)	%08
9	HT(horizontal tab)	%09
10	LF(line feed)	%0A
11	VT(vertical tab)	%0B
12	FF(form feed)	%0C
13	CR(carriage return)	%0D
14	SO(shift out)	%0E
15	SI(shift in)	%0F
16	DLE(data link escape)	%10
17	DC1(device control 1)	%11
18	DC2(device control 2)	%12
19	DC3(device control 3)	%13
20	DC4(device control 4)	%14
21	NAK(negative acknowledge)	%15
22	SYN(synchronize)	%16
23	ETB(end transmission block)	%17
24	CAN(cancel)	%18
25	EM(end of medium)	%19
26	SUB(substitute)	%1A
27	ESC(escape)	%1B
28	FS(file separator)	%1C
29	GS(group separator)	%1D
30	RS(record separator)	%1E
31	US(unit separator)	%1F
32	space	%20
33	!	%21
34	"	%22
35	#	%23
36	$	%24
37	%	%25
38	&	%26
39	'	%27
40	(	%28
41	)	%29
42	*	%2A
43	+	%2B
44	,	%2C
45	-	%2D
46	.	%2E
47	/	%2F
48	0	%30
49	1	%31
50	2	%32
51	3	%33
52	4	%34
53	5	%35
54	6	%36
55	7	%37
56	8	%38
57	9	%39
58	:	%3A
59	;	%3B
60	<	%3C
61	=	%3D
62	>	%3E
63	?	%3F
64	@	%40
65	A	%41
66	B	%42
67	C	%43
68	D	%44
69	E	%45
70	F	%46
71	G	%47
72	H	%48
73	I	%49
74	J	%4A
75	K	%4B
76	L	%4C
77	M	%4D
78	N	%4E
79	O	%4F
80	P	%50
81	Q	%51
82	R	%52
83	S	%53
84	T	%54
85	U	%55
86	V	%56
87	W	%57
88	X	%58
89	Y	%59
90	Z	%5A
91	[	%5B
92	\	%5C
93	]	%5D
94	^	%5E
95	_	%5F
96	`	%60
97	a	%61
98	b	%62
99	c	%63
100	d	%64
101	e	%65
102	f	%66
103	g	%67
104	h	%68
105	i	%69
106	j	%6A
107	k	%6B
108	l	%6C
109	m	%6D
110	n	%6E
111	o	%6F
112	p	%70
113	q	%71
114	r	%72
115	s	%73
116	t	%74
117	u	%75
118	v	%76
119	w	%77
120	x	%78
121	y	%79
122	z	%7A
123	{	%7B
124	|	%7C
125	}	%7D
126	~	%7E
127	DEL(delete (rubout))	%7F
Footnotes
URL (Uniform Resource Locator)
What every developer must know about URL Encoding
Why does URL encoding exist for ASCII character set
RFC 3986 - Uniform Resource Identifier (URI): Generic Syntax.
