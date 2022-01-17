# MultiMedia-Project
**In this project i will stream the stored videos usingHTTP protocol** 

Ffmpeg video generation commands :
------
The cleanest way to force I-frame positions using FFmpeg is to use the 
**{-x264opts ‘keyint=24:min-keyint=24:no-scenecut’}**  argument.
1- -x264opts allow you to use additional options for the x264 encoding lib.
2- keyint sets the maximum GOP (Group of Pictures) size, which is the group of 3- frames contained between two I-Frames. More info on GOPs.
3- min-keyint sets the minimum GOP size.
4- no-scenecut removes key-frames on scene cuts.

` ffmpeg -y -i video.mp4 -c:a aac -ac 2 -ab 128k -c:v libx264 -x264opts 'keyint=24:min-keyint=24:no-scenecut' -b:v 1500k -maxrate 1500k -crf 28 -bufsize 1000k -vf "scale=-1:720" video720.mp4 `
 
` ffmpeg -y -i video.mp4  -c:a aac -ac 2 -ab 128k -c:v libx264 -x264opts 'keyint=24:min-keyint=24:no-scenecut' -b:v 800k -maxrate 800k  -crf 33 -bufsize 500k -vf "scale=-1:540" video540.mp4 `
 
 
` ffmpeg -y -i video.mp4  -c:a aac -ac 2 -ab 128k -c:v libx264 -x264opts 'keyint=24:min-keyint=24:no-scenecut' -b:v 400k -maxrate 400k  -crf 44 -bufsize 400k -vf "scale=-1:360" video360.mp4 `

To choose the value of the GOP (distance between two keyframes) size, you’ll need to take in account the length of the segments you want to generate: segment length * framerate has to be a multiple of the GOP size.
Example: if the frame rate is 24 and you want 2-seconds segments, the GOP size needs to be either 48 or 24). Know that if your GOP size is too big, the seek might not work properly in some players: as for quality switching, the player has to seek out an I-frame to resume the streaming.

__Audio Extraction :__

` ffmpeg -i input-video.avi -vn -acodec copy output-audio.aac `

__Video Extraction :__

` ffmpeg -i video-with-audio.mp4 -c copy -an video.mp4` 

__MP4BOX :__
------
 
The DASH manifest, a .mpd file (Media Presentation Description), is an XML providing the identification and location of the above items, particularly the urls where the media files are hosted. To play the stream, the DASH player simply needs the manifest, as it fetches each part of the video needed from the information contained in the manifest. According to the network and CPU status, the player will choose the segment from the most suitable representation to deliver a stream with no buffering.

` MP4Box -dash 2000 -frag 2000 -rap -segment-name 'segment$RepresentationID$' -fps 24 video_144.mp4#video:id=144 video_360.mp4#video:id=360 video_720.mp4#video:id=720 video_1080.mp4#video:id=1080:role=main Black_Audio.m4a#audio:id=sound -out manifest/manifestBlack.mpd `

