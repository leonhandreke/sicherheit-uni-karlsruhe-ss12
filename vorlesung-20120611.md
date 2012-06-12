% Vorlesung "Sicherheit"
%
% 11. Juni 2012

# Zugriffskontrolle

## Bell-LaPadula-Modell

### Simple Security Property (ss-Eigenschaft)

Ein Zustand $(b, M, f)$ genügt des ss-Eigenschaft, falls für alle $(s, o, a) \in b$ mit $a \in \{read, write\}$ gilt: $f_s(s') \geq f_o(o')$ (no read up).

### Star Property (*-Eigenschaft)

Ein Zustand $(b, M, f)$ erfüllt die *-Eigenschaft, falls für jedes $(s', o', a) \in b$ mit $a \in \{append, write\}$ gilt: $f_c(s') \leq f_o(o')$ (no write down).

Weiterhin, falls ein $(s', o', a) \in b$ mit $a \in \{append, write\}$ und ein $(\wedge{a}, \wedge{o}, \wedge{a}) \in b$ mit $s\ = \wedge{s}$ und $\wedge{a} \in \{read, write\}$ existiert, dann  muss $f_o(\wedge{o}) \leq f_o(o')$ gelten, d.h. kein Nachrichtenfluss von high level object zu low level object.

### Discretionary Security Property (ds-Eigenschaft)

Ein Zustand $(b, M, f)$ erfüllt die ds-Eigenschaft, fals für $(s', o', a) \in b$ stets $a \in M_{s'o'}$ gilt.

## Basic Security Theorem

Müller-Quade:

> "Bevor jetzt jemand behauptet, das wäre doch alles trivial, man kann es auch falsch machen."

Werden ausgehend von einem sicheren Initialzustand nur sichere Übergänge durchgeführt, so erhält man einen sicheren Systemzustand.

### Bewertung des BLP-Modells

**Nachteile**:

* Leider sammelt sich Information "oben", ein Deklassifizieren ist nicht möglich. In der Praxis werden etwa "trusted subjects" eingeführt, um ein Deklassifizieren von Daten zu erlauben.
* Integrität der Daten wird nicht mitbetrachtet. Niedrigstufige Prozesse/User können eventuelll höher eingestufte Objekte verändern.
* Keine Forderung an die ACM, etwa darf allen $s \in S$ alle Rechte gegeben werden.
* Verdeckte Kanäle, z.B. die Existenz oder auch Nichtexistenz von Dateien bleibt unberücksichtigt.

**Vorteile**:

* formal
* handhabbar

	Müller-Quade:

	> "Man kann damit tatsächlich Entscheidungen fällen, wenn man irgendein fluffiges, kompliziertes System hätte, wäre das vielleicht nicht der Fall."

## Chinese-Wall-Modell

Zugriffsrechte hängen von der Vergangenheit ab. Dies unterbindet einen Informationsfluss zwischen konkurrierenden Firmen. Gegeben ist:

* Menge $C$ von Firmen
* Objektmenge $O$ (jedes Objekt gehört einer Firma, über die Funktion $y$ abfragbar)
* Subjektmenge $S$ (die Berater)
* Funktion $y: O \mapsto C$, welche jedem Objekt die zugehörige Firma zuordnet
* Funktion $x: O \mapsto \mathcal{P}(e)$ welche jedem Objekt eine "conflict of interest"-Klasse zuordnet.

Das Security Label eines Objektes ist das Tupel $(x(o), y(o))$. Im Falle $x(o) = \emptyset$spricht man von "sanitized information".

Eine Matrix $M$ enthält Information über Zugriffe: $M = ( m_{so} )_\{s \in S, o \in O\}$ mit $( m_{so} ) = true$ falls $s$ Zugriff auf $o$ hatte und $( m_{so} ) = false$ sonst. **TODO**: Schöne Klammer über zwei Zeilen für die Definition. Der Initialzustand ist $M = {( false)}_{s \in S, o \in O}$

### Simple Security Property (ss-Eigenschaft)

$s \in S$ erhält Zugriff auf $o \in O$ nur falls für alle $o' \in O$ mit $M_{so'} = true$ gilt: $y(o_ = y(o')$ oder $y(o) \not \in x(o')$.

### *-Eigenschaft

Ein $s \in S$ erhält Schreibzufriff auf ein Objekt $o \in O$ nur, falls $s$ aktuell keinen Lesezugriff auf $o\ \in O$ hat mit $y(o) \neq y(o')$ und $x(o') \neq \emptyset$

Die *-Eigenschaft verhindert die Weitergabe von Firma $A$ (der $a$ gehört) and Firma $C$ (der $c$ gehört) über eine dritte Firma $B$. Ein potentielles Weitergabeszenario ohne die *-Bedingung wäre:

1. $s \in S$ liest $a$ und schreibt nach $b$
2. $s' \in S$  liest $b$ und schreibt auf $c$
