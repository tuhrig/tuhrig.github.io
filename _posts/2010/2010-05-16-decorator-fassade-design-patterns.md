---
layout: post
title: "Decorator & Fassade (Design Patterns)"
date: "2010-05-16"
categories: 
  - "academic"
  - "design-pattern"
  - "slides"
---

Zu Beginn dieses Semesters habe ich in der Vorlesung "Design Patterns" ([Professor Walter Kriha](http://www.kriha.de "Homepage Walter Kriha")), die Entwurfsmuster **Deocrator** und **Fassade** vorgestellt.
Decorator und Fassade sind zwei Strukturmuster der sogenannten "[Gang of Four](https://www.digitalocean.com/community/tutorials/gangs-of-four-gof-design-patterns)", den Ur-Vätern der Design-Patterns.

## Decorator

Der Decorator beschreibt einen Lösungsweg, dynamisch zur Laufzeit Eigenschaften zu einem Objekt hinzuzufügen. 
Dies wird über ein einheitliches Interface realisiert, welches jede Komponente und jeder Decorator implementiert. 
Außerdem hält ein jeder Decorator einen Verweis auf eine konkrete Komponente bzw. einen weiteren Decorator. 
So können beliebige Ketten von Eigenschaften und Methoden erstellt werden.

## Fassade

Die Fassade stellt eine Möglichkeit vor, den Zugriff auf ein komplexes Subsystem von Objekten zu kapseln. 
Ähnlich dem Eingang eines Gebäudes stellt sie einen zentralen Zugang zu dem Subsystem bereit. 
Dabei können bei Bedarf auch Sicherheitskontrollen oder ähnliches, in der Fassade implementiert werden (vgl. **Proxy-Pattern**).

## Präsentation

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
    const site = "https://speakerdeck.com/player/4fe4544ccb0797002202b94d";
    document.getElementsByName('thirdPartyContent')[0].src = site;
}
</script>

**Download:** [Decorator-Fassade.pdf](/assets/pdf/Decorator-Fassade.pdf)

**Beste Grüße,** Thomas.
