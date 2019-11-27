+++ 
date = 2015-04-18T16:24:14-05:00
title = "Using HandBrake on a Mac to convert video from a Hubsan H107D"
description = ""
slug = "converting-video-from-a-hubsan-h107d-using-handbrake-cli" 
tags = ["Hubsan", "Howto"]
categories = []
externalLink = ""
series = []
+++

I've been playing with a [Hubsan H107D](http://www.amazon.com/Hubsan-H107D-FPV-Mini-Quadcopter/dp/B00GSNWB5K/ref=sr_1_1?ie=UTF8&qid=1429404325&sr=8-1&keywords=hubsan+h107d) quadcopter lately. If you happen to own one of these, you've probably discovered by now that the video written to the MicroSD card is not very usable without some conversion. It is stored as a 720x240 AVI by default, and appears rather...squashed. To top this off, VLC complains about a missing/broken AVI index. 

The following HandBrakeCLI command will convert the video to an MP4 and correct the aspect ratio. 

    HandBrakeCLI -i 001.AVI -o 001.mp4 -e x264 -q 22 -w 720 -l 480

## If HandBrakeCLI is not installed

Install it using [Homebrew](http://brew.sh). 

    brew install Caskroom/cask/handbrakecli

## Converting an entire folder

If you'd rather convert an entire folder of video files, use something like this: 

    #!/bin/bash

    DEST_EXT=mp4
    HANDBRAKE_CLI=HandBrakeCLI

    for FILE in `ls $1`
    do
        filename=$(basename $FILE)
        extension=${filename##*.}
        filename=${filename%.*}

        $HANDBRAKE_CLI -i $1/$FILE -o $1/$filename.$DEST_EXT -e x264 -q 22 -r 30 -w 720 -l 480
    done

And then...

    ./convert_video.sh ~/My/Directory

## Next Time...

My next goal is to find an easy way to stitch these together so the entire flight is contained in one video.