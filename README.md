
# Introducing GeoJS

GeoJS is a new javascript library for visualizing geospatial data in the browser.  It is completely 
open source and hosted at https://github.com/OpenGeoscience/geojs.
* History
* Difference from openlayers, google maps, leaflet…
* Projects and funding?
 

# API Overview

GeoJS is supported by a hierarchy of classes which define basic interfaces for the objects used
in the API.

## Maps

The entry point for developer interaction with the API is the `geo.map` class.  As with most object
constructors in GeoJS, the map constructor takes an object containing optional parameters.
```javascript
map = geo.map(
    {
        "node":   "#map",     # The DOM node to contain the map
        "center": [40, -100], # The initial lat/lng center point
        "zoom":   2           # The initial zoom level
    }
);
```
The map serves as the root of the scene tree and contains georeferencing functions that can be used
for converting to and from pixel coordinates and geographic coordinates.
```javascript
# Get the pixel coordinates of the point 40° N, 100° W
>>> map.gcsToDisplay({x: -100, y: 40})
{x: 100, y: 100}

# Get the geographic coordinates of the pixel at (100, 100)
>>> map.displayToGcs({x: 100, y: 100})
{x: -100, y: 40}
```
Finally, the map provides a uniform looping mechanism for drawing synchronized animations within
its layers.  Running an animation from a map containing animatable layers is a simple matter of
calling `map.animate()`.


## Layers and renderers

A layer is an abstract representation of content visible to the user.  Layers are like transparent
sheets placed in a stack.  Visible features on one layer will cover features on layers below.
In addition, only the top layer of the stack can directly receive mouse events.  The event
handling interface described below allows layers to respond to mouse events and map navigation
in a uniform manner.

In terms of responding to map navigation events such as panning.  Layers come in two varieties
either *sticky* or *non-sticky*.  A sticky layer will automatically transform features contained
within it to maintain its position relative to the map.  This is useful for geographic features
such as markers placed at a specific latitude/longitude.  A non-sticky layer will not automatically
transform.  One might use this sort of layer for legends that remain fixed on the screen, or
for features that must have custom behavior with map navigation.

Along with each layer is a renderer, which is responsible for actually drawing the features.
Every layer has exactly one renderer and every renderer has exactly one layer.  Renderers
currently come in two varieties, `geo.vglRenderer` and `geo.d3Renderer`.  The vgl renderer
draws features in a wegGL context via the dependent vgl module[^vgl],
while the d3 renderer
draws features inside an SVG element using the d3 library[^d3].  Both renderers have the same
top level API, but also contain hooks to obtain low level contexts for advanced usage.

In order to draw and use a map object a special layer called a *reference layer* must be
attached to the map.  The reference layer is responsible for specifying the coordinate
system and translating mouse and keyboard events into map navigation events.  At present,
only one reference layer class exists in geoJS, `geo.osmLayer`.  This layer
fetches tiles from the open street maps tile server to render tiles on demand.  This layer
is created as follows:
```javascript
osm = map.createLayer("osm");
```


## Feature layers

Feature layers are a specialization of a layer that allows the user to create features like
circles, polygons, etc. over the map.  The feature layer object is instantiated directly from
the map object with an optional parameter specifying the target renderer.
```javascript
layer = map.createLayer("feature", {renderer: "d3Renderer"});
```
Feature layers contain an interface to create feature objects, which are sets of drawable
shapes.  Out of necessity, each feature type has its own API for setting coordinate positions
and rendering styles, but the general scheme is roughly consistent with the following example.

```javascript
layer.createFeature("point")
    .positions([{x: -100, y: 40}, {x: -110, y: 35}])
    .style({color: [1, 0, 0], size: [10]});
```
![points feature example](https://raw.githubusercontent.com/jbeezley/Geojs-source/master/img/points.png "geo.pointFeature")

## Objects and events

The lowest level class that all other classes inherit from is the core `geo.object` which
provides all objects with a timekeeping mechanism as well as providing the interface for
the internal events system.  Another important base class is the `geo.sceneObject` from
which all drawable objects are derived.  The `sceneObject` defines a tree structure through
which events are propagated.  All events in the scene tree first propagate up the tree from
parent to parent until it reaches the root node.  Once the event reaches the root, the node
calls its own handlers and then triggers the event on all of its children.  Parent nodes can
block events from propagating up the scene tree as well as prevent its children from receiving
events that originated from a different branch.  The scene tree event model is what allows
events to *propagate* through layers and have them react together.


## Testing

GeoJS is released with a broad range of unit tests ensuring not only that changes do not
break API calls, but that rendering style and behavior stay consistent as well.  There are
several unit test frameworks from Jasmine[^Jasmine] tests run with PhantomJS[^PhantomJS] to 
end-to-end multi-browser tests run with [^Selenium].  The tests frameworks allow the developers
to add new tests easily, often just by adding a jasmine spec in a new file.  Each test is
automatically integrated into the build and dashboard all with code coverage reporting
builtin.  The master branch is run nightly on dashboard machines reporting to CDash[^cdash].
Continuous integration testing is performed for every push using Travis-CI[^travis], whose
results are also summarized by CDash.


# Applications

*Climate pipes, custom tile server, grits?*


## Future

*Maybe selection api, 2.5D, any other crazy plans for the future*


[^vgl]: https://github.com/OpenGeoscience/vgl/
[^d3]: http://d3js.org/
[^Jasmine]: http://jasmine.github.io/
[^PhantomJS]: http://phantomjs.org/
[^Selenium]: http://docs.seleniumhq.org/
[^cdash]: http://my.cdash.org/index.php?project=geojs
[^travis]: https://travis-ci.org/
