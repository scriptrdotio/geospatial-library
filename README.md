# geospatial-library
Library to provide basic geospatial operations like distance calculation, conversion of decimal coordinates to sexagesimal and vice versa, etc.

This library was migrated from the dist of this nodejs library [https://github.com/manuelbieh/Geolib] to a [www.scriptr.io] script lib.

It contains all the functionalities available in the basic geolib.js file v2.0.21
[https://github.com/manuelbieh/Geolib/blob/master/dist/geolib.js]

## Importing & Initalizing the scriptr Geolib

var geo = require("geolib.js");

var geolib = new geo.Geolib();

## Methods

### geolib.getDistance(object start, object end[, int accuracy, int precision])

Calculates the distance between two geo coordinates

Takes 2 or 4 arguments. First 2 arguments must be objects that each have latitude and longitude properties (e.g. {latitude: 52.518611, longitude: 13.408056})Works with:. Coordinates can be in sexagesimal or decimal format. 3rd argument is accuracy (in meters). So a calculated distaWorks with:nce of 1248 meters with an accuracy of 100 is returned as 1200 (accuracy 10 = 1250 etc.). 4th argument is precision in sub-meters (1 is meter presicion, 2 is decimeters, 3 is centimeters, etc).

Return value is always an float and represents the distance in meters.

Examples
```javascript
geolib.getDistance(
    {latitude: 51.5103, longitude: 7.49347},
    {latitude: "51° 31' N", longitude: "7° 28' E"}
);
geolib.getDistance(
    {latitude: 51.5103, longitude: 7.49347},
    {latitude: "51° 31' N", longitude: "7° 28' E"}
);

// Working with W3C Geolocation API
navigator.geolocation.getCurrentPosition(
    function(position) {
        alert('You are ' + geolib.getDistance(position.coords, {
            latitude: 51.525,
            longitude: 7.4575
        }) + ' meters away from 51.525, 7.4575');
    },
    function() {
        alert('Position could not be determined.')
    },
    {
        enableHighAccuracy: true
    }
);
```
### geolib.getCenter(array coords)

Calculates the geographical center of all points in a collection of geo coordinates

Takes an object or array of coordinates and calculates the center of it.

Returns an object: {"latitude": centerLat, "longitude": centerLng}

Examples
```javascript
var spots = {
    "Brandenburg Gate, Berlin": {latitude: 52.516272, longitude: 13.377722},
    "Dortmund U-Tower": {latitude: 51.515, longitude: 7.453619},
    "London Eye": {latitude: 51.503333, longitude: -0.119722},
    "Kremlin, Moscow": {latitude: 55.751667, longitude: 37.617778},
    "Eiffel Tower, Paris": {latitude: 48.8583, longitude: 2.2945},
    "Riksdag building, Stockholm": {latitude: 59.3275, longitude: 18.0675},
    "Royal Palace, Oslo": {latitude: 59.916911, longitude: 10.727567}
}

geolib.getCenter(spots);

geolib.getCenter([
    {latitude: 52.516272, longitude: 13.377722},
    {latitude: 51.515, longitude: 7.453619},
    {latitude: 51.503333, longitude: -0.119722}
]);
```
### geolib.getCenterOfBounds(array coords)

Calculates the center of the bounds of geo coordinates.

Takes an array of coordinates, calculate the border of those, and gives back the center of that rectangle.

On polygons like political borders (eg. states), this may gives a closer result to human expectation, than getCenter, because that function can be disturbed by uneven distribution of point in different sides.

Imagine the US state Oklahoma: getCenter on that gives a southern point, because the southern border contains a lot more nodes, than the others.
```javascript
Returns an object: {"latitude": centerLat, "longitude": centerLng}
```
### geolib.isPointInside(object latlng, array polygon)

Checks whether a point is inside of a polygon or not. Note: the polygon coords must be in correct order!

Returns true or false

Example
```javascript
geolib.isPointInside(
    {latitude: 51.5125, longitude: 7.485},
    [
        {latitude: 51.50, longitude: 7.40},
        {latitude: 51.555, longitude: 7.40},
        {latitude: 51.555, longitude: 7.625},
        {latitude: 51.5125, longitude: 7.625}
    ]
); // -> true
```
### geolib.isPointInCircle(object latlng, object center, integer radius)

Similar to is point inside: checks whether a point is inside of a circle or not. Returns true or false
Example
```javascript
// checks if 51.525, 7.4575 is within a radius of 5km from 51.5175, 7.4678
geolib.isPointInCircle(
    {latitude: 51.525, longitude: 7.4575},
    {latitude: 51.5175, longitude: 7.4678},
    5000
);
```
### geolib.getRhumbLineBearing(object originLL, object destLL)

Gets rhumb line bearing of two points. Find out about the difference between rhumb line and great circle bearing on Wikipedia. Rhumb line should be fine in most cases: http://en.wikipedia.org/wiki/Rhumb_line#General_and_mathematical_description Function is heavily based on Doug Vanderweide's great PHP version (licensed under GPL 3.0) http://www.dougv.com/2009/07/13/calculating-the-bearing-and-compass-rose-direction-between-two-latitude-longitude-coordinates-in-php/ Returns calculated bearing as integer.
Example
```javascript
geolib.getRhumbLineBearing(
    {latitude: 52.518611, longitude: 13.408056}, 
    {latitude: 51.519475, longitude: 7.46694444}
);
```
### geolib.getBearing(object originLL, object destLL)

Gets great circle bearing of two points. See description of getRhumbLineBearing for more information. Returns calculated bearing as integer.
Example
```javascript
geolib.getBearing(
    {latitude: 52.518611, longitude: 13.408056}, 
    {latitude: 51.519475, longitude: 7.46694444}
);
```
### geolib.getCompassDirection(object originLL, object destLL, string bearingMode (optional))

Gets the compass direction from an origin coordinate (originLL) to a destination coordinate (destLL). Bearing mode. Can be either circle or rhumbline (default). Returns an object with a rough (NESW) and an exact direction (NNE, NE, ENE, E, ESE, etc).
Example
```javascript
geolib.getCompassDirection(
    {latitude: 52.518611, longitude: 13.408056}, 
    {latitude: 51.519475, longitude: 7.46694444}
);
//Output
{
    rough: 'W',
    exact: 'WSW'
}
```
### geolib.orderByDistance(object latlng, mixed coords)

Sorts an object or array of coords by distance from a reference coordinate Returns a sorted array [{latitude: x, longitude: y, distance: z, key: property}]
Examples
```javascript
// coords array
geolib.orderByDistance({latitude: 51.515, longitude: 7.453619}, [
    {latitude: 52.516272, longitude: 13.377722},
    {latitude: 51.518, longitude: 7.45425},
    {latitude: 51.503333, longitude: -0.119722}
]);

// coords object
geolib.orderByDistance({latitude: 51.515, longitude: 7.453619}, {
    a: {latitude: 52.516272, longitude: 13.377722},
    b: {latitude: 51.518, longitude: 7.45425},
    c: {latitude: 51.503333, longitude: -0.119722}
});
```
### geolib.findNearest(object latlng, mixed coords[[, int offset], int limit])

Finds the nearest coordinate to a reference coordinate.

Examples
```javascript
var spots = {
    "Brandenburg Gate, Berlin": {latitude: 52.516272, longitude: 13.377722},
    "Dortmund U-Tower": {latitude: 51.515, longitude: 7.453619},
    "London Eye": {latitude: 51.503333, longitude: -0.119722},
    "Kremlin, Moscow": {latitude: 55.751667, longitude: 37.617778},
    "Eiffel Tower, Paris": {latitude: 48.8583, longitude: 2.2945},
    "Riksdag building, Stockholm": {latitude: 59.3275, longitude: 18.0675},
    "Royal Palace, Oslo": {latitude: 59.916911, longitude: 10.727567}
}

// in this case set offset to 1 otherwise the nearest point will always be your reference point
geolib.findNearest(spots['Dortmund U-Tower'], spots, 1)
```
### geolib.getPathLength(mixed coords)

Calculates the length of a collection of coordinates

Returns the length of the path in meters

Example
```javascript
// Calculate distance from Berlin via Dortmund to London
geolib.getPathLength([
    {latitude: 52.516272, longitude: 13.377722}, // Berlin
    {latitude: 51.515, longitude: 7.453619}, // Dortmund
    {latitude: 51.503333, longitude: -0.119722} // London
]); // -> 945235
```
### geolib.getSpeed(coords, coords[, options])

Calculates the speed between two points within a given time span. Returns the speed in options.unit (default is km/h).
Example
```javascript
geolib.getSpeed(
    {lat: 51.567294, lng: 7.38896, time: 1360231200880},
    {lat: 52.54944, lng: 13.468509, time: 1360245600880},
    {unit: 'mph'}
); // -> 66.9408 (mph)
```
### geolib.isPointInLine(object point, object start, object end

Calculates if given point lies in a line formed by start and end. Returns true or false
Examples
```javascript
var point1 = {latitude: 0.5, longitude: 0};
var point2 = {latitude: 0, longitude: 10};
var point3 = {latitude: 0, longitude: 15.5};
var start  = {latitude: 0, longitude: 0};
var end    = {latitude: 0, longitude: 15};

var isInLine1 = geolib.isPointInLine(point1, start, end) //-> false;
var isInLine2 = geolib.isPointInLine(point2, start, end) //-> true;
var isInLine3 = geolib.isPointInLine(point3, start, end) //-> false;
```
### geolib.convertUnit(string unit, float distance[, int round])

Converts a given distance (in meters) to another unit.

Parameters

unit can be one of:

m (meter)
km (kilometers)
cm (centimeters)
mm (millimeters)
mi (miles)
sm (seamiles)
ft (foot)
in (inch)
yd (yards)
distance distance to be converted (source must be in meter)

round fractional digits

Example
```javascript
geolib.convertUnit('km', 14213, 2) // -> 14,21
```
### geolib.sexagesimal2decimal(string coord)

Converts a sexagesimal coordinate to decimal format

Example
```javascript
geolib.sexagesimal2decimal("51° 29' 46\" N")
```
### geolib.decimal2sexagesimal(float coord)

Converts a decimal coordinate to sexagesimal format

Example
```javascript
geolib.decimal2sexagesimal(51.49611111); // -> 51° 29' 46.00
```

### geolib.latitude(object latlng)

### geolib.longitude(object latlng)

### geolib.elevation(object latlng)

Returns the latitude/longitude/elevation for a given point and converts it to decimal.

Works with:

longitude: longitude, lng, lon, 0 (GeoJSON array)
latitude: latitude, lat, 1 (GeoJSON array)
elevation: elevation, elev, alt, altitude, 2 (GeoJSON array)
Examples
```javascript
geolib.latitude({lat: 51.49611, lng: 7.38896}); // -> 51.49611 geolib.longitude({lat: 51.49611, lng: 7.38896}); // -> 7.38896
```
### geolib.useDecimal(mixed latlng)

Checks if a coordinate is already in decimal format and, if not, converts it to

Example
```javascript
geolib.useDecimal("51° 29' 46\" N"); // -> 51.59611111
geolib.useDecimal(51.59611111) // -> 51.59611111
```
### geolib.computeDestinationPoint(start, distance, bearing, radius(optional))

Computes the destination point given an initial point, a distance and a bearing If no radius is given it defaults to the mean earth radius of 6371000 meter. Returns an object: `{"latitude": destLat, "longitude": destLng}` (Attention: this formula is not *100%* accurate (but very close though))
Example
```javascript
var initialPoint = {lat: 51.516272, lon: 0.45425}
var dist = 1234;
var bearing = 45;

geolib.computeDestinationPoint(initialPoint, dist, bearing);
// -> {"latitude":51.52411853234181,"longitude":0.4668623365950795}
```
