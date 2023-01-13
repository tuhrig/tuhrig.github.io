---
layout: post
title: "Reflections in Java: \"private\" umgehen"
date: "2010-05-20"
categories: 
  - "coding"
  - "academic"
  - "java"
---

Eine gesunde Selbstreflexion ist von Zeit zu Zeit eine schöne Sache. 
Man sollte sich schließlich selbst kennen und einschätzen können. 
Ähnlich sieht es im Code aus. 
Und auch wenn [Java](https://en.wikipedia.org/wiki/Java_(programming_language)) nur eingeschränkte Mittel für [Reflections](https://stackoverflow.com/questions/37628/what-is-reflection-and-why-is-it-useful) beinhaltet - z.B. im Verglich zu [Smalltalk](https://en.wikipedia.org/wiki/Smalltalk) - hat mich der Vortrag zweier Kommilitonen doch ein wenig zum experimentieren gebracht. 
Anbei ein paar Zeilen Code, mit denen man sehr schnell und einfach das Sicherheitskonzept hinter Java und dem Schlüsselwort `private` umgehen kann:

Unsere Beispielklasse, aus der wir ein privates Attribut auslesen wollen, welches in einem privaten Konstruktor initialisiert wird:

```java
package inspector; 
public class Geheimnis{ 
    private String geheimnis; 
    private Geheimnis(){ 
        geheimnis = "Top secret!"; 
    } 
}
```

Als Erstes holen wir uns ein Objekt der Klasse (keine Instanz!). 
Die Klasse einer Klasse ist die Klasse `Class` (Meta-Klasse). 
Somit haben wir ein Objekt an der Hand, welches die Klasse repräsentiert: 

```java
Class c = Class.forName("inspector.Geheimnis");
```

Nun können wir relativ einfach Attribute, Methoden und Konstruktoren auslesen, auch private: 

```java
Constructor[] constructors = c.getDeclaredConstructors(); 
Field[] fields = c.getDeclaredFields(); 
Method[] methods = c.getMethods();
```

Wenn wir die Arrays auf der Console ausgeben, sehen wir die qualifizierten Namen der einzelnen Methoden und Attribute.
Wir sehen auch den privaten Konstruktor und das private Attribut welches wir auslesen wollen.
Als Nächstes schalten wir das Schlüsselwort `private` aus: 

```java
constructors[0].setAccessible(true); 
fields[0].setAccessible(true);
```

Damit haben wir es eigentlich schon geschafft. 
Wir halten ein Objekt in der Hand, dass unsere Klasse repräsentiert. 
Darin haben wir das Schlüsselwort `private` ausgeschaltet und können uns nun Instanzen erzeugen und mit dem eigentlich geheimen Attribut tun und lassen, was wir wollen: 

```java
//Wir können nun Objekte erzeugen... 
Geheimnis g = (Geheimnis) constructors[0].newInstance(null); 
//...das private Attribut auslesen... 
String unbekannt = (String) fields[0].get( g ); 
//...und ändern: 
fields[0].set(g, "I know your secret!");
```

**Beste Grüße,** Thomas.
