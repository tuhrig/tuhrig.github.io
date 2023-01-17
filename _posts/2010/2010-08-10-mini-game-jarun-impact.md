---
layout: post
title: "Mini-Game: JARUN Impact"
date: "2010-08-10"
categories: 
  - "academic"
  - "c"
  - "coding"
---

In diesem Semester habe ich für die Vorlesung **Entwicklung von Computerspielen** ein Mini-Game entwickelt. 
Herausgekommen ist dabei **JARUN Impact**, eine Art 2D-Space-Shooter.

## Das Spiel

In JARUN Impact, steuert man ein kleines Raumschiff durch insgesamt drei verschiedene Level. 
Diese sind unterschiedlich schwer und beherbergen auch verschiedene Gegner. 
Dabei läuft das Spielprinzip jedoch stets gleich ab: 
Man steuert sein eigenes Raumschiff mithilfe der Tastatur um Gegner und Hindernisse herum und zielt und schießt dabei mit der Maus. 
Das Ziel ist es möglichst weit zu kommen und sich so eventuell in die Punkte-Highscore eintragen zu können - und diese ~~gibt~~ gab es online.

![](/images/2010/08/jarun1.png)
![](/images/2010/08/jarun2.png) 

(Die Online-Highscore funktioniert inzwischen leider nicht mehr.
Ich kann euch nur noch den PHP-Code von damals zeigen.)

```php
<table>
    <tr>
        <td width="20%" height="40px"><b>Rank:</b></td>
        <td width="20%" height="40px"><b>Player:</b></td>
        <td width="20%" height="40px"><b>Score:</b></td>
        <td width="20%" height="40px"><b>Date:</b></td>
        
        <?php
            require_once('jarun/db.inc.php');

            $dbResult = mysql_query("SELECT * FROM jarunscore ORDER BY Score DESC LIMIT 10");
                
            if($dbResult && mysql_num_rows($dbResult)>0)
            {
                $i = 0;
                
                while ($db_score = mysql_fetch_assoc($dbResult))
                {
                    $i++;
                    
                    echo "<tr>";
                                                            echo "<td height='30px'>".$i."</td>";
                    echo "<td height='30px'>".$db_score["Name"]."</td>";
                    echo "<td height='30px'>".$db_score["Score"]."</td>";
                    echo "<td height='30px'>".$db_score["Date"]."</td>";
                    echo "</tr>";
                }
            }
        ?>
</table>
```

Entwickelt habe ich das Ganze in **C#** mithilfe des **XNA-Frameworks von Microsoft**. 
Dieses ist zur Entwicklung von Spielen auf Windows, der X-Box und zum Teil auch auf anderen Microsoftplattformen gedacht. 
Es bringt den großen Vorteil, eine Menge von Funktionen und Klassen für den Entwickler bereitzustellen, die für die Programmierung eines Spieles notwendig sind. 
Und es funktioniert einfach gut.

> **Bedingt durch C# und XNA läuft das Spiel nur auf Windows!**

Wer sich für Jarun Impact oder generell die Entwicklung interessiert findet anbei noch die Links zum Download. 
Ich habe auch das gesamte Visual Studio Projekt, inklusive Grafiken und Musik hochgestellt. 
Ich hoffe, es hilft dem ein oder anderem weiter.

- [Ausführbares Programm](https://drive.google.com/file/d/1lK6qtXhjWRZ2NE6KIv5Mu2y_5snEb-Db/view?usp=share_link)
- [Projektdateien](https://drive.google.com/file/d/1qsc_75qs0qDPYHPNy2OKWmIFzI-594Fo/view?usp=share_link)
- [PHP-Skripte für die Online-Highscore](https://drive.google.com/file/d/1O1gzrFqFB7hZg7IIaeqrUSVnoivw8Au6/view?usp=share_link)

**Beste Grüße**, Thomas.
