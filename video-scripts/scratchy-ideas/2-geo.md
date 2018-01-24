
# Geo and Location Stuff

This is going to be intrusive, so create real value.
* Always request on a gesture ("Find near me")

Why use location?
* Tailor experience (I'm thinking your in Sydney? Show local stores)
* Show you on a map (find landmarks around you)
* Markup data (the photo was taken here)

## Fallback to IP Address lookup geo?


## Realtime or one-off

* Use the [right API](https://developers.google.com/web/fundamentals/native-hardware/user-location/) getCurrentPosition() vs watchPosition()

## Using geoOptions for caching

Prevents using any device features, battery, etc if there's recent data:

```
var geoOptions = {
    maximumAge: 5 * 60 * 1000,
  }
navigator.geolocation.getCurrentPosition(geoSuccess, geoError, geoOptions);
```

## High precision

YOu don't need to be inside a metre if you're finding a landmark. Higher battery drain.

```
var geoOptions = {
 enableHighAccuracy: true
 }
```


## Testing locations in Dev Tools

Using the Sensors tab in the Console to set your location to London.

## Integrating with Maps

Putting your location on a Map

## Showing places of interest

Find me landmarks near here

## Deep Linking: Launch native apps: Integrating with Directions ? 

Launching native apps with a Get Directions button
Getting directions from here to there
You just need to [create the right maps url](https://developers.google.com/maps/documentation/urls/guide) and magic will happen 
Something like:

```
https://www.google.com/maps/dir/?api=1&destination=Pike+Place+Market+Seattle+WA&travelmode=bicycling 
```


## Device Orientation and Motion

Compass example?