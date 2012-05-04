% Vorlesung "Sicherheit"
%
% 3. Mai 2012

# Schlüsselaustausch

Verschlüsselung und Authetifikation benötigen gemeinsamen geheimen Schlüssel.

## 3-Pass (Schlüssel-/Nachrichtenaustausch mit mittelalterlichen Mitteln)

* Kiste mit Schloss von Alice zu Bob. Bob befestigt sein eigenes Schloss an der Kiste.
* Kiste mit Schloss von Alice und Bob zurück zu Alice. Alice nimmt ihr Schloss ab.
* Kiste mit Schloss von Bob zurück zu Bob.

*Achtung*: Man-in-the-Middle! Der Bote befestigt ein eigenes Schloss, Bob bekommt die Kiste niemals zu sehen.

Abhilfe: schwer fälschbare Siegel auf dem Schloss. Anmerkung aus dem Publikum: Verschiedene Boten verwenden ("separation of duties").

*Idee*: Nachrichtentransfer über einen authentifizierten aber nicht abhörsicheren Kanal mittels One-Time-Pad (OTP).

* Alice schickt ihre Nachricht mit ihrem OTP zu Bob.
* Bob addiert seinen OTP, schickt die Nachricht zurück zu Alice.
* Alice subtrahiert ihren OTP, schickt die nur noch mit dem OTP von Bob verschlüsselte Nachricht zu Bob zurück.

*Problem*: Addiert man alle verschickten Nachrichten, so ergibt sich die Ausgangsnachricht.

## White-Mouth-Frog

Jeder Teilnehmer hat einen gemeinsamen Schlüssel mit der Zentrale. Alice schickt ein verschlüsseltes Tupel mit Timestamp, Bobs Name und dem gewünschten Schlüssel an die Zentrale, die die Nachricht entschlüsselt, für Bob neu verschlüsselt und an Bob weiterleitet.

Nachteile:

* Benötigt eine vertrauenswürdige Zentrale - "single point of failure"!
* Möglicher "denial of service"-Angriff gegen die Zentrale durch viele Verbindungsaufbauten.
* Alice benötigt einen guten Zufallsgenerator.

## Kerberos

Jeder Teilnehmer hat einen gemeinsamen Schlüssel mit der Zentrale.

* Alice teilt der Zentrale mit, dass sie Alice ist und gerne mit Bob kommunizieren würde: $(alice, bob)$.
* Zentrale an Alice: $Enc_{K_{AZ}} (T_Z, L, K_{AB}, bob)$, $Enc_{K_{BZ}} (T_Z, L, K_{AB}, alice)$
	$L$ bezeichnet die Gültigkeitsdauer, $T_Z$ den Timestamp, $K_{AB}$ den Session-Key für die Kommunikation zwischen Alice und Bob.
* Alice an Bob: $Enc_{K_{AB}} (alice, T_A)$, $Enc_{K_{BZ}} (T_Z, L, K_{AB}, alice)$
* Bob schickt an Alice ein Ackowledgement: $Enc_{K_{AB}} (T_A + 1)$

Probleme:

* benötigt synchrone Uhren.
* Chriffre muss "non-malleable" (nicht-biegbar) sein, sonst eventuell $Enc_{K_{AB}} (T_A + 1)$ aus $Enc_{K_{AB}} (T_A)$ berechenbar.

	confidentiality $\not \Rightarrow$ non-malleability!

* Needham-Schroeder (ältere Version des Verfahrens) hatte noch keine Zeitstempel, sondern Zufall $\implies$ Replay-Attacken möglich. Beispiel: Alice ist eine Kamera und der Angreifer spielt alte Aufnahmen wieder ein, um Bob zu betrügen.
