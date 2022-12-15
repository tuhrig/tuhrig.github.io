---
layout: post
title: "Java 8 in Eclipse (Kepler and Luna)"
date: "2014-04-01"
categories: 
  - "coding"
tags: 
  - "eclipse"
  - "java"
  - "java-8"
---

Java 8 is officially available since a couple of days. It was released on March 18th this year. Yeah! But - Eclipse, the biggest and most popular Java IDE is a little bit behind the schedule (at least IMHO). **There is no official Eclipse version for Java 8 right now!**

But there are two other thing: an up-to-date nightly build of the new Eclipse version **Luna** which will be released with Java 8 support and a patch for the current Eclipse version **Kepler**! Since it is sometimes (= always) hard to find the correct Eclipse version on eclipse dot org or anything useful at all, here is how to do it.

# Java 8 in Eclipse Juno (4.2)

If you still use Eclipse Juno (4.2), you use an old version of Eclipse without Java 8 support and I strongly recommend to use a new version (as described below). If you cannot do that (or don't want to do that) here is an old tutorial from me how to setup Java 8 in Eclipse Juno: [http://tuhrig.de/java-8-in-eclipse-juno](http://tuhrig.de/java-8-in-eclipse-juno).

# Java 8 in Eclipse Kepler (4.3)

[![2014-03-31 12_16_15-Program Manager](images/2014-03-31-12_16_15-Program-Manager-300x200.png)](http://tuhrig.de/wp-content/uploads/2014/03/2014-03-31-12_16_15-Program-Manager.png)

Kepler (4.3) is the current version of Eclipse. You can download it from [http://www.eclipse.org/downloads](http://www.eclipse.org/downloads). After you downloaded it, it will _not_ have Java 8 support right out of the box! To add it, you have to install a feature patch:

1. Download and install Java 8 (e.g. from Oracle [JRE](http://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html)/[JDK](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) or [OpenJDK](https://jdk8.java.net/download.html))
2. Add it to Eclipse: [![2014-03-31 12_25_37-Java - Java8Project_src_Test.java - Eclipse](images/2014-03-31-12_25_37-Java-Java8Project_src_Test.java-Eclipse.png)](http://tuhrig.de/wp-content/uploads/2014/03/2014-03-31-12_25_37-Java-Java8Project_src_Test.java-Eclipse.png)
3. Install the feature patch via `Help > Install New Software...`:
    
    http://download.eclipse.org/eclipse/updates/4.3-P-builds/
    
    [![2014-03-31 12_04_14-Java - test_src_test_Hello.java - Eclipse](images/2014-03-31-12_04_14-Java-test_src_test_Hello.java-Eclipse.png)](http://tuhrig.de/wp-content/uploads/2014/03/2014-03-31-12_04_14-Java-test_src_test_Hello.java-Eclipse.png)

You have to restart Eclipse during the installation and accept a license and click several next-buttons. But after you installed the feature patch, you have Java 8 support in Eclipse Kepler (4.3). If you have problems with the installation process, download a new Eclipse Kepler version.

[![2014-03-31 12_34_14-](images/2014-03-31-12_34_14-.png)](http://tuhrig.de/wp-content/uploads/2014/03/2014-03-31-12_34_14-.png)

# Java 8 in Eclipse Luna (4.4)

[![2014-03-31 16_18_56-C__Users_tuhrig_AppData_Local_Temp_7zOADE2.tmp_repositories • - Sublime Text 2 (](images/2014-03-31-16_18_56-C__Users_tuhrig_AppData_Local_Temp_7zOADE2.tmp_repositories-•-Sublime-Text-2--300x200.png)](http://tuhrig.de/wp-content/uploads/2014/03/2014-03-31-16_18_56-C__Users_tuhrig_AppData_Local_Temp_7zOADE2.tmp_repositories-•-Sublime-Text-2-.png)

Luna (4.4) is the upcoming version of Eclipse. It will be released this summer and it will contain Java 8 support. But you can already download some nightly builds of it which contains Java 8 support.

You can download Eclipse Luna [here](http://download.eclipse.org/eclipse/downloads). Just make sure you download the correct build version, since not all builds have Java 8 support already! When you download the correct version, you can just run Eclipse and use the new Java 8 features (you also need to install Java 8, e.g. from Oracle [JRE](http://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html)/[JDK](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) or [OpenJDK](https://jdk8.java.net/download.html)).

[![2014-03-31 12_38_38-Eclipse Project Downloads](images/2014-03-31-12_38_38-Eclipse-Project-Downloads.png)](http://tuhrig.de/wp-content/uploads/2014/03/2014-03-31-12_38_38-Eclipse-Project-Downloads.png)

By the way, some nightly builds still contain test errors. This is due to the fact that Eclipse Luna is still in development. But to play around with Java 8 or for a small project it should already be good enough.

[![2014-04-01 07_42_46-Eclipse Project](images/2014-04-01-07_42_46-Eclipse-Project-1024x123.png)](http://tuhrig.de/wp-content/uploads/2014/04/2014-04-01-07_42_46-Eclipse-Project.png)

# A simple Java 8 example

To test your Eclipse IDE, here is a very simple Java 8 example:

public class Test {
	public static void main(String\[\] args) {
		new Thread(() -> System.out.println("Hello Java 8!")).start();
	}
}

# Troubleshooting

If you have problems with Java 8 in Eclipse check your project settings. If your compliance level is not set to `1.8`, you cannot use Java 8. And if you don't even have the compliance level 1.8 then your Eclipse somehow doesn't support Java 8 and something went wrong. In this case, get yourself a new Kepler version and install the feature patch or download an appropriate Luna build as described above.

[![2014-03-31 12_12_56-Java - test_src_test_Hello.java - Eclipse SDK](images/2014-03-31-12_12_56-Java-test_src_test_Hello.java-Eclipse-SDK.png)](http://tuhrig.de/wp-content/uploads/2014/03/2014-03-31-12_12_56-Java-test_src_test_Hello.java-Eclipse-SDK.png)

# Resources

- For Juno: [http://tuhrig.de/java-8-in-eclipse-juno](http://tuhrig.de/java-8-in-eclipse-juno)

- For Kepler: [https://wiki.eclipse.org/JDT/Eclipse\_Java\_8\_Support\_For\_Kepler](https://wiki.eclipse.org/JDT/Eclipse_Java_8_Support_For_Kepler)
- For Luna: [http://download.eclipse.org/eclipse/downloads](http://download.eclipse.org/eclipse/downloads)

**Best regards,** Thomas
