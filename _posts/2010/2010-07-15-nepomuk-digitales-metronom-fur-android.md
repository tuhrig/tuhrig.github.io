---
layout: post
title: "Nepomuk - digitales Metronom für Android-Geräte"
date: "2010-07-15"
categories: 
  - "academic"
  - "android"
  - "java"
  - "coding"
  - "slides"
---

Im den vergangenen Monaten, habe ich mich im Rahmen eines Pflichtprojekts in meinem Studium, mit der Entwicklung von Software auf der Android-Plattform auseinandergesetzt. Entstanden ist dabei **Nepomuk**, ein digitales Metronom mit Sprache für die Android-Plattform (ab 1.6).

[](http://tuhrig.de/wp-content/uploads/nepomuk_logo.png)[](http://tuhrig.de/wp-content/uploads/nepomuk_logo1.png)[![](images/nepomuk_logo2-300x73.png "nepomuk_logo")](http://tuhrig.de/wp-content/uploads/nepomuk_logo2.png)

Die Idee zu diesem Projekt stammte von **Michiel Oldenkamp** ( [www.musithek-oldenkamp.eu](http://www.musithek-oldenkamp.eu) ), einem langjährigen und professionellen Musiker. Dieser stellte während seinen Unterrichtsstunden häufiger fest, dass das Ticken eines Metronoms alleine nicht ausreiche. Viel mehr benötigten die Schüler einen ständigen Nebensitzer, zum permanenten Vorzählen des Taktes. Daraus entstand die Idee ein digitales Metronom für ein mobiles Endgerät zu entwickeln, welches neben dem übliche Ticken, über eine Sprachausgabe verfügt.

Nepomuk implementiert insgesamt sieben Sprachen, drei verschiedene Klangschemata und eine Tapper-Funktion zur Berechnung des Tempos. Eine **Präsentation als Videoaufzeichnung**, ist unter [http://events.mi.hdm-stuttgart.de/](http://events.mi.hdm-stuttgart.de/ "Events.mi") zu finden. Außerdem habe ich das Projekt auch auf der diesjährigen Media Night an der HdM Stuttgart vorgestellt.

<table width="100%"><tbody><tr><td align="right"><a href="http://tuhrig.de/wp-content/uploads/IMG_2150.jpg"><img class="size-medium wp-image-201" title="IMG_2150" src="images/IMG_2150-225x300.jpg" alt="" width="225" height="300"></a></td><td align="left"><a href="http://tuhrig.de/wp-content/uploads/IMG_2161.jpg"><img class="size-medium wp-image-202" title="IMG_2161" src="images/IMG_2161-225x300.jpg" alt="" width="225" height="300"></a></td></tr></tbody></table>

Das größte Problem bei der Entwicklung war sicherlich die Android-Plattform selbst. Google verfolgt mit dem auf Linux basierenden Betriebssystem und der eigens entwickelten Java VM zwar ehrgeizige Ziele, erreicht diese aber gerade in puncto **Real-Time-Audioverarbeitung** nicht. Denn was soll ein Metronom unbedingt können? - Richtig, es muss im Takt ticken. Doch was bleibt von einem "Takt", wenn Oberflächen permanent neu gestartet werden falls man das Handy kippt, wenn der Garbage Collector nach dem Stop-the-World Prinzip vorgeht oder wenn die Audio-API zuviel Latenz aufweist?

Wer sich für die App, bzw. die Probleme und Umsetzung interessiert, kann hier die gesamte Dokumentation und den Quellcode, sowie die eigentliche Anwendung herunterladen.

[Dokumentation](http://www.tuhrig.de/wp-content/uploads/nepomuk/Nepomuk%20-%20Dokumentation.pdf "Nepomuk Dokumentation")

[Quellcode](http://www.tuhrig.de/wp-content/uploads/nepomuk/Nepomuk%20-%20Quellcode.zip "Nepomuk Quellcode")

[Nepomuk (Android App)](http://www.tuhrig.de/wp-content/uploads/nepomuk/Nepomuk.apk "Nepomuk Android App")

**Beste Grüße,** _Thomas Uhrig._
