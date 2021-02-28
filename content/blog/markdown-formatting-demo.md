---
title: "Visualizing Git Repositories"
date: 2020-12-16T12:49:27+06:00
featureImage: images/allpost/allPost-1.png
postImage: images/single-blog/feature-image.png
---


TLDR: This are the high level steps to create visualizations for your own Git repositories as the example below.

- Use Gource for creating the git visualization PPM frames

- Use FFmpeg for converting PPM frames to MPEG/AVI (any codec) of your choice

- Use combination of sox and FFmpeg to add background music to the generated video (Optional). I did this step because I am anyways uploading this to YouTube).

You can find all the pre-rendered videos here. Do like and subscribe the channel if you like it, helps me a lot in keeping me motivated to keep doing these fun stuffs.


## Introduction:

The side-project I did was to create the git visualizations as attached above for top 100 starred projects in GitHub. Aim is to provide an End of Year update for 2020 for the aforementioned open-source projects.

This seemed a trivial task but, boy o' boy, I struggled to get this working properly. So, lets's go through each steps in detail below:



## Step 1: Gource | FFmpeg | sox

Download and install Gource, FFmpeg and sox(optional) on your OS of choice. For FFmpeg and sox, the binary has to be added to path but steps should be relatively easy. Gource puts the binary in path automatically but if it doesn't just the binary dirt to the path manually.

## Step 2: Clone Repo Of Choice

Clone the GitHub repo you want to create the visualizations of. (Ex. Kubernetes)

``` js
git clone https://github.com/kubernetes/kubernetes.git
```

## Step 3: Run Gource and FFmpeg

To generate the video: cd in to the cloned directory and run the command

cd kubernetes

``` js
gource --hide filenames,dirnames,usernames,progress,mouse --font-size 20 --seconds-per-day 0.1 --auto-skip-seconds 1 -1920x1080 -o - | ffmpeg -y -r 60 -f image2pipe -vcodec ppm -i - -vcodec libx264 -preset ultrafast -pix_fmt yuv420p -crf 1 -threads 0 -bf 0 kubernetes.mp4
```
## Step 4: Prepare Audio to be mixed (Optional)

This was the first hurdle as my audio length was shorter than the video length and I wanted to loop the audio till the length of my video. Easy said and hard to do. But luckily, StackOverflow to the rescue and I got a quick fix.

So, I am using EpidemicSound for my background music and the usual track length for my genre of choice was ~3 mins. I used sox, to create a new mp3 file with an input mp3 looped 5 times. For ex. input mp3 -> 3 mins, then output mp3 would be 15 mins.

This way my audio file would generally be longer than the video file. You'll see why I did this later in the next section. To do this copy the file in the same cloned folder and run the following command:

``` js
sox input_music.mp3 looped_output_music.mp3 repeat 5
```

Now, you would have a new looped_output_music.mp3 file of 5 times the length of the input mp3.

## Step 5: Do the actual music mux (Optional)

FFmpeg has a nifty feature where you can mux the audio and video with a flag(shorter) that will clip the audio where the video ends. So, on the running the command below, you would have the final video clip with audio

``` js
ffmpeg -i kubernetes.mp4 -i looped_output_music.mp3 -shortest final_video.mp4
```

Go Ahead and watch the video! You're done!

## Orchestration Challenges:

The above process will help you in generating the visualization for a single repository. But, my use case was to generate it for top 100 repositories. And hence, the challenge for orchestration. I solved it by creating a bunch of shell scripts triggered by cron as below:

- One off script - retrieve top 100 repository urls and clone everything in a folder

- Recurring - take 10 repository batch and generate raw Gource video
- Recurring - take the raw files one by one and mix it by randomly selected mp3 from a folder.

I would also like to at some point of time to upload to YouTube programatically but this is manual for now :)

As of writing this article, there are 38 videos in my playlist and I don't think it'll take a long time to reach 100.



Hope you find it useful. Please message me if you need any help, I'll try my best to solve it for you.

You can visit my playlist below: