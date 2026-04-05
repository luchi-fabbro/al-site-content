---
title: "IP Camera Fetcher"
description: "An experiment to see if AppleScript was still viable for building a real application — turns out it was more capable than expected."
date: "2011-09-23"
tags:
  - AppleScript
  - Mac
  - Growl
  - IP Camera
draft: false
---

I decided to find out whether AppleScript was still a viable and useful tool for building a real application, so I sat down with the AppleScript editor and wrote something from scratch.

On launch, the app prompts the user for three things: a destination folder for the captured images, the URL of the IP-based camera, and the cycle rate at which it should grab stills. From there it runs on a timer, quietly pulling images and naming them by timestamp.

I didn't stop there. I added a logging subroutine (accessible via Console at `~/Library/Logs/ipcamera-gather-events.log`), Growl notification support for image capture and error alerts, and a timestamp-based filename generator that turned out to be more involved than I expected.

I had written very little AppleScript before this — quick launchers, the odd check to see whether an application was installed. I was genuinely surprised to find I could build object-oriented functions with it. It holds up better than its reputation suggests.

Here's what an hour of captured frames from a Seattle street camera looks like when stitched together:

<video controls width="100%" style="margin: 1.5rem 0; border-radius: 4px;">
  <source src="https://cdn.datanut.net/assets/videos/Fremont%20Ave%20N%20Seattle%20-%20IP%20Street%20Camera.mp4" type="video/mp4" />
  Your browser does not support the video tag.
</video>

## The code

```applescript
(*
This application was written to help collect camera images
-- set camUrl to "http://www.seattle.gov/trafficcams/images/Fremont_36.jpg?1316748399706"
*)

log_event("**********Startup Initialization**********")

property script_name : "IP Camera Image Capture"
property written_by : "Angelo Luchi"
property script_description : "This script will be a basic application for gathering images from an IP camera and storing them on your computer. It will prompt you for a destination folder, camera URL, and interval in seconds."
property camUrl : "http://www.seattle.gov/trafficcams/images/Fremont_36.jpg"
property intervalSeconds : "60"

global deskFol1

try
    set growlCheck to createGrowlPipe()
    log_event("Register Growl Application: " & growlCheck)
    if growlCheck is true then
        log_event("Growl Application Name: " & script_name)
    end if

    set deskFol1 to chooseFolder()
    log_event("Selected Destination Folder: " & deskFol1)
    log_event("POSIX Path: " & POSIX path of deskFol1)

    enterUrl()
    log_event("Property camUrl: " & camUrl)

    enterInterval()
    log_event("Property intervalSeconds: " & intervalSeconds)

    log_event("Starting Image Capture")
    log_event("Trying " & camUrl & " every " & intervalSeconds & " seconds.")
    sendNotification("Error Alert", "Trying " & camUrl & " every " & intervalSeconds & " seconds.")
on error
    sendNotification("Error Alert", "User Canceled Operations!")
    quit application
end try

on idle
    set fileName1 to makeFileName()
    set destination_file to (deskFol1 & fileName1)
    set thisImageSRC to camUrl
    set newError to false

    try
        collectURLImage(thisImageSRC, destination_file)
    on error
        set newError to true
        set p to POSIX path of destination_file
        do shell script "rm " & p
    end try

    if newError is false then
        log_event("Collected Image: " & fileName1)
        sendNotification("Image Creation", "Collected Image: " & fileName1)
    else
        log_event("Error fetching URL: " & thisImageSRC)
        sendNotification("Error Alert", "Error fetching URL: " & thisImageSRC)
    end if

    return intervalSeconds
end idle

on quit
    log_event("**********Terminating Application**********")
    continue quit
end quit

-- Logging
on log_event(themessage)
    set theLine to (do shell script "date +'%Y-%m-%d %H:%M:%S'" as string) & " " & themessage
    do shell script "echo " & theLine & " >> ~/Library/Logs/ipcamera-gather-events.log"
end log_event

-- Growl
on testGrowl()
    tell application "System Events"
        set isRunning to (count of (every process whose name is "Growl")) > 0
    end tell
    return isRunning
end testGrowl

on createGrowlPipe()
    set test to testGrowl()
    if test is true then
        try
            tell application "Growl.app"
                set the allNotificationsList to {"Image Creation", "Error Alert"}
                set the enabledNotificationsList to {"Image Creation", "Error Alert"}
                «event register» given «class appl»:script_name, «class anot»:allNotificationsList, «class dnot»:enabledNotificationsList, «class iapp»:"Collect IP Camera Images"
                return true
            end tell
        end try
    else
        return false
    end if
end createGrowlPipe

on sendNotification(notification, msg)
    set test to testGrowl()
    if test is true then
        try
            tell application "Growl.app"
                «event notifygr» given «class name»:notification, «class titl»:script_name, «class desc»:msg, «class appl»:script_name
            end tell
        end try
    end if
end sendNotification

-- Filename generator
on makeFileName()
    set theDate to the current date
    copy theDate to b
    set the month of b to January
    set monthNum to (1 + (theDate - b + 1314864) div 2629728)
    if (monthNum < 10) then
        set monthNum to "0" & (monthNum as string)
    end if
    set yearNum to the year of theDate
    set dateNum to the day of theDate
    if (dateNum < 10) then
        set dateNum to "0" & dateNum
    end if
    set hourNum to the hours of theDate
    if (hourNum < 10) then
        set hourNum to "0" & hourNum
    end if
    set minuteNum to the minutes of theDate
    if (minuteNum < 10) then
        set minuteNum to "0" & minuteNum
    end if
    set secondNum to the seconds of theDate
    if (secondNum < 10) then
        set secondNum to "0" & secondNum
    end if
    set theFileName to (yearNum as string) & (monthNum as string) & (dateNum as string) & (hourNum as string) & (minuteNum as string) & (secondNum as string) & ".jpg"
    return theFileName
end makeFileName

-- Prompts
on chooseFolder()
    set theVar to (choose folder with prompt "Please choose a folder to save the images to.") as string
    return theVar
end chooseFolder

on enterUrl()
    set theIcon to note
    repeat
        display dialog "Please enter a camera URL:" default answer camUrl with icon theIcon
        set camUrl to text returned of result
        try
            if camUrl = "" then error
            exit repeat
        on error
            set theIcon to stop
        end try
    end repeat
    return camUrl
end enterUrl

on enterInterval()
    set theIcon to note
    repeat
        display dialog "Please enter a time interval in seconds (> 10):" default answer intervalSeconds with icon theIcon
        set intervalSeconds to text returned of result
        try
            if intervalSeconds < 10 then error
            set intervalSeconds to intervalSeconds as number
            exit repeat
        on error
            set theIcon to stop
        end try
    end repeat
    return intervalSeconds
end enterInterval

-- Image fetch via curl
on collectURLImage(urlAddress, destinationPath)
    set input to quoted form of urlAddress
    set temp_file to POSIX path of destinationPath
    set q_temp_file to quoted form of temp_file
    set cmd to "curl -o " & q_temp_file & " " & input
    do shell script cmd
end collectURLImage
```

## Download

The application, source code, and an example stop motion of captured images are available here:

[Download IPCamera AppleScript 0.26](https://cdn.datanut.net/assets/downloads/IPCamera_Applescript_0.26.zip)
