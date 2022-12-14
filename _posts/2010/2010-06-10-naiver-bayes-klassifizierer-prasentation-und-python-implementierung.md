---
layout: post
title: "Naiver Bayes Klassifizierer (Präsentation & Python-Code)"
date: "2010-06-10"
categories: 
  - "academic"
tags: 
  - "academic"
  - "data-mining"
  - "download"
  - "programming"
  - "python"
  - "slides"
---

**Data-Mining**, also die Extraktion von Wissen aus sehr großen Datenmengen, ist ein Teilgebiet der künstlichen Intelligenz welches zunehmend an Bedeutung gewinnt. Es findet sich im Umfeld von Softwareentwicklung und Datenbanken wieder, in Erhebungen und Statistiken, in Marketing und Management. Dokumente wie Webseiten, E-Mails oder RSS-Feeds, werden geclustered und klassifiziert.

[![](images/bayes_klassifizierer_screen_shot-1024x453.png "bayes_klassifizierer_screen_shot")](http://tuhrig.de/wp-content/uploads/bayes_klassifizierer_screen_shot.png)

Der **naive Bayes** **Klassifizierer** erlaubt es, Dokumente relativ einfach nach gelernten Regeln zu klassifizieren, also in Gruppen einzuteilen. Anhand vorklassifizierter Dokumente lernt er welche Wörter, mit welcher Wahrscheinlichkeit in welcher Gruppe auftreten. Ein neues Dokument, wird dann anhand der Wahrscheinlichkeiten der einzelnen Wörter, sowie der Wahrscheinlichkeit, dass diese Klasse überhaupt auftritt eingeordnet.

Der naive Bayes Klassifizierer kann zwar keine semantische Aussage über ein Dokument treffen (eine Aussage "das Dokument A ist zu 56,743% Werbung" wäre also weder möglich noch sinnvoll, da sie mehr als subjektiv ist), jedoch kann er relativ sicher die Klasse angeben in die das Dokument am wahrscheinlichsten fällt und es dieser zuordnen. So ist der naive Bayes Klassifizierer etwa das Mittel der Wahl für Spam-Filter in E-Mail-Programmen geworden. Der Nutzer kann den Klassifizierer anlernen und an einigen Stellschrauben drehen. Hat er genügend "Wissen gelernt", so erfolgt die Einteilung in Spam und Nicht-Spam überzeugend und performant.

Anbei findet ihr eine **Präsentation** (\*.pdf) zum naiven Bayes Klassifizierer, sowie eine **kommentierte Implementierung in Python**. Die Präsentation stellt das Verfahren noch einmal im Detail vor und gibt auch Einblicke in die Mathematik - nämlich das Bayes-Theorem - hinter der Idee. Die Python-Implementierung setzt einen Bayes Klassifizierer für Dokumente (also allgemein Strings) um. Diese Implementierung lernt anhand einer Reihe von vorgegebenen Dokumenten und teilt ein neues Dokument dann in einer von zwei Klassen ein (siehe [Screen-Shot](http://tuhrig.de/wp-content/uploads/bayes_klassifizierer_screen_shot.png "http://tuhrig.de/wp-content/uploads/bayes_klassifizierer_screen_shot.png")).

**Präsentation:** [naiver\_bayes\_klassifizierer\_präsentation.pdf](https://speakerdeck.com/tuhrig/dokumenten-klassifikation-mit-dem-naiven-bayes-klassifizierer-python)

**Python-Implementierung:** [naiver\_bayes\_klassifizierer.zip](http://tuhrig.de/wp-content/uploads/naiver_bayes_klassifizierer.zip)

**Beste Grüße,** _Thomas Uhrig._

**Klassifizierer**
