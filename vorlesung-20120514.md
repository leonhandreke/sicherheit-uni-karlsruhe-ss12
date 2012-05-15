% Vorlesung "Sicherheit"
%
% 14. Mai 2012

# Sicherheitsbegriffe

Definition von "Sicherheit" muss sorgfältig gewählt sein. So wurde beispielsweise die Enigma so konstruiert, dass niemals ein Zeichen auf sich selbst abgebildet werden konnte. Dies wurde damals als "sicher" empfunden, ist jedoch eigentlich eine Abschwächung der Chiffre.

## Begriffsdefinitionen

Eine Wahrscheinlichkeit $\eta$ heißt vernachlässigbar, wenn für jedes $n$ ein $k_0$ existiert, so dass für alle $k \ge k_0$ gilt: $0 \leq {\eta}(k) < \frac{1}{k^n}$.

Eine Wahrscheinlichkeit  $p = 1 - \eta$ mit $\eta$ vernachlässigbar nennen wir überwältigend.

\newpage

## IND-CCA2-Sicherheit für Public-Key-Kryptographieverfahren

Ein Public-Key-Kryptographieverfahren besteht aus 3 Algorithmen:

* ${KeyGen}(1^k) \mapsto (pk, sk)$
* ${Enc}(m, pk, 1^k) \mapsto c$
* ${Dec}(c, sk, 1^k) \mapsto m$

All diese Algorithmen sollten polynomielle Laufzeit besitzen. Der Sicherheitsparameter $1^k$ wird stets in unärer Kodierung übergeben, damit die Laufzeit der Algorithmen polynomial in der Läge der Sicherheitsparameters ist (nicht etwa polynomial in der Länge der raffiniertesten Darstellung des Sicherheitsparameters).

Wir fordern für die Algorithmen Korrektheit, d.h. die Wahrscheinlichkeit, dass $Dec(Enc(m, pk, 1^k), sk, 1^k) = m$ ist, ist überwältigend groß.

Experiment ${Exp}_{ind-cca-b}(k)$ mit einem IND-CCA2-Angreifer $A$ und einem Schema $S$:

1. $(pk, sk) \mapsfrom {KeyGen}(1^k)$

2. $m_0, m_1, s \mapsfrom A^{{Dec}(., sk, 1^k)}(1^k)$

	Der Angreifer $A$ gibt zwei von ihm gewählte Challenges $m_0$ und $m_1$ aus.

3. $\tilde{b} \mapsfrom A^{{Dec}(., sk, 1^k)}({Enc}(m_b, pk, 1^k), s, 1^k)$

	Der Angreifer $A$ erhält eine der Challenges $m_b$ verschlüsselt und muss "erraten", um welches $b$ es sich handelt.

4. $\texttt{return}$ $\tilde{b}$

$A^{{Dec}(., sk, 1^k)}$ bezeichnet einen Angreifer $A$, der ein Entschlüsselungsorakel verwenden darf, welches jedes Chiffrat, nicht jedoch die Chiffrate der Challenges $m_0$ und $m_1$ entschlüsseln kann.

${Adv}(k) = |\underbrace{P[{{Exp}_{ind-cca-0}}^{A, S} \mapsto 1]}_\text{richtig Raten} - \underbrace{P[{{Exp}_{ind-cca-1}}^{A, S} \mapsto 1]}_\text{falsch Raten}|$

Ein Verfahren $S$ heißt nun IND-CCA-sicher, wenn für alle PPT-Algorithmen $A$ der Vorteil $Adv(k)$ vernachlässigbar ist.

### RSA-ES-OAEP

Dieses Verfahren wird verwendet, um RSA in der Praxis zu implementieren und ist beweisbar IND-CCA2-sicher. Man benötigt eine Nachricht $m$, einen Zufallsstring $r$ sowie zwei perfekte Hashfunktionen. Das Verfahren macht aus dem deterministischen Verfahren RSA durch die hinzunahme eines Zufallsstrings $r$ ein probablistisches Verfahren.

## Sicherheitsbegriffe für Digitale Signaturen

Man will gerne haben: **Authentizität** (einer Person eindeutig zugeordnet), **Integrität**, **Unabstreitbarkeit (non-repudiation)**, **Praktikabilität** (kurz im Vergleich zum Dokument). Dies ist kein formaler Sicherheitsbegriff!

### Signieren mit RSA

Man entschlüsselt das Dokument als wäre es ein Chiffrat. Dies kann (hoffentlich) nur der Besitzer von $sk$. Prüfen kann aber jeder, der $pk$ kennt. "Verschlüsseln" der Signatur ergibt die Nachricht. Signatur $\varsigma$ zu $m$ wäre dann $m^d \mod N = \varsigma$ und überprüfbar mit ${\varsigma}^e mod N \stackrel{?}{=} m$.

**Probleme**:

1. Ein zufälliges $r$ wirkt wie eine gültige Signatur von $r^e \mod N$.  So kann man jemandem unterstellen, $r^e \mod N$ signiert zu haben. Um dies zu widerlegen muss er seinen $sk$ aufgeben.
2. Zu einer gegebenen Signatur lässt sich ohne $sk$ ein passendes Dokument konstruieren.

**Abhilfe**: Hash-then-Sign, denn das Invertieren der Hashfunktion ist zu schwierig. Diese Methode ist beweisbar sicher im Random Oracle Model.

### EUF-CMA (Existential Unforgeability under Chosen Message Attack)

*EUF-CMA* ist ein Sicherheitsbegriff für Digitale Signaturen. Man definiert für ein Signaturverfahren hat die folgenden Komponenten:

* Verifcation Key $vk$, Signature Key $sk$. Nicht jedes Verfahren muss Public-Key-Cryptography-basiert sein, deshalb werden "generische" Bezeichnungen verwendet.

* ${KeyGen}(1^k) \mapsto (vk, sk)$

* ${Sign}(m, sk, 1^k) \mapsto \varsigma$

* ${Verify}(\varsigma, m, vk, 1^k) \in \{valid, invalid\}$

Wir verlangen Korrektheit, d.h. ${Verify}({Sign}(m, sk, 1^k), m, vk, 1^k) = valid$ gilt mit überwältigender Wahrscheinlichkeit.

"Existential Unforgeability" bedeutet, dass das Verfahren bereits als gebrochen betrachtet wird, wenn ein valides Nachricht-Signatur-Paar gefunden wird. Der Inhalt der Nachricht wird dabei vom Angreifer bestimmt ("Chosen Message").

Experiment ${{Exp}_{S, A}}^{enf-cma}(k)$ mit einem EUF-CMA-Angreifer $A$ und einem Schema $S$:

1. $(vk, sk) \mapsfrom {KeyGen}(1^k)$

2. $(m, \varsigma) \mapsfrom A^{{Sign}(., sk, 1^k)}(vk, 1^k)$, wobei $m$ keine vom Orakel signierte Nachricht sein darf.

3. $\texttt{return}$ $Verify(\varsigma, m, vk, 1^k)$

$A^{{Sign}(., sk, 1^k)}$ bezeichnet dabei einen Angreifer $A$, der ein Signaturorakel verwenden darf, welches jede Nachricht außer der vom Angreifer zur Ausgabe gewählten Nachricht $m$ signiert.

Ein Schema S heißt EUF-CMA-sicher, falls für alle PPT-Algorithmen A gilt: $P[Exp_{S,A}^{euf-cma}(k) \mapsto valid]$ ist vernachlässigbar.
