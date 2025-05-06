# Motivation

Speichern und Übertragen von Binärdaten ist nicht immer problemlos möglich. 

Hinweis: Hashwerte sind Binärdaten

Eine Einschränkung auf ASCII Zeichen (printable characters) ist dann sinnvoll. Binärdaten werden daher oft in eine ASCII Text Darstellung umgewandelt. 

Jede Art der Darstellung (bzw. Umwandeln in ein anderes Format) von Daten/Information nennt man **Kodierung** (engl. Encoding).


```bash
$ wget http://web275.142.hosttech.eu/htl/ITSI_2/03_hashing_encoding/data/goethe.txt
$ openssl dgst -sha256 -binary goethe.txt
��_�+�BP�c�T}�?9�+d�U�?�p�
# sollten 32 Bytes sein, sind aber nur 26 Zeichen !?
```

Einschub: Warum können Binärdaten nicht als Text übertragen werden? Binärdaten bestehen aus einer Folge von Bytes. Jedes Byte kann einen Wert von 0...255 haben – nicht jedes dieser Werte entspricht einem druckbaren Zeichen.



# Hex Encoding

- auch Radix-16
- 1 Byte ⟶ 2 Buchstaben
  - Zeichensatz: `0–9`,`a–f`
  - Beispiel: Buchstabe "a" ⟶ 0d97 ⟶ 0x61 ⟶  "61"
    - `a` --> 1Byte
    - `61` --> 2 Buchstaben

```bash
$ openssl dgst -sha256 -hex goethe.txt
SHA256(goethe.txt)= a911a35ff92bcd4250c063a0547dae3f1ba2b839eaf12b64c955cf3ff7e570e0
```

Nachteil: doppelter Platzbedarf im Vergleich zu Binär



# Base64

- auch Radix-64
- 1 Buchstabe repräsentiert im Base64 format 6 Bit
- 3 Byte Binär sind 24 Bit. Werden diese 24Bit in 6Bit Einheiten geteilt, ergeben sich 4 Teile, also 4 Buchstaben.
In der Regel werden gerne 3 Byte Blöcke verarbeitet. Also immer 4 Buchstaben angezeigt.
- Zeichensatz: `A–Z`, `a–z`,  `0–9` , `+`, `/` und `=` (64 Buchstaben und `=` als Padding)

  ![Base64 Kodiertabelle](fig/base64_tabelle.png)

Beispiele:

ASCII:
```
3AHITS
```

Hex

```
334148495453
```

Base64
```
M0FISVRT
```

## Padding: 

Das Padding mit "="-Zeichen ermöglicht es, die ursprüngliche Länge der uncodierten Daten eindeutig zu bestimmen, wenn diese nicht durch 3 teilbar ist

Wenn die Länge der ursprünglichen Länge der uncodierten Daten kein Vielfaches von 3 ist wird `=` oder `==` hinten angefügt. Die Länge eines Base64 Strings ist daher immer ein Vielfaches von 4.

- 6 Buchstaben: `Router` - 6/3=2 --> kein Padding
  - Base64: `Um91dGVy`
- 7 Buchstaben: `Encoder` - 7/3=2 | 1 Rest --> "==" damit wieder ein vielfaches von 3
  - Base64: `RW5jb2Rlcg==`
- 8 Buchstaben: `Computer` - 8/3=2 | 2 Rest --> "=" damit wieder ein Vielfaches von 3
  - Base64: `Q29tcHV0ZXI=`
- 9 Buchstaben: `Prozessor` - 9/3=3 | 0 Rest --> kein Padding
  - Base64: `UHJvemVzc29y`

## Beispiel:

### 3AHITS
| Codierung         | Wert   | Info                                     |
|-------------------|--------|------------------------------------------|
| ASCII             | 3AHITS | 6Buchstaben - 6Bytes                     |
| Binär 8er Gruppen | 00110011 01000001 01001000 01001001 01010100 01010011 | 6x8Bit= 48Zeichen  |
| Binär 6er Gruppen | 001100 110100 000101 001000 010010 010101 010001 010011 | 8x6er Gruppen |
| Base64            | M0FISVRT | Jede 6er Gruppe repräsentiert ein Zeichen - siehe Tabelle |

### Wert
| Codierung         | Wert   | Info                                     |
|-------------------|--------|------------------------------------------|
| ASCII             | Werte | 5 Buchstaben - 5 Bytes                     |
| Binär 8er Gruppen | 01010111 01100101 01110010 01110100 01100101 | 5x8Bit= 40Zeichen  |
| Binär 6er Gruppen | 010101 110110 010101 110010 011101 000110 0101 | 5x6er Gruppen und eine 4er Gruppe |
| | 010101 110110 010101 110010 011101 000110 010100 | die letzte 6er Gruppe mit 0 auffüllen
| Base64            | V2VydGU | Jede 6er Gruppe repräsentiert ein Zeichen - siehe Tabelle |
| Padding | V2VydGU= | 1 "=" da die Länge 5/3=1 | 2 Rest -> "=" damit wieder ein vielfaches von 3





Siehe [Base64 wikipedia en](https://en.wikipedia.org/wiki/Base64)

Base64 encoding mit `openssl`:

```sh
$ openssl dgst -sha256 -binary goethe.txt | openssl enc -base64
qRGjX/krzUJQwGOgVH2uPxuiuDnq8StkyVXPP/flcOA=
```

--- 

## ASCII und Base64 Tabelle


| **dez** | **bin**  | **hex** | **ASCII** | **Base64** |  | **dez**  | **bin**  | **hex** | **ASCII** |  | **dez**  | **bin**  | **hex** | **ASCII** |  | **dez**  | **bin**  | **hex** | **ASCII** |
|---------|----------|---------|-----------|------------|------|----------|----------|---------|-----------|------|----------|----------|---------|-----------|------|----------|----------|---------|-----------|
| 0       | 00000000 | 00      | NULL      | A          |      | 64       | 01000000 | 40      | @         |      | 128      | 10000000 | 80      | Ç         |      | 192      | 11000000 | C0      | └         |
| 1       | 00000001 | 01      | SOH       | B          |      | 65       | 01000001 | 41      | A         |      | 129      | 10000001 | 81      | ü         |      | 193      | 11000001 | C1      | ┴         |
| 2       | 00000010 | 02      | STX       | C          |      | 66       | 01000010 | 42      | B         |      | 130      | 10000010 | 82      | é         |      | 194      | 11000010 | C2      | ┬         |
| 3       | 00000011 | 03      | ETX       | D          |      | 67       | 01000011 | 43      | C         |      | 131      | 10000011 | 83      | â         |      | 195      | 11000011 | C3      | ├         |
| 4       | 00000100 | 04      | EOT       | E          |      | 68       | 01000100 | 44      | D         |      | 132      | 10000100 | 84      | ä         |      | 196      | 11000100 | C4      | ─         |
| 5       | 00000101 | 05      | ENQ       | F          |      | 69       | 01000101 | 45      | E         |      | 133      | 10000101 | 85      | à         |      | 197      | 11000101 | C5      | ┼         |
| 6       | 00000110 | 06      | ACK       | G          |      | 70       | 01000110 | 46      | F         |      | 134      | 10000110 | 86      | å         |      | 198      | 11000110 | C6      | ã         |
| 7       | 00000111 | 07      | BEL       | H          |      | 71       | 01000111 | 47      | G         |      | 135      | 10000111 | 87      | ç         |      | 199      | 11000111 | C7      | Ã         |
| 8       | 00001000 | 08      | BS        | I          |      | 72       | 01001000 | 48      | H         |      | 136      | 10001000 | 88      | ê         |      | 200      | 11001000 | C8      | ╚         |
| 9       | 00001001 | 09      | HT        | J          |      | 73       | 01001001 | 49      | I         |      | 137      | 10001001 | 89      | ë         |      | 201      | 11001001 | C9      | ╔         |
| 10      | 00001010 | 0A      | LF        | K          |      | 74       | 01001010 | 4A      | J         |      | 138      | 10001010 | 8A      | è         |      | 202      | 11001010 | CA      | ╩         |
| 11      | 00001011 | 0B      | VT        | L          |      | 75       | 01001011 | 4B      | K         |      | 139      | 10001011 | 8B      | ï         |      | 203      | 11001011 | CB      | ╦         |
| 12      | 00001100 | 0C      | FF        | M          |      | 76       | 01001100 | 4C      | L         |      | 140      | 10001100 | 8C      | î         |      | 204      | 11001100 | CC      | ╠         |
| 13      | 00001101 | 0D      | CR        | N          |      | 77       | 01001101 | 4D      | M         |      | 141      | 10001101 | 8D      | ì         |      | 205      | 11001101 | CD      | ═         |
| 14      | 00001110 | 0E      | SO        | O          |      | 78       | 01001110 | 4E      | N         |      | 142      | 10001110 | 8E      | Ä         |      | 206      | 11001110 | CE      | ╬         |
| 15      | 00001111 | 0F      | SI        | P          |      | 79       | 01001111 | 4F      | O         |      | 143      | 10001111 | 8F      | Å         |      | 207      | 11001111 | CF      | ¤         |
| 16      | 00010000 | 10      | DLE       | Q          |      | 80       | 01010000 | 50      | P         |      | 144      | 10010000 | 90      | É         |      | 208      | 11010000 | D0      | ð         |
| 17      | 00010001 | 11      | DC1       | R          |      | 81       | 01010001 | 51      | Q         |      | 145      | 10010001 | 91      | æ         |      | 209      | 11010001 | D1      | Ð         |
| 18      | 00010010 | 12      | DC2       | S          |      | 82       | 01010010 | 52      | R         |      | 146      | 10010010 | 92      | Æ         |      | 210      | 11010010 | D2      | Ê         |
| 19      | 00010011 | 13      | DC3       | T          |      | 83       | 01010011 | 53      | S         |      | 147      | 10010011 | 93      | ô         |      | 211      | 11010011 | D3      | Ë         |
| 20      | 00010100 | 14      | DC4       | U          |      | 84       | 01010100 | 54      | T         |      | 148      | 10010100 | 94      | ö         |      | 212      | 11010100 | D4      | È         |
| 21      | 00010101 | 15      | NAK       | V          |      | 85       | 01010101 | 55      | U         |      | 149      | 10010101 | 95      | ò         |      | 213      | 11010101 | D5      | ı         |
| 22      | 00010110 | 16      | SYN       | W          |      | 86       | 01010110 | 56      | V         |      | 150      | 10010110 | 96      | û         |      | 214      | 11010110 | D6      | Í         |
| 23      | 00010111 | 17      | ETB       | X          |      | 87       | 01010111 | 57      | W         |      | 151      | 10010111 | 97      | ù         |      | 215      | 11010111 | D7      | Î         |
| 24      | 00011000 | 18      | CAN       | Y          |      | 88       | 01011000 | 58      | X         |      | 152      | 10011000 | 98      | ÿ         |      | 216      | 11011000 | D8      | Ï         |
| 25      | 00011001 | 19      | EM        | Z          |      | 89       | 01011001 | 59      | Y         |      | 153      | 10011001 | 99      | Ö         |      | 217      | 11011001 | D9      | ┘         |
| 26      | 00011010 | 1A      | SUB       | a          |      | 90       | 01011010 | 5A      | Z         |      | 154      | 10011010 | 9A      | Ü         |      | 218      | 11011010 | DA      | ┌         |
| 27      | 00011011 | 1B      | ESC       | b          |      | 91       | 01011011 | 5B      | [         |      | 155      | 10011011 | 9B      | ø         |      | 219      | 11011011 | DB      | █         |
| 28      | 00011100 | 1C      | FS        | c          |      | 92       | 01011100 | 5C      | \         |      | 156      | 10011100 | 9C      | £         |      | 220      | 11011100 | DC      | ▄         |
| 29      | 00011101 | 1D      | GS        | d          |      | 93       | 01011101 | 5D      | ]         |      | 157      | 10011101 | 9D      | Ø         |      | 221      | 11011101 | DD      | ¦         |
| 30      | 00011110 | 1E      | RS        | e          |      | 94       | 01011110 | 5E      | ^         |      | 158      | 10011110 | 9E      | ×         |      | 222      | 11011110 | DE      | Ì         |
| 31      | 00011111 | 1F      | US        | f          |      | 95       | 01011111 | 5F      | _         |      | 159      | 10011111 | 9F      | ƒ         |      | 223      | 11011111 | DF      | ▀         |
| 32      | 00100000 | 20      |           | g          |      | 96       | 01100000 | 60      | `         |      | 160      | 10100000 | A0      | á         |      | 224      | 11100000 | E0      | Ó         |
| 33      | 00100001 | 21      | !         | h          |      | 97       | 01100001 | 61      | a         |      | 161      | 10100001 | A1      | í         |      | 225      | 11100001 | E1      | ß         |
| 34      | 00100010 | 22      | "         | i          |      | 98       | 01100010 | 62      | b         |      | 162      | 10100010 | A2      | ó         |      | 226      | 11100010 | E2      | Ô         |
| 35      | 00100011 | 23      | #         | j          |      | 99       | 01100011 | 63      | c         |      | 163      | 10100011 | A3      | ú         |      | 227      | 11100011 | E3      | Ò         |
| 36      | 00100100 | 24      | $         | k          |      | 100      | 01100100 | 64      | d         |      | 164      | 10100100 | A4      | ñ         |      | 228      | 11100100 | E4      | õ         |
| 37      | 00100101 | 25      | %         | l          |      | 101      | 01100101 | 65      | e         |      | 165      | 10100101 | A5      | Ñ         |      | 229      | 11100101 | E5      | Õ         |
| 38      | 00100110 | 26      | &         | m          |      | 102      | 01100110 | 66      | f         |      | 166      | 10100110 | A6      | ª         |      | 230      | 11100110 | E6      | µ         |
| 39      | 00100111 | 27      | '         | n          |      | 103      | 01100111 | 67      | g         |      | 167      | 10100111 | A7      | º         |      | 231      | 11100111 | E7      | þ         |
| 40      | 00101000 | 28      | (         | o          |      | 104      | 01101000 | 68      | h         |      | 168      | 10101000 | A8      | ¿         |      | 232      | 11101000 | E8      | Þ         |
| 41      | 00101001 | 29      | )         | p          |      | 105      | 01101001 | 69      | i         |      | 169      | 10101001 | A9      | ®         |      | 233      | 11101001 | E9      | Ú         |
| 42      | 00101010 | 2A      | *         | q          |      | 106      | 01101010 | 6A      | j         |      | 170      | 10101010 | AA      | ¬         |      | 234      | 11101010 | EA      | Û         |
| 43      | 00101011 | 2B      | +         | r          |      | 107      | 01101011 | 6B      | k         |      | 171      | 10101011 | AB      | ½         |      | 235      | 11101011 | EB      | Ù         |
| 44      | 00101100 | 2C      | ,         | s          |      | 108      | 01101100 | 6C      | l         |      | 172      | 10101100 | AC      | ¼         |      | 236      | 11101100 | EC      | ý         |
| 45      | 00101101 | 2D      | -         | t          |      | 109      | 01101101 | 6D      | m         |      | 173      | 10101101 | AD      | ¡         |      | 237      | 11101101 | ED      | Ý         |
| 46      | 00101110 | 2E      | .         | u          |      | 110      | 01101110 | 6E      | n         |      | 174      | 10101110 | AE      | «         |      | 238      | 11101110 | EE      | ¯         |
| 47      | 00101111 | 2F      | /         | v          |      | 111      | 01101111 | 6F      | o         |      | 175      | 10101111 | AF      | »         |      | 239      | 11101111 | EF      | ´         |
| 48      | 00110000 | 30      | 0         | w          |      | 112      | 01110000 | 70      | p         |      | 176      | 10110000 | B0      | ░         |      | 240      | 11110000 | F0      | ­\-        |
| 49      | 00110001 | 31      | 1         | x          |      | 113      | 01110001 | 71      | q         |      | 177      | 10110001 | B1      | ▒         |      | 241      | 11110001 | F1      | ±         |
| 50      | 00110010 | 32      | 2         | y          |      | 114      | 01110010 | 72      | r         |      | 178      | 10110010 | B2      | ▓         |      | 242      | 11110010 | F2      | ‗         |
| 51      | 00110011 | 33      | 3         | z          |      | 115      | 01110011 | 73      | s         |      | 179      | 10110011 | B3      | │         |      | 243      | 11110011 | F3      | ¾         |
| 52      | 00110100 | 34      | 4         | 0          |      | 116      | 01110100 | 74      | t         |      | 180      | 10110100 | B4      | ┤         |      | 244      | 11110100 | F4      | ¶         |
| 53      | 00110101 | 35      | 5         | 1          |      | 117      | 01110101 | 75      | u         |      | 181      | 10110101 | B5      | Á         |      | 245      | 11110101 | F5      | §         |
| 54      | 00110110 | 36      | 6         | 2          |      | 118      | 01110110 | 76      | v         |      | 182      | 10110110 | B6      | Â         |      | 246      | 11110110 | F6      | ÷         |
| 55      | 00110111 | 37      | 7         | 3          |      | 119      | 01110111 | 77      | w         |      | 183      | 10110111 | B7      | À         |      | 247      | 11110111 | F7      | ¸         |
| 56      | 00111000 | 38      | 8         | 4          |      | 120      | 01111000 | 78      | x         |      | 184      | 10111000 | B8      | ©         |      | 248      | 11111000 | F8      | °         |
| 57      | 00111001 | 39      | 9         | 5          |      | 121      | 01111001 | 79      | y         |      | 185      | 10111001 | B9      | ╣         |      | 249      | 11111001 | F9      | ¨         |
| 58      | 00111010 | 3A      | :         | 6          |      | 122      | 01111010 | 7A      | z         |      | 186      | 10111010 | BA      | ║         |      | 250      | 11111010 | FA      | ·         |
| 59      | 00111011 | 3B      | ;         | 7          |      | 123      | 01111011 | 7B      | {         |      | 187      | 10111011 | BB      | ╗         |      | 251      | 11111011 | FB      | ¹         |
| 60      | 00111100 | 3C      | <         | 8          |      | 124      | 01111100 | 7C      | \|         |      | 188      | 10111100 | BC      | ╝         |      | 252      | 11111100 | FC      | ³         |
| 61      | 00111101 | 3D      | =         | 9          |      | 125      | 01111101 | 7D      | }         |      | 189      | 10111101 | BD      | ¢         |      | 253      | 11111101 | FD      | ²         |
| 62      | 00111110 | 3E      | >         | +          |      | 126      | 01111110 | 7E      | ~         |      | 190      | 10111110 | BE      | ¥         |      | 254      | 11111110 | FE      | ■         |
| 63      | 00111111 | 3F      | ?         | /          |      | 127      | 01111111 | 7F      | DEL       |      | 191      | 10111111 | BF      | ┐         |      | 255      | 11111111 | FF      | nbsp      |
