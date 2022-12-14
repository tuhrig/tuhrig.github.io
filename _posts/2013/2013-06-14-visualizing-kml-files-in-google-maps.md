---
layout: post
title: "Visualizing KML files in Google Maps"
date: "2013-06-14"
categories: 
  - "academic"
  - "coding"
tags: 
  - "google"
  - "html"
  - "javascript"
  - "kml"
  - "maps"
---

The question is easy: **How can I visualize the track of a KML file on Google Maps via JavaScript?**

Last evening, I spend about four hours looking for a solution for this (pretty trivial) question. In the end, the code was simple and very short, but it was hard to find some good and clear resources about the topic. The documentation from Google about their maps API is very well - but in my opinion it lacks of some simple examples to start with. Therefore, here is a super simple example to **copy and past** and start right ahead.

# Example

<script src="http://code.jquery.com/jquery-1.10.1.min.js"></script>

<script src="https://maps.googleapis.com/maps/api/js?v=3.exp&amp;sensor=false"></script>

<script src="http://geoxml3.googlecode.com/svn/branches/polys/geoxml3.js"></script>

<script src="http://geoxml3.googlecode.com/svn/trunk/ProjectedOverlay.js"></script>

    

<script>
	
        function initialize() {
	
            var options = {
                center: new google.maps.LatLng(-34.397, 150.644),
                mapTypeId: google.maps.MapTypeId.ROADMAP
            };
		    
            var map = new google.maps.Map(document.getElementById("canvas"), options);
            var parser = new geoXML3.parser({map: map, processStyles: true});
            parser.parse("test.kml");
        }
	    
        $(document).ready(initialize);
	    
    </script>

 
	
    

	

# Run it

You can just copy and paste the example. You only have to do two things:

1. Get a KML file, call it [test.kml](http://tuhrig.de/wp-content/uploads/test.kml) and put it beside your HTML file in a folder.
2. Start a web-server in that folder (see below).

In order to run the example, you have to start a web-server. Otherwise you can't load the KML file, since it is not available trough the browser (due to cross origin requests and all that stuff). A very easy way to do that is with python. Just open a command line the folder where your HTML document is and start a server like this:

python -m SimpleHTTPServer 8080

Then go on [http://localhost:8080/](http://localhost:8080/) and see your map.

# Explanation

The script is only doing a few simple things. First of all, it initializes a map object using the [Google Maps API](https://developers.google.com/maps). This object represents the actual map drawn in the `div` with the id `canvas`. Then the script creates a parser object of the [geoxml3](https://code.google.com/p/geoxml3/) library. This library offers a very comfortable way to display KML files on Google Maps. However, the support for polylines (tracks on the map) is pretty new. So you have to use the [poly branch](https://code.google.com/p/geoxml3/source/browse/#svn%2Fbranches%2Fpolys) of library. Otherwise you won't see any lines, just your starting point. The library can also parse KML as a pure string. Check their wiki for more information.

# Finally

<iframe width="550px" height="520px" src="http://tuhrig.de/wp-content/uploads/maps.html"></iframe>

**Best regards,** Thomas Uhrig
