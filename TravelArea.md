# Travel Area

**TravelArea**

```js
var area = new L.esri.Routing.DriveArea().origin(latlng).distance(5).distance(10).distance(15).addTo(map);
```

**Barriers**

```js
var area = new L.esri.Routing.DriveArea().origin(latlng).distance(5).barrier(geojson).addTo(map);
```

**Styling**

```js
var area = new L.esri.Routing.DriveArea({
    style: stylingFunction, // path options or function
    barrierStyle: stylingFunction // path options or function
}).origin(latlng).distance(5).barrier(geojson).addTo(map);
```

**Access Raw Polygons**

```js
var area = new L.esri.Routing.DriveArea().origin(latlng).distance(5).distance(10).run(function(error, areas, barriers){
    // console.log(do stuff);
});
```

```js
var area = new L.esri.Routing.DriveArea().origin(latlng).distance(5).distance(10).run(function(error, areas, barriers){
    // console.log(do stuff);
});
```