---
layout: post
category: stories
tagline: "sss"
tags : [gis,maps,tiles,leaflet]
---

### LeafletJS with custom projection ###

I'm used to generating tiles with custom projections or resolutions. Using them on the web was easy bucause [OpenLayers](http://openlayers.org/ "OpenLayers") exists. One time, [I even made a maps API on top of OpenLayers](http://api.mapas.sapo.pt/ "API SAPO Mapas") - around 2009 those were the best maps in Portugal. Yes, better than Google Maps then.

Anyway, for the project I'm currently working on I decided to use [LeaftletJS](http://leafletjs.com/ "LeafletJS") to create a map. The tiles I had were in the projection [EPSG:3785](http://spatialreference.org/ref/epsg/3785/) (or EPSG:900913), TMS style, but with custom resolutions (zoom levels). Out of the box LeafletJS doesn't let you specify the custom resolutions on its L.TileLayer.

Fortunately, the awesome people of [Kartena](http://blog.kartena.se/ "Kartena") made a plugin which lets you specify custom projections, resolutions and tile origins - [Proj4Leaflet](https://github.com/kartena/Proj4Leaflet).

They have a very good documentation which allowed me to set up a map pretty quickly. However, after defining my custom projection, I was getting the following error

    Projected bounds does not match grid at zoom 0

[I asked for help on the Proj4Leaflet Github page](https://github.com/kartena/Proj4Leaflet/issues/31) and [Per Liedman](https://github.com/perliedman) quickly helped me solving the issue. My upper bounds were wrong and he helped me recalculating them to the correct value. Thanks Per!

After that the map was working perfectly!

For referrence, here's an example on how to use tiles with custom projections with LeafletJS, using the Proj4Leaflet plugin:

    var customCRS = new L.Proj.CRS.TMS('EPSG:900913',
        '+proj=merc +a=6378137 +b=6378137 +lat_ts=0.0 +lon_0=0.0' + 
        '+x_0=0.0 +y_0=0 +k=1.0 +units=m +nadgrids=@null +wktext  +no_defs', 
        [6422198.546, -851225.043, 196550.197, 9691950.164], 
        {
            origin: [-851225.043, 6422198.546],
            resolutions: [6386.233628906251,
                3193.1168144531257,
                1596.5584072265628,
                798.2792036132814,
                399.1396018066407,
                199.56980090332036,
                99.78490045166018,
                49.89245022583009
            ]
        }
    );

    var ljs_map = new L.Map('ljs_map', {
        crs: customCRS
    });

    var layer = new L.Proj.TileLayer.TMS('http://url.to.the/layer/{z}/{x}/{y}.png', customCRS, {
        maxZoom: 7,
        minZoom: 0,
        tileSize: 512
    });

    ljs_map.addLayer(layer);
    ljs_map.setView([51, 0], 7);


