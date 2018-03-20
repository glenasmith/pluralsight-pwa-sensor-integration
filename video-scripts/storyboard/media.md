# Media Script



Input controls.

    <h1>First Memories</h1>

    

    <input id="takeSelfie" type="file" accept="image/*" capture="user">
    <input id="takeLandmark" type="file" accept="image/*" capture="environment">

And a placeholder figure:

    <figure class="app-grid">
        <img id="selfie" src="../../images/avatar-placeholder.svg">
    </figure>

And styling:

    figure {
        --app-grid-columns: 1;
    }

Add paper buttons:

    <paper-button raised class="indigo"
                    on-click="takeSelfie">
        selfie
    </paper-button>
    <paper-button raised 
                class="green" 
                on-click="takeLandmark">
        landmark
    </paper-button>

And hide the native input controls.

    <div style="display: none;">

Invoke the underlying `input` control.

    takeSelfie() {
        this.$.takeSelfie.click();
    }


    takeLandmark() {
        this.$.takeLandmark.click();
    }


Catch the change event

    <input id="takeSelfie" type="file" accept="image/*" capture="user" on-change="uploadPhoto">
    <input id="takeLandmark" type="file" accept="image/*" capture="environment" on-change="uploadPhoto">

Implement that uploadPhoto call


    uploadPhoto(evt) {
        var files = Array.from(evt.target.files);
        if (files && files.length) {
            var file = files[0];
            this.$.selfie.src = URL.createObjectURL(file);
        }
    }