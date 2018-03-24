# Media Script




## Basic Image Capture

Input controls.

    <h1>Basic Memories</h1>

    <input id="photoInput" type="file" accept="image/*" capture>

And a placeholder figure:

    <figure>
        <img id="photo" src="../../images/avatar-placeholder.svg">
    </figure>

And styling:

    img {
        max-width: 90%;
    }

And hide the native input controls.

    <div style="display: none;">

Add paper buttons:

    <paper-button raised class="indigo" on-click="takePhoto">
            Take Photo
    </paper-button>



Invoke the underlying `input` control.

    takePhoto() {
        this.$.photoInput.click();
    }



Catch the change event

    <input id="photoInput" type="file" accept="image/*" capture="user" on-change="uploadPhoto">

Implement that uploadPhoto call

    uploadPhoto(evt) {
        var files = Array.from(evt.target.files);
        if (files && files.length) {
            var file = files[0];
            var selfieUrl = URL.createObjectURL(file)
            this.$.photo.src = selfieUrl;
        }
    }

## Applying Instagram Filters

* All canned code for this. No live coding.

## Full Screen API

Ad the click handler to the image:

    on-click="toggleFullScreenPhoto"

And the implementation:

    toggleFullScreenPhoto() {

        var img = this.$.photo;

        img.requestFullScreen = img.requestFullScreen ||
            img.webkitRequestFullScreen ||
            img.mozRequestFullScreen;

        if (img.requestFullScreen) {
            img.requestFullScreen();
        } else {
            console.log("Fullscreen not supported");
        }
    }