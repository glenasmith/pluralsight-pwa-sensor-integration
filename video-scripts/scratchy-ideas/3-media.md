
## Start with an Album

You'll need to fetch this from localstorage or firebase or ?
But images will need to stay on the device until you get network again.



## The Specs

Modern web browsers support a set of APIs called [Media Capture and Streams](https://www.w3.org/TR/mediacapture-streams/). These APIs allow you to access inputs such as microphones and cameras, and to a limited extent they also let you record and visualize the data.

Browsers of yet greater modernity support an API called [MediaStream Recording](https://www.w3.org/TR/mediastream-recording/). This API makes recording and processing the data from these inputs really easy and fun.

## Photos

[Be Inspired by Cheese](https://polymer-cheese.firebaseapp.com/)

[Great article](https://developers.google.com/web/fundamentals/media/capturing-images/) covers basics.

You could start with a basic:

```
<input type="file" accept="image/*" capture>
```

and let the user choose. Or you could be specific:

```
<input type="file" accept="image/*" capture="user">
```

for the selfie camera, or the main camera 

```
<input type="file" accept="image/*" capture="environment">
```

But that only works on phones. Desktops will prompt for an upload.

## Wire it up to a img element

img.src = URL.createObjectURL(file);


## Working with Fullscreen Mode

video = document.querySelector("video");

 video.requestFullScreen =
      video.requestFullScreen ||
      video.webkitRequestFullScreen ||
      video.mozRequestFullScreen;

function fullScreenVid(evt) {
    video.requestFullScreen();
}


## Sensing Media Support

```
    const supported = 'mediaDevices' in navigator;
```

Warning: Direct access to the camera is a powerful feature. It requires consent from the user, and your site MUST be on a secure origin (HTTPS).

## Applying Instagram Image Filters

Via CSS is quick ala [CSSgram](https://una.im/CSSgram/)
Or something [more serious](https://github.com/girliemac/filterous-2) which alters the image itself

## Asking permission to record stuff

Give a compelling reason, and in the right context. Not too early.

## Selecting Available Devices

If you're using a PC, microphone will matter

## Showing a Video element, Capturing to a Canvas

You could rework our Photo capture to use this technique. 
Remember: ask for permission appropriately.

Start progressive.
```
<input type="file" accept="video/*" capture>
```
Then pipe that into an <video> element and you're off


## Recording an Audio stream

Sample principles as video.

Start progressive.
```
<input type="file" accept="audio/*" capture>
```
Then pipe that into an <audio> element and you're off

Visualise on a cool audio element

## Canvas Gotchas

* Remember to set canvas height and width (or things get blurring/downsampled). And I don't mean CSS width, I mean the canvas.width of the object)

* There is a experimental Photo API called ImageCapture - but it's not in any browsers except Chrome (behind a flag?) -- https://developer.mozilla.org/en-US/docs/Web/API/ImageCapture

* You can [request full screen mode](https://developer.mozilla.org/en-US/docs/Web/API/Fullscreen_API) if you want the video element to take up all the real estate. Then perhaps use a click to take the pic? 


## Media Fragments

To add a media fragment, you simply add #t=[start_time][,end_time] to the media URL. For example, to play the video between seconds 5 through 10, specify:

<source src="video/chrome.webm#t=5,10" type="video/webm">

IOS doesn't support this, but most other platforms do.

## Working with Poster Images

Add a poster attribute to the video element so that your users have an idea of the content as soon as the element loads, without needing to download video or start playback.

<video poster="poster.jpg" ...>
  ...
</video>

A poster can also be a fallback if the video src is broken or if none of the video formats supplied are supported. 


## Working with ChromeCast

Take your video and share it with the world!

