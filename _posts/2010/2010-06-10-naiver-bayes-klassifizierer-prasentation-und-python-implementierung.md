---
layout: post
title: "Naiver Bayes Klassifizierer (Präsentation & Python-Code)"
date: "2010-06-10"
categories: 
  - "academic"
  - "data-mining"
  - "python"
  - "slides"
---

[Data-Mining](https://en.wikipedia.org/wiki/Data_mining), also die Extraktion von Wissen aus großen Datenmengen, gewinnt zunehmend an Bedeutung. 
Es findet sich im Umfeld von Softwareentwicklung und Datenbanken wieder, in Erhebungen und Statistiken, im Marketing und Management. 
Dokumente wie Webseiten, E-Mails oder RSS-Feeds, werden geclustert und klassifiziert.

![](/images/2010/06/bayes_klassifizierer.png)

## Der naive Bayes-Klassifizierer

Der naive Bayes-Klassifizierer erlaubt es, Dokumente nach gelernten Regeln zu klassifizieren, also in Gruppen einzuteilen. 
Anhand vorklassifizierter Dokumente lernt er welche Wörter, mit welcher Wahrscheinlichkeit in welcher Gruppe auftreten. 
Ein neues Dokument, wird anhand der Wahrscheinlichkeiten der einzelnen Wörter, sowie der Wahrscheinlichkeit, dass diese Klasse überhaupt auftritt eingeordnet.

Der naive Bayes Klassifizierer kann zwar keine semantische Aussage über ein Dokument treffen 
(eine Aussage "_das Dokument A ist zu 56,743% Werbung_" wäre also weder möglich noch sinnvoll, da sie mehr als subjektiv ist), 
jedoch kann er relativ sicher die Klasse bestimmen, in die das Dokument am wahrscheinlichsten fällt. 
So ist der naive Bayes Klassifizierer etwa das Mittel der Wahl für Spam-Filter in E-Mail-Programmen geworden. 
Der Nutzer kann den Klassifizierer anlernen und an einigen Stellschrauben drehen. 
Hat er genügend "Wissen gelernt", so erfolgt die Einteilung in _Spam_ und _Nicht-Spam_ überzeugend und performant.

## Präsentation

Anbei findet ihr eine Präsentation zum naiven Bayes Klassifizierer, sowie eine kommentierte Implementierung in [Python](https://www.python.org/). 
Die Präsentation stellt das Verfahren im Detail vor und gibt Einblicke in die Mathematik - nämlich das [Bayes-Theorem](https://en.wikipedia.org/wiki/Bayes%27_theorem) - hinter der Idee. 
Die Python-Implementierung setzt einen Bayes-Klassifizierer für Dokumente (also `Strings`) um. 
Die Implementierung lernt anhand einer Reihe von vorgegebenen Dokumenten und teilt ein neues Dokument dann in eine von zwei Klassen ein.

<iframe
name="thirdPartyContent"
class="speakerdeck-iframe"
frameborder="0"
allowfullscreen="true"
mozallowfullscreen="true"
webkitallowfullscreen="true"
data-ratio="1.3333333333333333">
</iframe>

<script>
if(consentGiven()) {
    const site = "https://speakerdeck.com/player/4fe497da4252c8001f0075e4";
    document.getElementsByName('thirdPartyContent')[0].src = site;
}
</script>

- **Präsentation:** [Naiver_Bayes_Klassifizierer.pdf](/assets/pdf/Naiver_Bayes_Klassifizierer.pdf)
- **Python-Implementierung:** [naiver_bayes_klassifizierer.zip](/assets/zip/naiver_bayes_klassifizierer.zip)

**Beste Grüße,** Thomas.

