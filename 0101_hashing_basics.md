- Daten (beliebig groß) ⇨ Hashfunktion ⇨ Wert fixer Länge, z.B. 128, 256 oder 512 Bits. 
  - "Fingerabdruck" der Daten
- nicht umkehrbar (one way function)
- Kollisionssicher: es ist praktisch unmöglich andere Daten zu finden die den gleichen Hashwert ergeben

Angriff nur durch brute-force.



# Kryptographische Anwendung

- Erkennen von Fälschungen (Integrität einer Nachricht)
- Digitale Unterschrift (Authentizität)
- Speichern von Passwörtern
- Kryptowährungen (Bitcoin ⇨ SHA-256)



# Umsetzung

Erste Idee: 8-Bit Summe der ASCII Codes. 

```
L  U  K  A  S
76 85 75 65 83
Summe = 384 % 256 = 128
```

Ist nicht umkehrbar (viele Texte können 128 ergeben)

Aber: `KLAUS` ergibt den gleichen Hash-Code (=Hash-Kollision).

```
K  L  A  U  S
75 76 65 85 83
Summe = 384 % 256 = 128
```

Es sollte nicht so einfach sein Daten zu finden die den gleichen Hashwert ergeben. Brauchen eine bessere Lösung!  ⇨



# Sichere Hash Funktionen

*secure hash functions*, Komplexe Mathematik – sind ziemlich gut!

- historisch: [MD5](https://en.wikipedia.org/wiki/MD5) (128 Bit, 16 Byte), [SHA1](https://en.wikipedia.org/wiki/SHA-1) (160 Bit, 20 Byte)
- [**SHA2**](https://en.wikipedia.org/wiki/SHA-2), aktuell in Verwenung (SHA-256, SHA-512)
- [SHA3](https://en.wikipedia.org/wiki/SHA-3) (seit 2015), noch nicht verbreitet (SHA3-512)


