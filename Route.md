# L.esri.Routing.Route

`L.esri.Routing.Route` is a class that is responsible for solving routes between any number of `L.esri.Routing.Stop` objects (or simple latlngs).

## A Basic Route

```js
// with an array of stops
var route = new L.esri.Routing.Route().stops([latlng,latlng]).addTo(map);

// with single stops
var route = new L.esri.Routing.Route().stop(latlng).stop(latlng).addTo(map);

// with start/end
var route = new L.esri.Routing.Route().start(latlng).end(latlng).addTo(map);
```

## Named Stops (`L.esri.Routing.Stop`)

A distinct object respresenting a stop in a route.  This is important for a number of reasons.

* Stops can be added to the map
* Bind popups to stops
* Wrapping of advanced parameters like U-Turns and curb approaches
* Update stops later by passing the same name.
* Bind events to stops.

```js
var home = new L.esri.Routing.Stop(latlng, {
    name:  'Home'
});

var caffeine = new L.esri.Routing.Stop(latlng, {
    name:  'Augies'
}).uTurn(false).approchFrom('Right');

var work = new L.esri.Routing.Stop(latlng, {
    name:  'Esri HQ'
});

var route = new L.esri.Routing.Route().start(home).stop(caffeine).end(work).addTo(map);
```

`L.esri.Routing.Stop` will extend `L.Marker`.  Background for this decision can be found [here](https://github.com/Esri/esri-leaflet-routing/issues/11)

## Optimizing Routes

The semantics for route optimization have been discussed [here](https://github.com/Esri/esri-leaflet-routing/issues/8). The tables from [@nixta](https://github.com/nixta) represent how this will work, with the exception of using slightly different method/option names.

```js
var work = new L.esri.Routing.Stop(latlng, {
    name:  'Esri HQ'
});

var store = new L.esri.Routing.Stop(latlng, {
    name:  'Groceries'
});

var gas = new L.esri.Routing.Stop(latlng, {
    name:  'Gas'
});

var bank = new L.esri.Routing.Stop(latlng, {
    name:  'Bank'
});

var home = new L.esri.Routing.Stop(latlng, {
    name:  'Home'
});

var route = new L.esri.Routing.Route({
    optimizeStopOrder: true
}).start(work).stops([store,bank,gas]).end(home).addTo(map);
```

### Default params with `optimizeStops: false`

| Syntax | Notes | findBestSequence | preserveFirstStop | preserveLastStop |
-------- | ------- | ------- | ------- | ------- |
| `.start().end()` | start + end | `false` | `N/A` | `N/A` |
| `.start().end().stops()` | start + stops + end | `false` | `N/A` | `N/A` |
| `.start().stops()` | start + stops | `false` | `N/A` | `N/A` |
| `.end().stops()` | stops + end | `false` | `N/A` | `N/A` |
| `.stops()` | stops | `false` | `N/A` | `N/A` |

### Default params with `optimizeStops: true`

| Syntax | Notes | findBestSequence | preserveFirstStop | preserveLastStop |
-------- | ------- | ------- | ------- | ------- |
| `.start().end()` | start + end | `false` | `N/A` | `N/A` |
| `.start().end().stops()` | start + stops + end | `true` | `true` | `true` |
| `.start().stops()` | start + stops | `true` | `true` | `false` |
| `.end().stops()` | stops + end | `true` | `false` | `true` |
| `.stops()` | stops | `true` | `false` | `false` |

## Popups and Events

```js
// with an array of stops
var route = new L.esri.Routing.Route().stops([latlng,latlng]).addTo(map);

// bind an event to your route
var route.on('mouseover', function(e){
    // e.target.routeJSON for the data used to create the route
});

// bind a popup to your route
var route.bindPopup(function(e){
    //routeInfo includes the JSON used to create the route (all stops, routes, directions, ect...).
}));
```

## Accessing Directions

```js
var route = new L.esri.Routing.Route().stops([latlng,latlng]).addTo(map);

// when the route is loaded a directions property will be included in the response object
route.on('load', function(e){
    e.target.directions
});

// it should also be possible to get the directions with a callback. This could return cached directions (from the rendering step) if possible.
route.getDirections(function(error, steps){
    // steps will be an ordered GeoJSON feature collection of the steps.
});
```

## Popup with Directions

In order to support the use case of clicking a section of the route and showing the directions for that step we can render multiple invisible polylines like so:

1. Render a single continuous polyline representing the whole route.
2. For each step in the directions render a polyline with opacity=0 and a wider stroke
3. When using `bindPopup()` the popup will be bound to the invisible polylines for each segment of the route allowing for the following sample to work.

```
// with an array of stops
var route = new L.esri.Routing.Route().stops([latlng,latlng]).addTo(map);

var route.bindPopup(function(e){
    //e.route includes the JSON used to create the route (all stops, routes, directions, ect...).
    //e.step includes all the information about the step in the route that was clicked on
}));
```

## Raw Data Access

If developers want to access the raw data behind the route insteed of calling `addTo()` and adding the route as a layer they can use it like a `Task` and use `run()`.

```js
var route = new L.esri.Routing.Route().stops([latlng,latlng]).run(function(error, geojson, response){
    // geojson would contain route.route, route.stops, route.barriers, route.directions, ect... as geojson features/feature collections.
});
```

## Options
Full list of options to pass when creating `L.esri.Routing.Route`

* `travelMode` - One of `DrivingTime`, `DrivingDistance`, `TruckingTime`, `TruckingDistance`, `WalkingTime`, `WalkingDistance` OR a `L.esri.Routing.TravelMode` object.
* `useTraffic` - `true` or `false` -
* `startTime` - Date object, if set overrides `travelMode` to be `Time` and useTraffic to be `true`.
* `optimizeStopOrder` - `false` or `true` optimize the order of the stops (via `findBestSequence`) also controls behavior for `preserveFirstStop` and `preserveLastStop`
* `language`: Any valid string from the directions service
* `units`: `Miles` or `Kilometers` default if inferred from `language`.
* `directionsStyle` - `Print` or `'Navigation'` how to return the directions

## Methods

* `start()` - defines the starting point on the route. Accepts a `L.esri.Routing.Stop` or `LatLng` with an optional name
* `end()` - define teh ending point of the route. Accepts a `L.esri.Routing.Stop` or `LatLng` with an optional name
* `stop()` - defines a single stop along the route. Accepts a `L.esri.Routing.Stop` or `LatLng` with an optional name,
* `stops()` - defines an array of stops along the route. Accepts a `L.esri.Routing.Stop` or `LatLng` with an optional name, will probably also be able to accept an L.esri.Tasks.Query for pulling stops from a service.
* `barrier()` - Still being debated [here](https://github.com/Esri/esri-leaflet-routing/issues/12), will probably also be able to accept an L.esri.Tasks.Query for pulling stops from a service.
* `barriers()` - Still being debated [here](https://github.com/Esri/esri-leaflet-routing/issues/12), will probably also be able to accept an L.esri.Tasks.Query for pulling stops from a service.
* `addTo()` - Add the route to the map
* `removeFrom()` - Removes the route from the map
* `run()` - executes the requests and runs a callback
* `getDirections()` - passes directions to a callback
* `redraw()` - If objects representing stops/barriers have changed, rerun the route

## Inferred options and parameters

* `preserveFirstStop` and `preserveLastStop` are inferred from the use of the `start()` and `end()` methods
* `useTimeWindows` will be `true` if ANY stop declares `arriveBetween(datetime, datetime)`
* `L.esri.Routing.TravelMode` will also expose things like restrictions, impedance, hierarchy, ect... for developers who REALLY want to customize the output.
* If barriers from a query are used `returnBarriers` will be `true` otherwise barrier geometries will be added client side to the response

## Excluded params

* `returnRoutes` and `returnDirections` will always be true.
* `outSr` will always be `4326`
* `outputGeometryPrecision` and `outputGeometryPrecisionUnits` will not be exposed

## L.esri.Routing.Stop

`L.esri.Routing.Stop` will extend L.Marker for simplicity and add the following options and methods:

### Options

* `Name` - sets the name on the stop.  This will be used in directions.

### Methods

* `arriveBetween(date, date)` - sets a time window for the object to arrive at this stop. Dates should be in the timezone of the stop.
* `addTravelTime(minutes)` - type of distance (walking, trucking, driving) is inferred from other params (travelMode)
* `addTravelDistance(distance)` - unit and type of distance (walking, trucking, driving) is inferred from other params (travelMode)
* `approchFrom(direction)` `Right`, `Left` and `NoUTurn` controls the `CurbApproach` parameter

## Related Discussion
* The semantics of `start()`, `end()`, `finish()`, `via()`, `from()`, `to()`, etc... are being discussed in https://github.com/Esri/esri-leaflet-routing/issues/8