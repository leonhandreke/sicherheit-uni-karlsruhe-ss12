% Vorlesung "Sicherheit"
%
% 24. Mai 2012

# PKI (Public Key Infrastructure)

Aufgabe einer PKI sind z.B. die Ausstellung, Verteilung, Prüfung und der Widerruf digitaler Zertifikate.

## Beispiel: X.509 Zertifikate

Ein solches Zertifikat enthält:

1. Versionsnummer (v1, 2, oder 3)
2. Seriennummer, die von der CA gewählt wird. Innerhalb einer CA sollte diese Seriennummer eindeutig sein.
3. Für die Signatur des Zertifikates verwendeter Signaturalgorithmus
4. "Distinguished Name" (DN) des Ausstellers
5. Gültigkeitsdauer
6. DN des Inhabers
7. Public Key Information: Algorithmus, Parameter, $pk$
8. Eventuelle Erweiterungen durch die Versionsnummer definiert
9. Signatur auf 1 - 8.


## Certificate Revocation

Die Widerrufung von Zertifikaten wird normalerweise mit Hilfe von Certificate Revocation Lists (CRLs) erreicht. Eine CA publiziert eine Liste aller widerrufenen Zertifikate (die noch nicht abgelaufen sind) sowie eine Gültigkeitsdauer der publizierten CRL.

Um den Status eines einzigen Zertifikates anzufragen, verwendet man heute oft das Online Certificate Status Protocol.

## Web of Trust

Als "Web of Trust" bezeichnet man eine art "social network" von mini-CAs. Diese vernetzen sich auf Key Signing Partys. Jeder kann selber festlegen welcher mini-CA (bzw. welchem anderen User) er vertraut. Im Einzelfall sind die CAs unsicher, Angriffe skalieren jedoch nicht gut.

## TLS (Transport Layer Security)

TLS ist ein Standard im Internet und der Nachfolger von SSL.

1. Client $\rightarrow$ Server: "Hello", Parameter, Zufallszahl
2. Client $\leftarrow$ Server: Parameter, Zufallszahl, Zertifikat. Optional: Zertifikatsanfrage an den Client
3. Client $\rightarrow$ Server: $c = {Enc}_{{pk}_{Server}}(\text{pre master secret})$
4. Client $\rightarrow$ Server: Signierte Daten, die den Protokollauf eindeutig machen und ein Zertifikat
5. Client $\rightarrow$ Server: "Finish" (authenticated, encrypted)
6. Client $\leftarrow$ Server: "Finish" (authenticated, encrypted)

Weitere Features von TLS sind:

* "Key Renegotiation", welche das Verfahren neu startet, kann jederzeit beantragt werden
* Die Nachricht "Finish" entählt einen Hash aller bisherigen Protokollnachrichten der Session
* Die verwendeten pseudozufällige Funktion telt den Input in zwei Hälften und hasht mit MD5 und SHA1 und XOR'd die Ergebnisse. Die Sicherheit bleibt (teilweise) erhalten, auch wenn beispielsweise MD5 Schwächen zeigt. Man spricht von einem "Robust Combiner".


## Key Renegotiation Attacks

Müller-Quade:

> "Wir kommen zum Höhepunkt des heutigen Tages!"

Diese Art von Attacke wurde 2009 entdeckt.

1. Client $\rightarrow$ Server: TLS Handshake 1. Dieses Paket wird vom Angreifer abgefangen und gehalten.
2. Angreifer $\leftrightarrow$ Server: TLS Handshake 2
3. Angreifer $\leftrightarrow$ Server: Application Layer Commands, die den Server in einen speziellen Zustand bringen.
4. Angreifer $\rightarrow$ Server: Trigger Renegotiation
5. TLS Handshake 1 wird nun innerhalb der verschlüsselten Session 2 weitergeführt.

Der Client kommuniziert nun verschlüsselt mit dem Server, der Angreifer kann also im weitern Verlauf der Session nicht mitlesen oder manipulieren. Allerding ist der Anwedungskontext möglicherweise zum Nachteil des Clients manipuliert worden.

