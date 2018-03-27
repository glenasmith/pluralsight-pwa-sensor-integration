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

## Capturing Audio and Video

We'll need some audio capture card:

    <div class="card">

            <h1>Audio Memories</h1>

            <audio id="audio" src="[[audioUrl]]" controls></audio> 
            
            <paper-button raised class="cyan" on-click="takeAudio">
                    Record Audio
            </paper-button>

            <div style="display: none;">
                <input id="audioInput" type="file" accept="audio/*" capture
                                on-change="uploadAudio">
            </div>


    </div>

And some backing code:

            uploadAudio(evt) {
                var files = Array.from(evt.target.files);
                if (files && files.length) {
                    var file = files[0];
                    var audioUrl = URL.createObjectURL(file);
                    this.$.audio.src = audioUrl;
                    this.set('audioUrl', audioUrl);
                }

            }

## WEbcam Memories

I'll need a video element:

    <div class="card">

            <h1>Webcam Memories</h1>

            <video id="photoPreview" autoplay></video>

    </div>

And a button:

      <paper-button raised class="indigo" >
               <iron-icon icon="image:add-a-photo"></iron-icon>
                    Webcam Photo
            </paper-button>

And an on-click to get started:

    on-click="startCamera"

And a start camera function.

            startCamera() {
                const photoPreview = this.$.photoPreview;

                let constraints = {
                    video: true,
                    audio: false,
                };

                navigator.mediaDevices.getUserMedia(constraints)
                    .then((stream) => {
                        photoPreview.srcObject = stream;
                        this.set('cameraActive', true);
                    }, (err) => {
                        console.log('User rejected camera capture permissions', err);
                        this.set('captureRejected', true);
                    });


            }

## Enumerating Devices


On page.ready() invoke:

    this.listDevices();


Then we'll need a listDevices() method to invoke:


            listDevices() {
                let self = this;
                navigator.mediaDevices.enumerateDevices()
                            .then((devices) => {
                                devices.forEach((device) => {
                                    console.log(
                                        " deviceId = " + device.deviceId +
                                        " kind = " + device.kind +
                                        " label = " + device.label +
                                        " groupId = " + device.groupId
                                    );
                                })
                            })
                    .catch(function (err) {
                        console.log(err.name + ": " + err.message);
                    });
            }

And some props:

    selectedCameraIdx: {
        type: Number,
        value: 0,
    },
    cameraDevices: {
        type: Array,
        value: []
    }


Then catch the cameras

    let cameras = [];

    if (device.kind === 'videoinput') {
        cameras.push(device);
    }
    self.set('cameraDevices', cameras);

Wire up a select:

        <paper-dropdown-menu label="Video Source">
                <paper-listbox slot="dropdown-content" selected="{{selectedVideoIdx}}">
                    <template is="dom-repeat" items="[[cameraDevices]]">
                        <paper-item>[[item.label]] ([[item.deviceId]])</paper-item>
                    </template>
                </paper-listbox>
        </paper-dropdown-menu>  

  
Ok switch cameras on startup:

        var cameraDevices = this.get('cameraDevices');
        var comboSelection = this.get('selectedCameraIdx');
        
        var selectedCamera = cameraDevices[comboSelection];

        let constraints = {
                    video: {
                        deviceId: selectedCamera.deviceId
                    },
                    audio: false,
                };


## Grabbing a still image



Add a takePicture function:

    takePicture() {
        
        const photoPreview = this.$.photoPreview;
        const photoCanvas = this.$.photoCanvas;
        const photoContext = photoCanvas.getContext('2d');

        photoContext.drawImage(photoPreview, 0, 0, photoCanvas.width, photoCanvas.height);
        this.togglePhotoCaptureControls(false);
        this.stopCamera(photoPreview);

    }


Stop camera:

    stopCamera(photoPreview) {
            photoPreview.srcObject.getVideoTracks().forEach(track => track.stop());
    }

## Video Diary

Enhance listDevices:

             listDevices() {

                navigator.mediaDevices.enumerateDevices()
                    .then((devices) => {
                        let cameras = [];
                        let microphones = [];
                        devices.forEach((device) => {
                            console.log(
                                " deviceId = " + device.deviceId +
                                " kind = " + device.kind +
                                " label = " + device.label +
                                " groupId = " + device.groupId
                            );
                            if (device.kind === 'videoinput') {
                                cameras.push(device);
                            }
                            if (device.kind === 'audioinput') {
                                microphones.push(device);
                            }
                        })
                        this.set('cameraDevices', cameras);
                        this.set('audioDevices', microphones);
                    })

            }

Add some markup:

    <div class="card">
        <h1>Video Diary</h1>

        <video id="videoPreview"></video>

        <paper-dropdown-menu label="Camera Source">
            <paper-listbox slot="dropdown-content" selected="{{selectedCameraIdx}}">
                <template is="dom-repeat" items="[[cameraDevices]]">
                    <paper-item>[[item.label]] ([[item.deviceId]])</paper-item>
                </template>
            </paper-listbox>
        </paper-dropdown-menu>

        <paper-dropdown-menu label="Audio Source">
            <paper-listbox slot="dropdown-content" selected="{{selectedAudioIdx}}">
                <template is="dom-repeat" items="[[audioDevices]]">
                    <paper-item>[[item.label]] ([[item.deviceId]])</paper-item>
                </template>
            </paper-listbox>
        </paper-dropdown-menu>

        <paper-button raised class="orange" on-click="recordVideo">
            <iron-icon icon="image:add-a-photo"></iron-icon>
            Record Video
        </paper-button>

        </div>


Then implement a record audio:

    recordVideo() {

                const videoPreview = this.$.videoPreview;

                let cameraDevices = this.get('cameraDevices');
                let cameraComboSelection = this.get('selectedCameraIdx');
                let selectedCamera = cameraDevices[cameraComboSelection];

                let audioDevices = this.get('audioDevices');
                let audioComboSelection = this.get('selectedAudioIdx');
                let selectedAudio = audioDevices[comboSelection];


                let constraints = {
                    video: {
                        deviceId: selectedCamera.deviceId
                    },
                    audio: {
                        deviceId: selectedAudio.deviceId
                    }
                }

            }

Start playback with mute:

     navigator.mediaDevices.getUserMedia(constraints)
                    .then((stream) => {
                        videoPreview.srcObject = stream;

                        // Not we mute so we don't get a mic feedback loop
                        videoPreview.muted = true;

                        // And we play to start the media streaming.
                        videoPreview.play();

Start API config:

    var recordOptions = {
                            audioBitsPerSecond : 128000,
                            videoBitsPerSecond : 2500000,
                            mimeType : 'video/webm'
                        }

Start recording:

    var mediaRecorder = new MediaRecorder(stream, recordOptions);
                        this.set('mediaRecorder', mediaRecorder);
                        mediaRecorder.start(1000);

On Data Available:

                        let chunks = [];

                        mediaRecorder.ondataavailable = function(e) {
                             chunks.push(e.data);
                        }   

On Stop:

    mediaRecorder.onstop = function(e) {

        videoPreview.srcObject.getAudioTracks().forEach(track => track.stop());
        videoPreview.srcObject.getVideoTracks().forEach(track => track.stop());
        videoPreview.srcObject = null;
    
        videoPreview.muted = false;

        let recordedVideo = self.get('recordedVideo');
        console.log(recordedVideo);
        var blob = new Blob(recordedVideo, { 'type' : 'video/webm' });
        
        videoPreview.src = URL.createObjectURL(blob);

    }                                             

Our video element:

    <video id="videoPreview" controls></video>
