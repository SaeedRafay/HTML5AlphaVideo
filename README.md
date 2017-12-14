# Interstitial HTML5 Video With Transparent Background

HTML5 does not have alpha support for videos. This package shows you how to use transparency in your video and use it as an interstitial intro on your webpage. It loads 1 second after all elements in your webpage are fully loaded.

## How To Do It?

1. Create your animation or video with no background in After Effects

2. Export a web friendly video (h264/mp4) with RGB channel
https://github.com/SaeedRafay/HTML5AlphaVideo/raw/master/images/rgb.png

3. Export the same video again but this time with Alpha channel
https://github.com/SaeedRafay/HTML5AlphaVideo/raw/master/images/alpha.png

4. Now stack both video one above the other like shown below
https://github.com/SaeedRafay/HTML5AlphaVideo/raw/master/images/stacked_videos.jpg

5. Add code for HTML5 video
```sh
<div id="video_holder"> 
    <video id="video" style="display:none">
        <source src="compressed.mp4" type='video/mp4; codecs="avc1.42E01E"' />
        <source src="compressed.ogv" type='video/ogg; codecs="theora, vorbis"' />
    </video>
    <canvas width="480" height="488" id="buffer"></canvas>
    <canvas width="480" height="244" id="output"></canvas>
</div>
```

6. Add following Javascript code
```sh
window.addEventListener("load", function(){

	setTimeout(function() {

		var outputCanvas = document.getElementById('output'),
			output = outputCanvas.getContext('2d'),
			bufferCanvas = document.getElementById('buffer'),
			buffer = bufferCanvas.getContext('2d'),
			video = document.getElementById('video'),
			width = outputCanvas.width,
			height = outputCanvas.height,
			interval;
			
		function processFrame() {
			buffer.drawImage(video, 0, 0);
			
			// this can be done without alphaData, except in Firefox which doesn't like it when image is bigger than the canvas
			var	image = buffer.getImageData(0, 0, width, height),
				imageData = image.data,
				alphaData = buffer.getImageData(0, height, width, height).data;
			
			for (var i = 3, len = imageData.length; i < len; i = i + 4) {
				imageData[i] = alphaData[i-1];
			}
			
			output.putImageData(image, 0, 0, 0, 0, width, height);
		}

		video.play();

		video.addEventListener('play', function() {
			clearInterval(interval);
			interval = setInterval(processFrame, 30);
		}, false);
		
		video.addEventListener('ended', function() {
			//Use the following line to hide video when the video ends
			//document.getElementById('video_holder').style.display = "none";

			//Use video.play() if you want to loop over the video
			video.play();
		}, false);

	}, 1000);

});
```

7. Add following CSS code

```sh
#video_holder {
	position: fixed;
	top: 50%;
	left: 50%;
	transform: translate(-50%, -50%);
}
#output {
	width: 480px;
	height: 244px;
}
#buffer {
	display: none;
}
```
