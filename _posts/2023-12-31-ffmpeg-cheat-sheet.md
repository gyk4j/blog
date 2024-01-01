---
layout: post
title:  "FFMPEG cheat sheet"
---

Springcleaning up my hard disk for the year.

Found a few random batch files and shell scripts and decided to consolidate 
these commands into a cheat sheet.

Majority of the information are taken from FFMPEG's 
[H.264 Video Encoding Guide][h264] and [MPEG-4 Encoding Guide][asp].

**Note**: Newlines have been added to each switch/parameter for readability.

# Getting metadata

Format can be: `csv`, `compact`, `json`, `xml`, `flat` etc.

```shell
ffprobe -v quiet -print_format xml -show_format file.avi
ffprobe -v quiet -print_format json -show_format file.mp4
ffprobe -hide_banner -print_format flat -show_format file.wmv
```

# Transcoding (and scaling) to MP4 H.264

**Note**: `-pix_fmt yuv420p` is always required for Explorer to make a thumbnail from the video.

## Convert AVI to MP4 (H.264) and resize to 240p Full HD resolution

```shell
ffmpeg 
  -i input.avi 
  -c:v libx264 
  -pix_fmt yuv420p 
  -vf scale=320:240 
  -c:a aac 
  output.mp4
```

## With advanced audio and video codec settings

- Hide unnecessary information output
- Video
  - Let video width be variable preserving aspect ratio, while downsizing to 240p
  - Use `veryfast` x264 preset for faster encoding
  - Use `main` H.264 profile for more complex but higher compression/efficiency encoding
- Audio
  - Stereo channels
  - 44.1 kHz bitrate
- Prefix metadata at file beginning for Internet/Web streaming
  - `movflags`
  - `faststart`

```shell
ffmpeg 
  -hide_banner 
  -nostats 
  -i input.wmv 
  -vf "scale=-2:240,format=yuv420p" 
  -c:v libx264 
  -preset veryfast 
  -profile:v main 
  -c:a aac 
  -ac 2 
  -ar 44100 
  -b:a 128k 
  -movflags 
  +faststart 
  output.mp4
```

# Benchmarking and comparing video codec quality

- Used for comparing MPEG-4 (Advanced Simple Profile) and MPEG-4 (H.264)

## Playing 2 video files side-by-side for comparison

Information from 
[Comparing video quality by viewing videos side by side][compare-side-by-side].

```shell
ffplay -f lavfi "movie=left.mp4,scale=iw/2:ih[v0];movie=right.mp4,scale=iw/2:ih[v1];[v0][v1]hstack"
```

Simpler example without resizing video to half.

```shell
ffplay -f lavfi "movie=left.mp4[v0];movie=right.mp4[v1];[v0][v1]hstack"
```


## Transcode to AVI container with MPEG-4 (ASP) video track and MP3 audio track

Good ol' days of DivX and Xvid. 'nuff said.

Create a video file largely resembling those in the 1990s and 2000s compatible 
with older DivX players

```shell
ffmpeg 
  -y
  -i input.mp4 
  -c:v mpeg4 
  -vtag xvid 
  -pix_fmt yuv420p 
  -c:a mp3 
  -b:a 128k 
  output.avi
```

## Transcode to MP4 container with MPEG-4 (ASP) video track and AAC audio track

Another variant with video quality `6` and mono audio channel at 64k.

Uses MP4 container for output.

Probably for smaller lower quality transcodings when we don't care about the 
audio.

```shell
ffmpeg
  -i input.wmv
  -pix_fmt yuv420p
  -c:v libxvid
  -q:v 6
  -ac 1
  -c:a aac
  -b:a 64k
  output.mp4
```

## Transcode to MP4 container with MPEG-4 (H.264) video track and AAC audio track

Modern format. Slower transcoding.

Uses `-tune film` for optimal compression bitrate?

People would usually use 160k audio bit rate or higher, but I am tone-deaf so I
couldn't tell any difference between 96k, 128k, 160k, 192k and 256k.

```shell
ffmpeg 
  -y 
  -i input.mp4
  -c:v libx264 
  -profile:v main 
  -preset veryfast 
  -tune film 
  -pix_fmt yuv420p 
  -c:a aac 
  -b:a 128k 
  output.mp4
```

# Slideshows and videos from still images

Sometimes we want to turn a directory of images into a slideshow video file, 
maybe with a 1 second display interval for each image.

Information mostly taken from [here][slideshow].

We can use FFMPEG to make one without using another software.

We can add tacky transition animations or effects like those early slideshow 
software if we need it. FFMPEG supports a few of them.

## Create MP4 slideshow from 1 photo or a series of JPEG images

- Useful for generating a video file from JPEG photos taken with a mobile 
  phone, such as shopping site's review video clips.
- Image files must be named and numbered in sequence (e.g. 000.jpg ... 999.jpg)
  - Name may be incremented by the `-loop 1` switch? **(Untested)**
- Create a slideshow lasting `10` seconds in total (i.e. # of images X interval seconds)
- Use a silent audio track (downloadable from Internet with various length/duration)
  **(Forgot why I wanted/needed to add a silent audio track)**
- Forgot why I needed `-shortest`. Taken from suggestion/example online.

Maybe [Shopee](https://shopee.sg) or [Lazada](https://lazada.sg) was 
rejecting my review's video file upload without an audio channel, which 
does not happen with real video files taken with a mobile phone?

```shell
ffmpeg 
  -loop 1 
  -t 10 
  -i %03d.jpg 
  -i silence.mp3 
  -c:v libx264 
  -vcodec libx264 
  -pix_fmt yuv420p 
  -profile:v baseline 
  -preset veryfast 
  -crf 22 
  -c:a aac 
  -b:a 128k 
  -shortest 
  output.mp4
```

## Create MP4 slideshow from a series of PNG images

An easier example using PNG images without audio track.

```shell
ffmpeg 
  -loop 1 
  -t 10 
  -i %03d.png 
  -c:v libx264 
  -vcodec libx264 
  -pix_fmt yuv420p 
  -profile:v baseline 
  -preset veryfast 
  -crf 22 
  -shortest 
  output.mp4
```
## Create MP4 slideshow from JPEG images with fixed output resolution

Sometimes our images may be from different sources, or for whatever reasons of 
different dimensions.

Uses image scaling resizing if larger than output resolution
- Preserves aspect ratio
- Pad with black bars if required?

```shell
ffmpeg
  -f image2
  -framerate 1
  -i %01d.jpg
  -vf "scale=1920:1080:force_original_aspect_ratio=decrease,pad=1920:1080:(ow-iw)/2:(oh-ih)/2" ^
  -c:v libx264
  -preset veryslow
  -crf 18
  -pix_fmt yuv420p
  -metadata year="2017"
  output.mp4
```

# Transcoding MP3 to MP4 audio file

Convert audio files to standard M4A or MP3 format.

Or to downscale high fidelity audio files to smaller low bitrate files.

## Convert MP3 audio file into M4A (MP4) audio file

- Uses modern AAC codec suitable for playback on iPhone/Android mobile phones
- Uses low 64k sampling, sufficient for human voice or podcast recordings
- Uses low 22050 bitrate, good enough for voice recording for small files 

```shell
ffmpeg 
  -i input.mp3 
  -c:a aac 
  -b:a 64k 
  -ar 22050 
  output.m4a
```

## Add album cover and metadata to audio file

Album should ideally be:

- Square
- Sized at 300 pixels by 300 pixels

Information taken from [Baeldung][cover-art] and [Multimedia.cx][metadata].

```shell
ffmpeg
  -i input.mp3
  -i cover.jpg
  -map 0:0
  -map 1:0
  -c copy
  -id3v2_version 3
  -metadata title="My Song Title"
  -metadata album_artist="John Doe"
  -metadata artist="John Doe"
  -metadata album="My Album"
  -metadata date="2023"
  -metadata genre="Pop"
  -metadata copyright="2023"
  -metadata publisher="Publishing Studio Limited"
  -metadata composer="Alice, Bob"
  -metadata:s:v title="Cover (Front)"
  -metadata:s:v comment="Cover (Front)"
  output.mp3
```

[h264]: https://trac.ffmpeg.org/wiki/Encode/H.264
[asp]: https://trac.ffmpeg.org/wiki/Encode/MPEG-4
[compare-side-by-side]: https://nrecursions.blogspot.com/2023/01/comparing-video-quality-by-viewing.html
[slideshow]: https://trac.ffmpeg.org/wiki/Slideshow
[cover-art]: https://www.baeldung.com/linux/terminal-music-add-album-art
[metadata]: https://wiki.multimedia.cx/index.php/FFmpeg_Metadata