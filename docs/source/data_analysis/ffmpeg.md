# Useful `ffmpeg` workflows for working with video data


`ffmpeg` is a very powerful tool to work with video data, but has a few gotchas that can be tricky. Below some recommendations for common workflows and the rationale behind them.


<!-- ## A brief primer on video encoding
- GoP? -->

## Re-encoding videos to ensure reliably seekable frames
For all the rest: recommended to re-encode video first

To re-encode (from SLEAP DOCS, with request for timestamps unchanged)
```bash
ffmpeg -y -i input_video.avi \
-c:v libx264 \
-pix_fmt yuv420p \
-preset superfast \
-crf 23 \
-vstats \ # dump video coding stats to vstats_HHMMSS.log file
-fps_mode passthrough \ # to pass timestamps unchanged
output_video.mp4
```

- `-y` : overwrite without asking
- `-c`: codec (encoder if applied to input or decoder if applied to output)
- `-c:v _nameofcodec_` --> specified the codec applied to the video (v) stream. If you use `copy` as name, the stream is not re-encoded
	- alternatively `-vcodec`
- `-pix_fmt`: this is mostly for videos to play [in Quicktime and most others](https://trac.ffmpeg.org/wiki/Encode/H.264#Encodingfordumbplayers). These players only support the YUV planar color space with 4:2:0 chroma subsampling for H.264 video.
- `-preset`
- `-crf` is the quality setting for x264 encoder. A CRF of 15 is nearly lossless



## Counting frames in a video

```bash
ffprobe -v error -select_streams v:0 -count_frames -show_entries stream=nb_read_frames -of csv=p=0 <path-to-video>
```
Note that we use `nb_read_frames` rather than `nb_frames`; the latter is faster but less accurate because it gets the frames from the header of the container.


## Check fps of a video

```bash
ffprobe -v error -select_streams v:0 -show_entries stream=r_frame_rate -of csv=p=0 <path-to-video>
```

## Extracting frames by index from video as image files
* It is sometimes unclear in ffmpeg whether frames are counting starting from 0 or 1
* You can use a select filter (which we know uses 0-based because the docs say so), but it is a bit cumbersome (see [here](https://superuser.com/questions/1009969/how-to-extract-a-frame-out-of-a-video-using-ffmpeg))
```bash
 ffmpeg -i input.mp4 -vf "select='eq(n\,10)+eq(n\,50)+eq(n\,100)'" -vsync vfr frame_%03d.png
```
Tip — for many frames, it's cleaner to use a script:
```bash
frames=(10 50 100 200 350)
for f in "${frames[@]}"; do
  ffmpeg -i input.mp4 -vf "select='eq(n\,$f)'" -vsync vfr "frame_${f}.png"
done
```
This spawns one ffmpeg call per frame, which is slower but easier to manage for large lists.
* You can change the fps before extracting...? (https://shotstack.io/learn/ffmpeg-extract-frames/)

Recommended for sanity: sleap-io or similar, it provides an array-like interface for a video, then use PIL or imageio to save the file

```python
import sleap_io as sio
import imageio.v3 as iio

video = sio.load_video(video_path)

for frame_idx in list_frames_idcs:
    frame = video[frame_idx] # numpy array, RGB, uint8

    # Save as PNG with imageio
    iio.imwrite(f"frame_{frame_idx:0>5d}.png", frame)

```

Note that sleap-io does not re-encode — it reads frames from the original file using whichever backend you have installed (imageio-ffmpeg by default, or OpenCV/PyAV optionally). If the video is not reliably seekable (e.g. a poorly encoded AVI), you can still get wrong frames, same as with raw ffmpeg. the recommendation in your ffmpeg.md to re-encode first still applies even when using sleap-io. sleap-io makes the frame access interface nicer, but it doesn't solve the underlying seekability problem of the source video.

## Extracting a clip from a video
* Recommended to always check at the end if the total number of frames in the output clip is what you expect! (See section "Counting frames in a video")

* Input seeking
- in theory for ffmpeg > 2.1 and without stream `copy` it is frame accurate


* Output seeking


* Stream copy: if there is stream `copy` there is no frame-accuracy, because the stream that is passed to the `input` and `output` gates is not decoded into frames. Even if used with "output" syntax.

* In seeking: how does ffmpeg decide if a frame is in or out?
frame_PTS: the start time of the frame (PRESENTATION TIMESTAMP = PTS)
- `-to` -----> means "include frame if frame_PTS < `-to` value"
- `-ss`------> means "include frame if frame_PTS >= `-ss` value"

* when using `-ss` or `-to` gates, what maters is the START TIME OF THE FRAME, sometimes called the frame PRESENTATION TIMESTAMP or frame PTS. From Claude:

> Output `-ss X` is a gate function i.e. allow only elements starting with timestamp X. Whether elements are decoded frames or original packets depend on the codec option set for that stream.
So if the stream that goes thru the "output gate" is in groups of pictures (e.g. because we have `copy` and the video is not transcoded), then it will let pass one full group of pictures

Note: [Syntax to express time duration in ffmpeg commands](https://ffmpeg.org/ffmpeg-utils.html#time-duration-syntax)

## Combine image files into a video

See here: https://hamelot.io/visualization/using-ffmpeg-to-convert-a-set-of-images-into-a-video/

```bash
ffmpeg -r 60 -f image2 -s 1920x1080 -i pic%04d.png -vcodec libx264 -crf 25  -pix_fmt yuv420p test.mp4
```

```bash
ffmpeg -r 30 -f image2 -s 1024x576 -start_number 11384 -i /Users/sofia/swc/project_movement_dataloader/bboxes-datasets/face_track_annotation/images/%08d.jpg -vcodec libx264 -crf 25  -pix_fmt yuv420p test.mp4
```
There's a common point of confusion: when extracting frames to image files, FFmpeg defaults to starting output filenames at 1 (e.g., frame0001.png), but internally the frame numbers in filters still use 0-based indexing. You can override this with -start_number 0.


## Further reading
* ffmpeg docs
* ffmpeg wiki
- [Frame accuracy when seeking](https://fftrac-bg.ffmpeg.org/wiki/Seeking)
- [Main options](https://ffmpeg.org/ffmpeg.html#Main-options)
- [Video options](https://ffmpeg.org/ffmpeg.html#Video-Options)
- [FFMPEG's H.264 guide seems useful](https://trac.ffmpeg.org/wiki/Encode/H.264#a1.ChooseaCRFvalue)
	- links to [Understanding rate control modes](https://slhck.info/video/2017/03/01/rate-control.html) and [What is CRF](https://slhck.info/video/2017/02/24/crf-guide.html)
