% Vorlesung "Sicherheit"
%
% 7. Mai 2012

# Schlüsselaustausch - ein Wunder?

*Frage*: Kann es sein, dass Alice und Bob in einem Kaffee Sitzen und sich hörbar unterhalten und es trotzdem schaffen, einen Schlüssel auszuhandeln?

## Merkle Puzzle

* Alice wählt zufällig k Paare von Schlüssel und zufälliger Schlüsselnummer. Sie verschlüsselt diese Paare mit einer symmetrischen Chiffre mit einem immer neuen Schlüssel. Sie schickt die verschlüsselten Paare an Bob und gibt Bob hinreichend viele Hinweise, damit er ein zufällig gewähltes verschlüsseltes Paar "knacken" kann.
* Bob schickt die Schlüsselnummer seines "geknackten" Schlüssels im Klartext an Alice.

*Vorteil*: Ein Angreifer weiß nicht, welcher der Schlüssel der verwendete ist (da die Schlüsselnummern von Alice zu Bob auch mit verschlüsselt werden) und muss deshalb jedes der verschlüsselten Paare brechen (bis er den verwendeten Schlüssel gefunden hat).

Der "Vorteil" von Alice und Bob gegenüber einem Lauscher ist leider nur gering.

## Diffie-Hellman Schlüsselaustausch (DH)

Gegeben sei eine (öffentlich bekannte) Gruppe $G$ und ein Erzeuger $g$ dieser Gruppe, etwa ein ${\mathbb{F}_p}^{\times}$ oder eine Gruppe auf einer Elliptischen Kurve.

* Alice wählt $a$ zufällig und schickt $g^a$ an Bob.
* Bob wählt $b$ zufällig und schickt $g^b$ an Alice.

Nun kennen beide $g^{ab} = {(g^a)}^b = {(g^b)}^a$ und können es als Schlüssel verwenden.

DH ist sicher (sogar beweisbar sicher) relativ zur Decisional DH-Annahme. (Müller-Quade: "Die Decisional DH-Annahme ist, ja, ich geb's zu, im wesentlichen die Annahme, dass Diffie-Hellman sicher ist.").

**Decisional Diffie-Hellman-Annahme**: Asymptotisch ist es nicht mit Polynomialzeit möglich, folgende Verteilungen zu unterscheiden: $(g, g^a, g^b, g^{ab})$, $(g, g^a, g^b, g^z)$, wobei $a$, $b$ und $z$ zufällig gewählt sind.

*Achtung*: "Man in the Middle"-Angriff möglich! Zu verhindern durch:

1. symmetrische Authentifikation (mit einem Langzeit-Geheimnis)
2. digitale Signaturen

# Public-Key Cryptography (PKK)

Öffentlicher Schlüssel von Alice $g^a$ (geheimer Schlüssel $a$) Nun kann man $m$ verschlüsseln, indem man $g^b$ zufällig wählt und dann $(g^b, g^{ab} \oplus m)$. Diffie und Hellman waren sehr kurz davor, sind jedoch nicht auf die Idee gekommen, ein zufälliges $g^b$ zu wählen.

## RSA

Public Key: $(e, N)$, Secret Key: $k$

Verschlüsselung: $c = m^e \mod N$

Entschlüsselung: $m = c^d \mod N = {(m^e)}^d = m^{ed} = m \mod N$

*...hier fehlt einiges an Information darüber, wie $e$ und $d$ zu wählen sind und warum das so ist.*

**RSA-Annahme**: $x \mapsto x^e \mod N$ ist ohne Zusätzliche Information schwierig zu invertieren.

*Möglicher Angriff*: Aktiver Angreifer

Alice und Bob nehmen beide an einer Aukion teil.

* Alice verschickt ihr verschlüsseltes Gebot $c = m^e \mod N$
* Bob verschickt sein verschlüsseltes Gebot $c' = 2^e c \mod N$
* Der Auktionär entschlüsselt $c' = 2^ec = 2^em^e$ zu $2m$ $\Rightarrow$ Bob gewinnt die Auktion!

Deshalb: RSA nicht so wie in der Vorlesung beschrieben naiv verwenden!
