
# Introducing GeoJS

GeoJS is a new javascript library for visualizing geospatial data in the browser.  It is completely 
open source and hosted at https://github.com/OpenGeoscience/geojs.
* History
* Difference from openlayers, google maps, leaflet…
* Projects and funding?
 

# API Overview

GeoJS  is supported by a hierarchy of classes which define basic interfaces for the objects used
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
```
```javascript
# Get the geographics coordinates of the pixel at (100, 100)
>>> map.displayToGcs({x: 100, y: 100})
{x: -100, y: 40}
```

## Layers and renderers

A layer is an abstract representation of content visible to the user.  The core class
`geo.layer` simply defines the interface that layer must implement to be used in a map.  

The map object can be used to generate layers containing content visible to the end user.  
The layers are instantiated by the map object



## Objects and events

The lowest level class that all other classes inherit from is the core `geo.object` which
provides all objects with a timekeeping mechanism as well as providing the interface for
the internal events system.  Another important base class is the `geo.sceneObject` from
which all drawable objects are derived.  The `sceneObject` defines a tree structure through
which events are propagated.  All events in the scene tree first propagate up the tree from
parent to parent until it reaches the root node.  Once the event reaches the root, the node
calls its own handlers and then triggers the event on all of its children.  Parent nodes can
block events from propagating up the scene tree as well as prevent its children from receiving
events that originated from a different branch.



## Features


## Testing


## Future


# Applications

