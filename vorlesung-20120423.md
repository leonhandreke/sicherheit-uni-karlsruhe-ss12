% Vorlesung "Sicherheit"
%
% 23. April 2012

# Reijndael-Algorithmus des AES-Standards

* 128 Bit Blocklänge
* Schlüssellänge von 128, 192 und 256 Bit möglich
* 10, 12 oder 14 Runden
* State und Rundenschlüssel werden als $4{\times}4$-Byte-Matrix repräsentiert
* jede Runde läuft in 4 Schritten ab
	1. Byte Substitution
		a) fasse Bytes als Elemente von $\mathbb{F}_{2^8}$ auf und invertiere falls $\neq 0$
		b) affine Transformation $y = Ax + b$ mit konstantem, invertierbarem $A \in \{0, 1\}^{8{\times}8}$, $b \in \{0, 1\}^8$.

	2. Shift Row

		Zyklische Rotation der Zeilen.

	3. Mix Column

		Lineare Transformation $y = \tilde{A}x$ mit konstantem, invertierbarem $\tilde{A} \in {\mathbb{F}_{2^8}}^{4{\times}4}$.

	4. Key Addition

		Bitweise XOR mit Rundenschlüssel.

* Reijndael ist nicht der sicherste der für den AES-Standard vorgeschlagenen Algortihmen, ist jedoch (im Gegensatz beispielsweise zum DES) performant in Software implementierbar.

# Betriebsmodi

## ECB (Electronic Codebook Mode)

Nachteile:

- Gleiche Klartextblöcke werde auf gleiche Chiffratblöcke abgebildet, wodurch die "Struktur" des Klartextes sichtbar werden kann (z.B. bei Bildern).
- Angreifer kann Blöcke vertauschen, löschen, duplizieren...

Vorteile:

- Übertragungsfehler (Bitflips) auf betroffenen Block begrenzt.
- perfekt parallelisierbar.
- verschlüsselte Blockspeicher blockweise bearbeitbar.

## CBC (Cipher Block Chaining Mode)

Vorteile:

- Nachteile von ECB beseitigt.
- Entschlüsselung ist selbstsynchronisierend.

	Klartextblock wird bei der Entschlüsselung nur aus den letzten zwei Chiffratblöcken berechnet.

	($\Rightarrow$ wahlfreier Lesezugriff bei Blockspeicher)

Nachteile:

- geringer Bandbreitenverlust, da Initialisierungsverkor übertragen werden muss.
- Fehler breiten sich auf einen weiteren Block aus.

## OFB (Output Feedback Mode)

Vorteile:

- Entschlüsselung muss nicht effizient sein, da nur "vorwärts" verschlüsselt wird.
- keine Fehlerfortpflanzung bei Bitflips
- pseudozufälliger Schlüsselstrom vorberechenbar (sinnvoll bei "harten" Zeitbeschränkungen).

Nachteile:

- gleicher IV $\Rightarrow$ XOR von Chiffraten = XOR von Nachrichten.

	$\Rightarrow$ es muss jedes mal ein neuer IV gewählt und übertragen werden.

- nicht robust gegen Verlorengehen ganzer Blöcke.
- Angreifer kann gezielt Klartextbits kippen.

## CTR (Counter Mode)

Nachteile identisch mit OFB.

Vorteile (im Wesentlichen OFB $\cup$ ECB):

- gut parallelisierbar.
- Pseudozufallsstrom vorberechenbar (wie bei OFB).
- Fehler auf betroffene Blöcke begrenzt.
- wahlfreier Zugriff auf verschlüsselten Speicher.

