% Vorlesung "Sicherheit"
%
% 18. Juni 2012

# Authentifikation

Authentifizierung bindet eine Identität an ein Subjekt.

## Ansätze

* Entität weiß etwas (Kennwort)
* Entität ist etwas (Biometrie)
* Entität besitzt etwas (Chipkarte)
* Entität kann etwas (Captcha)
* Entität befindet sich an einem bestimmten Ort

## Komponenten

* Menge $A$ der Authentifizierungsinformationen (Information, mit der Identität bewiesen wird)
* Menge $C$ der Komplementärinformationen (was das System speichert, um Authentifikation zu validieren)
* Menge $\mathcal{F} \subseteq C^A$ der der Komplemetierungsfunktionen (leitet aus gegebenem $a \in A$ das entsprechende $c \in C$ ab)
* Menge $L \subseteq {\{true, false\}}^{A \times C}$ der Authentifikationsfunktion (verifiziert Identifikation)
* Menge $S$ der Auswahlfunktionen (zum Anlegen, Ändern, Entfernen von Entitäten und der entsprechenden Daten)


## Typische Anwendung: Kennworte

**Ansatz**: Das System speichert Kennworte explizit.
*Problem*: Diebstahl der Datei mit Passwörtern.

**Ansatz**: Kryptographische Hashwerte speichern.
*Problem*: Offline Wörterbuch-Attacke sehr effizient.

**Ansatz**: Pro Benutzer wird eine andere Hashfunktion verwendet. Dies kann z.B. durch Salting erreicht werden: $H_s(pw) = H(pw || s)$.

**Ansatz**: "Remote-Login" mit dediziertem Authentifizierungsserver, z.B. oAuth.

### Sichere Passwörter

Daniel Kraschewski:

> "Das ist alles irgendwie trivial, gibt nicht viel Einsicht."

### Maßnahmen gegen Offline-Attacken

* Back-off: Nach $n$ Fehleinkaben Sperrung für $x^n$ Sekunden.
* Disconnection: Nach $n$ Fehleingaben Verbindungstrennung.
* Jailing: Begrenzter Zugriff wird trotz Fehleingabe gewährt. Oft mit Vorgabe vermeitlich attraktiver Ziele (Honeypots).


## Raffiniertere Verfahren: Challenge-Response

Der Benutzer hat ein Geheimnis $s$.

* System $\rightarrow$ User: zufällige Challenge $c$
* System $\leftarrow$ User: $r(c, s)$

Das Geheimnis $s$ soll dabei nicht aus $(c, r(c, s))$ rekonstruierbar sein, selbst bei schlau gewähltem $c$.

### RSA-Signaturen

Eigentlich toll, aber in Praxi manchmal zu aufwändig.

### Hasfunktion abhängig vom Geheimnis

$r(s, c) = Hash(s, c)$ oder $r(s, c) = Enc_s(c)$.

### Zero-Knowledge-Beweise

In Praxi meist viel zu aufwändig.

### SPEKE (Simple Password Encrypted Key Exchange)

* Am Ende haben beide Parteien als Nebeneffekt noch einen gemeinsamen Schlüssel.
* Basiert auf Diffie-Hellman
* Parameter:
	* $p = 2q + 1$ (safe prime)
	* Hashfunktion $H$
	* $g = {H(Passwort)}^2 \mod p$, $g$ erzeugt dann die Gruppe der "quadratischen Reste $\mod p$"
* Ablauf:
	* $A \rightarrow B$: $g^a$
	* $A \leftarrow B$: $g^b$
	* $k := H(g^{ab})$
* Key Confirmation:
	* $A \rightarrow B$: $H(H(k))$
	* $A \leftarrow B$: $H(k)$
	* Formal besser: $g^{ab} + 1$ statt $k$
* Problem: Wenn $A$ sich beispielsweise $a = 0$ wählt, kann sich $A$ ohne Passwort einloggen. Deshalb: Protokollabbruch, falls $ord(g^{ab}) < q$.
