---
layout: post
title: "Decorator & Fassade (Desing Patterns)"
date: "2010-05-16"
categories: 
  - "academic"
tags: 
  - "academic"
  - "design-pattern"
  - "download"
  - "programming"
  - "slides"
---

Zu Beginn dieses Semesters habe ich in der Vorlesung "**Design Patterns**" ([Professor Walter Kriha](http://www.kriha.de "Homepage Walter Kriha")), die Entwurfsmuster **Deocrator** und **Fassade** vorgestellt.

[![Decorater & Fassade](images/decorator_fassade-300x112.jpg "decorator_fassade")](http://tuhrig.de/wp-content/uploads/decorator_fassade.jpg)Decorator und Fassade sind zwei Strukturmuster der sogenannten "**Gang of Four**", den Vätern der Design-Patterns.

Der **Decorator** beschreibt einen Lösungsweg, dynamisch zur Laufzeit Eigenschaften zu einem Objekt hinzuzufügen. Dies wird über ein einheitliches Interface realisiert, welches jede Komponente und jeder Dekorierer implementiert. Außerdem hält ein jeder Dekorierer einen Verweis auf eine konkrete Komponente bzw. einen weiteren Dekorierer. So können beliebige Ketten von Eigenschaften und Methoden erstellt werden.

Die **Fassade** stellt eine Möglichkeit vor, den Zugriff auf ein komplexes Subsystem von Objekten zu kapseln. Ähnlich dem Eingang eines Gebäudes, stellt sie einen zentralen Zugang zu dem Subsystem bereit. Dabei können bei Bedarf auch Sicherheitskontrollen oder ähnliches, in der Fassade implementiert werden (vgl. **Proxy-Pattern**).

**Download:** [Decorator & Fassade](http://tuhrig.de/wp-content/uploads/Decorator-Fassade.pdf)

**Beste Grüße,** _Thomas Uhrig._
