# Random Notes

Need to serve over https if asking for access to camera, etc.


## Icons

I'm always trying to find them. They're [here](https://www.webcomponents.org/element/PolymerElements/iron-icons/demo/demo/index.html)

## Photos

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


## Build Issues

git config  url."https://".insteadOf git://

