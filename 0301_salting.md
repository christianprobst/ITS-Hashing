# Salting


## Unterscheidung Passworthashes und Prüfsummen

Sowohl Hashing als auch Verschlüsselung sind Methoden zum Schutz von Daten, haben aber unterschiedliche Funktionen. Die Passwortverschlüsselung wird verwendet, wenn der Klartext aus irgendeinem Grund wiederhergestellt werden muss. Das Passwort-Hashing wird typischerweise auf der Serverseite verwendet, wenn der Serverbetreiber den Klartext nicht zu kennen braucht, sondern nur nachweisen muss, dass der Benutzer den Klartext kennt. Diese Funktionen laufen, ebenso wie das Salting, im Hintergrund, ohne das Kundenerlebnis zu beeinträchtigen. [1](https://www.pingidentity.com/de/resources/blog/post/encryption-vs-hashing-vs-salting.html)


Während bei Prüfsummen der Hash immer gleich sein muss, ist das bei Passwörtern nicht gewünscht. Passworthashes sollen bei gleichen Passwörtern unterschiedliche Hashes haben.

## Was ist Salting

Salting wird als Technik zur Erhöhung der Sicherheit bei der Speicherung von Passwörtern eingesetzt. Einem Passwort wird vor dem Hashing von einem Passwwort ein zufälliger Wert (das "Salt") hinzugefügt.

## Warum ist Salting wichtig?

1. Schutz gegen Rainbow-Table-Angriffe wird durch Salting gewährleistet.
2. Brute-Force-Attacken werden durch Salting erschwert.
3. Die Einzigartigkeit identischer Passwörter wird durch Salting sichergestellt.

### Rainbow Table Angriff
Ein Rainbow Table Angriff zielt darauf ab, dass nicht Listen mit Passwörtern, sondern Listen mit Passwort hashes erstellt werden.

zb:
```
Klartext: password123
MD5 Hash: 482c811da5d5b4bc6d497ffa98491e38
```

Wenn aus einer Liste mit Passwörtern im Klartext eine Datei mit den Hashes der Passwörter erstellt wird, ist das eine Rainbow Table.


## Funktionsweise

Jedem dieser Passwörter wird ein zufälliger Wert ergänzt.

Durch das Hinzufügen eines Salt zum Kennwort „Password“ würde beispielsweise ein „gesalzenes“ Kennwort wie „4(j3Li95Password“ oder „Password4(j3Li95“ generiert. Nach dem Ergänzen des Salt wird die Kombination aus Klartext-Passwort und Salt gehasht und dadurch sicherer, als wenn nur das ursprüngliche Passwort gehasht wird.

Der Salt soll mindestens eine Länge von 32Byte haben.

Das Passwort-Hashing wird typischerweise auf der Serverseite verwendet, wenn der Serverbetreiber den Klartext nicht zu kennen braucht, sondern nur nachweisen muss, dass der Benutzer den Klartext kennt. 


```py
def hash_password(password):
# Ein 32 Byte Salt wird generiert.
salt = os.urandom(32)
password_bytes = password.encode('utf-8')
salted_password = salt + password_bytes
hashed_password = hashlib.sha256(salted_password).hexdigest()
return salt, hashed_password

def verify_password(stored_salt, stored_hash, input_password):
input_password_bytes = input_password.encode('utf-8')
salted_input = stored_salt + input_password_bytes
input_hash = hashlib.sha256(salted_input).hexdigest()
return input_hash == stored_hash
```

## Wo ist der Salt Wert zu finden
der Salt Wert ist in der `/etc/shadow` Datei zu finden:

``` 
$Methode$Salt$Passwort
```



## Vorteile des Saltings:

- Eine erhöhte Sicherheit gegen verschiedene Angriffsmethoden wird durch Salting erreicht.
- Eine individuelle Sicherheit für jedes Passwort wird durch Salting gewährleistet.
- Salting wird relativ einfach implementiert.

## Nachteile des Saltings:

- Der Speicherbedarf für Salt-Werte wird erhöht.
- Die Rechenzeit bei der Passwortüberprüfung wird leicht erhöht.

## Best Practices:

1. Kryptografisch sichere Zufallszahlengeneratoren werden für Salts verwendet.
2. Eine ausreichende Salt-Länge (mindestens 32 Bytes) wird gewählt.
3. Für jedes Passwort wird ein einzigartiges Salt generiert.
4. Salt und Hash werden getrennt vom Klartext-Passwort gespeichert.

## Zusammenfassung:

Salting wird als wichtige Sicherheitsmaßnahme bei der Passwort-Speicherung eingesetzt. Es schützt vor verschiedenen Angriffsszenarien und sollte in jeder modernen Anwendung implementiert werden, die Benutzerpasswörter verwaltet.
