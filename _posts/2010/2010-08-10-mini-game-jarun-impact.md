---
layout: post
title: "Mini-Game: JARUN Impact"
date: "2010-08-10"
categories: 
  - "academic"
  - "c"
  - "coding"
---

In diesem Semester habe ich für die Vorlesung **Entwicklung von Computerspielen** ein kleines Mini-Game als Prüfungsleistung entwickelt. Herausgekommen ist dabei **JARUN Impact**, eine Art 2D-Space-Shooter.

In JARUN Impact, steuert man ein kleines Raumschiff durch insgesamt drei verschiedene Level. Diese sind unterschiedlich schwer und beherbergen auch verschiedene Gegner. Dabei läuft das Spielprinzip jedoch stets gleich ab: Man steuert sein eigenes Raumschiff mit Hilfe der Tastatur um Gegner und Hindernisse herum und zielt und schießtdabei mit der Maus. Das Ziel ist es dabei möglichst weit zu kommen und sich so eventuell in die Punkte-Highscore eintragen zu können - und diese gibt es online:

[![](images/jarun1-300x182.png "jarun1")](http://tuhrig.de/wp-content/uploads/jarun1.png) [![](images/jarun2-300x182.png "jarun2")](http://tuhrig.de/wp-content/uploads/jarun2.png) **JARUN Impact Highscore:** 0) { $i = 0; while ($db\_score = mysql\_fetch\_assoc($dbResult)) { $i++; echo ""; echo ""; echo ""; echo ""; echo ""; echo ""; } } ?>

<table><tbody><tr><td width="20%" height="40px"><b>Rank:</b></td><td width="20%" height="40px"><b>Player:</b></td><td width="20%" height="40px"><b>Score:</b></td><td width="20%" height="40px"><b>Date:</b></td></tr><tr><td height="30px">".$i."</td><td height="30px">".$db_score["Name"]."</td><td height="30px">".$db_score["Score"]."</td><td height="30px">".$db_score["Date"]."</td></tr></tbody></table>

Entwickelt habe ich das Ganze in **C#** mit Hilfe des **XNA-Frameworks von Microsoft**. Dieses ist zur Entwicklung von Spielen auf Windows, der X-Box und zum Teil auch auf anderen Microsoftplattformen gedacht. Es bringt den großen Vorteil, eine Menge von Funktionen und Klassen für den Entwickler bereitzustellen, die für die Programmierung eines Spieles notwendig sind. Und - es funktioniert einfach gut.

**Bedingt durch C# und XNA läuft das Spiel jedoch nur auf Windows!**

Wer sich für Jarun Impact oder generell die Entwicklung interessiert findet anbei noch die Links zum Download. Ich habe auch die gesamten Visual Studio Projektdateien, inklusive Grafiken und Musik hochgestellt. Ich hoffe es hilft dem ein oder anderem weiter.

**Ausführbares Programm:** [http://tuhrig.de/wp-content/uploads/jarun/Jarun.zip](http://tuhrig.de/wp-content/uploads/jarun/Jarun.zip)

**\*\*\*** _Da es mit der Installation auf einigen PCs Schwierigkeiten gegeben hat, habe ich eine Bibliothek zur Content Pipeline aus dem Build Path entfernt. Ich hoffe, dass das Problem damit gelöst ist, die Installation müsste nun also ohne Weiteres möglich sein._ **\*\*\***

**Projektdateien:** [http://tuhrig.de/wp-content/uploads/jarun/JARUN-Quellcode.zip](http://tuhrig.de/wp-content/uploads/jarun/JARUN-Quellcode.zip)

**Php-Skripte für die Online-Highscore:** [http://tuhrig.de/wp-content/uploads/jarun/Jarun-Php-Skripte.zip](http://tuhrig.de/wp-content/uploads/jarun/Jarun-Php-Skripte.zip)

**Beste Grüße**, Thomas Uhrig.
