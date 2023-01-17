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

In den vergangenen Monaten, habe ich mich im Rahmen eines Studienprojekts, mit der Entwicklung von Software auf der Android-Plattform auseinandergesetzt. 
Entstanden ist dabei **Nepomuk**, ein digitales Metronom mit Sprachausgabe für die Android-Plattform (ab `v1.6`).

![](/images/2010/07/nepomuk_logo.png)

Die Idee zu diesem Projekt stammte von [Michiel Oldenkamp](http://www.musithek-oldenkamp.eu), einem langjährigen und professionellen Musiker. 
Dieser stellte während seiner Unterrichtsstunden fest, dass das Ticken eines Metronomes alleine nicht ausreiche. 
Viel mehr benötigten die Schüler einen ständigen Nebensitzer, zum permanenten Vorzählen des Taktes. 
Daraus entstand die Idee ein digitales Metronom für ein mobiles Endgerät zu entwickeln, welches neben dem übliche Ticken, über eine Sprachausgabe verfügt.

Nepomuk implementiert insgesamt sieben Sprachen, drei verschiedene Klangschemata und eine Tap-Funktion zur Berechnung des Tempos. 
Eine Präsentation als Videoaufzeichnung, ist unter [http://events.mi.hdm-stuttgart.de/](http://events.mi.hdm-stuttgart.de/) zu finden. 
Außerdem habe ich das Projekt auch auf der diesjährigen Media Night an der [HdM Stuttgart](https://www.hdm-stuttgart.de/) vorgestellt.

![](/images/2010/07/media-night-nepomuk-1.jpeg)

Das größte Problem bei der Entwicklung war die Android-Plattform selbst. 
Google verfolgt mit dem auf Linux basierenden Betriebssystem und der eigens entwickelten JVM zwar ehrgeizige Ziele, erreicht diese aber in puncto Real-Time-Audioverarbeitung (noch) nicht. 
Denn was soll ein Metronom unbedingt können? - Richtig, es muss im Takt ticken! 
Doch was bleibt von einem "Takt", wenn Oberflächen permanent neu gerendert werden, falls man das Handy kippt, wenn der Garbage-Collector nach dem Stop-the-World Prinzip vorgeht oder wenn die Audio-API zu viel Latenz aufweist?

Wer sich für die App, bzw. die Probleme und Umsetzung interessiert, kann hier die gesamte Dokumentation und den Quellcode, sowie die eigentliche Anwendung herunterladen.

- [Dokumentation als PDF](/assets/pdf/Nepomuk.pdf)
- [Quellcode als ZIP-File](https://drive.google.com/file/d/1R_b4kDozhkhPQf4djKCCBwqKSgPo4OmY/view?usp=share_link) (auf [https://drive.google.com](https://drive.google.com/))
- [Nepomuk (Android App) als *.apk](https://drive.google.com/file/d/1lQcpU_nwm_-xNEOGLlHZ0JbE4CQ9SVEn/view?usp=share_link) (auf [https://drive.google.com](https://drive.google.com/))

**Beste Grüße,** Thomas.
