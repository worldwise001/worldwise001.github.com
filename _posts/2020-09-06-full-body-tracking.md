---
author: worldwise001
layout: post
title: Streaming with Full-Body Tracking in VR
---

![Motion Capture of Miku playing Beat Saber](/images/2020-09-06-full-body-tracking/preview.gif)

**If you are looking for instructions on how to set up Full Body Tracking with the HTC Vive, you can skip to the following sections:**
- HTC Vive Trackers Setup
- Goodbye Custom Mods; Hello LIV

## Introduction

I've been a huge fan of [VR](https://en.wikipedia.org/wiki/Virtual_reality) ever since we saw some of the first few consumer products hit the market in around 2015/2016, and so back then I got myself a HTC Vive headset and controllers and upgraded aspects of my gaming PC to accommodate the more strenuous requirements at the time. The game [Beat Saber](https://store.steampowered.com/app/620980/Beat_Saber/) has particularly piqued my interest (in addition to a few other discoveries such as [Space Pirate Trainer](https://store.steampowered.com/app/418650/Space_Pirate_Trainer/), [SUPERHOT](https://store.steampowered.com/app/322500/SUPERHOT/), and [Pistol Whip](https://store.steampowered.com/app/1079800/Pistol_Whip/)). I also like occasionally streaming my ventures on [Twitch](https://www.twitch.tv/worldwise001). However there are some significant differences when trying to stream from the perspective of a typical keyboard+mouse/controller non-VR game, versus from the perspective of a controller/tracker VR game.

## Current VR Setup

The setup I've had for some time is an original HTC Vive Headset from around 2016, complete with the 2016 era controllers. It comes with two lightboxes, which are used to track the position of the headset and controllers with a remarkable amount of accuracy and smoothness.

The VR headset itself supports a resolution of 2160×1200, which is about 1080×1200 per eye. To support this sort of resolution, you generally needed a pretty beefy graphics card at the time, so here mine is supported by a GTX 1080.

![Lightbox 1](/images/2020-09-06-full-body-tracking/lightbox1.png) ![Lightbox 2](/images/2020-09-06-full-body-tracking/lightbox2.png)

![HTC Vive Headset and Controllers](/images/2020-09-06-full-body-tracking/headset.png)

![Gaming Desktop](/images/2020-09-06-full-body-tracking/computer.png)

## Streaming VR vs non-VR

All the VR games generally have a non-VR window that is displayed on the desktop monitor. You can easily capture that output using some popular off-the-shelf software, such as OBS. The view is somewhat indicative of what's visible in the headset, usually from one eye, but of course unless the game supports adjusting the FOV, the default FOV has a hard time showing everything that's going on. There's also the problem of, unlike controlled in a mouse/keyboard/controller situation, human bodies actually sometimes have very jerky movement. As the wearer of the headset you don't notice since your eyes naturally locks onto targets while you move, however for external viewers it can be very disorienting.

![Desktop Capture of Space Pirate Trainer](/images/2020-09-06-full-body-tracking/spt-1p.gif)

![Desktop Capture of Beat Saber](/images/2020-09-06-full-body-tracking/beatsaber-1p.gif)

Compare that to this of Halo: Reach where you can get a better field perspective of what's going on.

![Desktop Capture of Halo: Reach](/images/2020-09-06-full-body-tracking/halo-reach.gif)

## Adding Mods to Beat Saber

The first thought I had was trying to figure out how to get a third-person camera view working with Beat Saber. I had seen some folks demonstrate that capability on YouTube in the past, so I knew it was possible, and I figured it was through modding of some kind since modding is fairly popular in that community.

#### ModAssistant

There are a few different guides to aid first-time modders, including the [BSMG Wiki](https://bsmg.wiki/pc-modding.html) and [Beast Saber](https://bsaber.com/installing-the-mod-guide-necessary-for-any-custom-songs/); I used some combination of these guides. While many claim that you can technically set everything up manually, what you probably want to use is a piece of software called [ModAssistant](https://github.com/Assistant/ModAssistant). I was remarkably surprised at how easy this was to set up. You download the [latest release](https://github.com/Assistant/ModAssistant/releases), and simply run the executable. It doesn't actually install anything, so keep that executable aside from your Downloads folder if you don't want to lose it.

![ModAssistant Startup Screen](/images/2020-09-06-full-body-tracking/modassistant.png)

I love some of the user-friendliness of this windows. Note the Game Version and the ModAssistant version tracked in the lower left. You have to click "I Agree", which will install the basic scaffolding (probably by overwriting certain DLLs/binaries). If nothing seems to be happening, you can click on the "Mods" icon on the left hand side. That brings up something similar to the following window:

![ModAssistant Mods Screen](/images/2020-09-06-full-body-tracking/modassistant1.png)

Perhaps non-intuitively, you want to click "Install or Update" which will install all the mods indicated by the Checked Boxes. It should then show the green installed version numbers. As you can tell there are some core libraries that are generally required for all the other mods; digging into the source code it seems like it provides a bunch of useful convenience methods for modders.

An important caveat is that the Beat Saber releases change semi-frequently, and this seems to be a frequent source of breakage. I don't remember the last time I had to handle this, but if you are fine with wiping away your mods and starting fresh, you can do the following:

- Open your Steam library, and uninstall Beat Saber. Note that this will not remove the modded content.
- Navigate to your Beat Saber install directory and nuke the contents. This is usually `<your steam install folder>/steamapps/common/Beat Saber`.
- Open up Steam again and isntall Beat Saber.
- Repeat the above steps to reinstall mods.

#### Setting up CameraPlus

*If you're using this as a howto, skip to "__Goodbye Custom Mods; Hello LIV__"*

CameraPlus is a Beat Saber mod to configure custom cameras (specifically for popular third-person cameras) to view the player in Beat Saber. It commonly is also used in tandem with Custom Avatars. Setting up CameraPlus is only partially intuitive. It's easy to install through [ModAssistant](https://github.com/Assistant/ModAssistant), as shown in the screenshot above, but it does take some finagling to get the view working as you intended. Some of the gotchas I ran into include:

- The currently maintained source for CameraPlus is by [Snow1226](https://github.com/Snow1226/CameraPlus) not [xyonico](https://github.com/xyonico/CameraPlus) (the original author). Digging into commit history seems to show a change in maintainership a few times, but it's good to see that this is still actively being maintained for bugfixes and features.
- CameraPlus uses the existing Desktop window screen to render its view. It turns out if you enable Smooth Camera, that overwrites the CameraPlus settings so you're stuck with still a first-person view. However infuriatingly, you don't notice until you record yourself in-game, since in the menu screens, CameraPlus appears to work perfectly.
- Configuring the camera involves [watching a YouTube video](https://www.youtube.com/watch?v=RpYoMiKJygQ). If you are discovering CameraPlus through ModAssistant and bad Google SEO, you will not easily find any of these things.

Once you have CameraPlus install, the way to configure it is from the Desktop Window of Beat Saber, not from within the VR headset. If you right-click on the window, the CameraPlus menu interface pops up and you can do things such as add/remove cameras, and switch between First-Person and Third-Person view.

![CameraPlus Menu and Changing Perspectives](/images/2020-09-06-full-body-tracking/cameraplus-menu.gif)

If you're not happy with the Third-Person camera view, you can put on the VR headset and move it around with your pointer. Hold the trigger to grab it, let go of the trigger to let it go.

![CameraPlus Adjusting the Camera](/images/2020-09-06-full-body-tracking/cameraplus-camera-adjust.gif)

At the end of the day, I didn't end up using CameraPlus for my streaming since I found LIV to be slightly easier to set up. But more on that later.

#### Setting up Custom Avatars

*If you're using this as a howto, skip to "__Goodbye Custom Mods; Hello LIV__"*

The [Beat Saber Custom Avatars](https://github.com/nicoco007/BeatSaberCustomAvatars) mod is intended to pair with [CameraPlus](https://github.com/Snow1226/CameraPlus) to provide a representation of your person holding the sabers in-game. Unfortunately for some reason this isn't installed through [ModAssistant](https://github.com/Assistant/ModAssistant), so you have to follow the instructions yourself. Note you probably don't need to manually install [DynamicOpenVR](https://github.com/nicoco007/DynamicOpenVR/releases), since that is provided by ModAssistant. However you do need to download and install (read: extract the contents) of [Custom Avatars](https://github.com/nicoco007/BeatSaberCustomAvatars/releases) in your Beat Saber install directory, i.e. usually `<your steam install folder>/steamapps/common/Beat Saber`.

At the time of this writing, there's actually [a bug](https://github.com/nicoco007/BeatSaberCustomAvatars/issues/58#issuecomment-676755378) with Custom Avatars, so check the thread and download the pre-release rc2 version to see if it fixes the bug for you (it did for me).

Once the files are in, the left screen upon startup now has a Mods Tab with an Avatars section you can configure. From there you can choose the appropriate avatar you want to "wear". Note that the menu show Full-Body Tracking support, however the legs move really weird when we pick a Full-Body Avatar. That's because this is only using trackers on the headset and the controllers aka hands, and so the avatar "guesses" where your feet might want to be and move them appropriately.

![Configuring Beat Saber Custom Avatar](/images/2020-09-06-full-body-tracking/beatsaber-custom-avatar-config.gif)

There's one problem with using the Template Avatar and that's this:

![Beat Saber playthrough using Template Avatar](/images/2020-09-06-full-body-tracking/beatsaber-boring.gif)

#### Finding Avatars

If you don't want to use the stock standard boring avatars, you should go look for [some online](https://bsaber.com/avatars/). Note that not all avatars support Full-Body Tracking, and actually if you are more savvy with 3D modeling software (e.g. Blender), there are a few guides out there, such as [this one](https://bs.assistant.moe/Avatars/). But if you're not into that, I recommend adding `tag:FBT` and `tag:Full Body Tracking` to refine your search results. I am currently using [Sour Miku Black v2](https://modelsaber.com/Avatars/?id=1564625718&pc).

## Full-body Tracking

Okay now we have some promising possibilities here with [CameraPlus](https://github.com/Snow1226/CameraPlus) and [Custom Avatars](https://github.com/nicoco007/BeatSaberCustomAvatars), however we are stuck with headset+hand tracking only with body interpolation and that's not cool. So how do we set up full-body tracking?

#### Emulating Vive Trackers with Kinect

*Note: this does not use LIV, and sets up virtual vive trackers using a Kinect. It probably is not what you want!*

TBD, but it's gnarly.

#### HTC Vive Trackers Setup

You'll want three trackers, two for your feet and one for your waist. This is the typical setup for most Full-Body tracking situations. The trackers themselves only come with a camera mount for attachment, so it's up to you to figure out how to attach them to your person. I went ahead and bought the [TrackBelt straps](https://www.amazon.com/gp/product/B07DFBR6Y3) from Amazon and they work well enough. Note: despite the picture, the straps DO NOT come with trackers! Each Vive Tracker currently retails for $99 USD.

It's fairly easy to set up a single HTC Vive Tracker. It's harder to set up 3. Kudos to [this reddit user](https://www.reddit.com/r/Vive/comments/67jaqd/how_to_pair_3_extra_trackers/dgs3au8/) for figuring out the careful set of steps, reposted here:

- Plug in Tracker A Dongle
- Plug Tracker A into a wired USB hub that is not occupied by the Tracker A Dongle
- See SteamVR find Tracker A (it will be lit green).
- Unplug Tracker A's wired USB cable.
- Tracker A will now be gray, but you can right-click on it
- Right click on Tracker A in SteamVR, select "pair Tracker"
- Hold button on Tracker A for 4 seconds until blue light is flashing
- Tracker will pair (sometimes can take 2-3 tries, be patient)
- Turn off Tracker A (right click in SteamVR and power off Tracker. If you do it by holding the button down for six seconds 'till it powers off you may force it to un-pair because it will cycle through the pairing process again)
- UNPLUG TRACKER A DONGLE
- Plug in Tracker B Dongle and repeat all steps with Trackers B, and then C
- Plug in all 3 Tracker Dongles, Turn on both controllers, and then power up all 3 Trackers. You should see everything lit up.

If you have lost track of Tracker A, B, etc. I recommend labeling your dongles and your trackers:

![Labeled Dongles](/images/2020-09-06-full-body-tracking/dongles.png)

![Labeled Trackers](/images/2020-09-06-full-body-tracking/trackers.png)

R = Right foot, L = Left food, W = Waist.

## Goodbye Custom Mods; Hello LIV

I spent a lot of time trying to get avatar setup with Beat Saber back in April 2020. At the time I was armed with only a Kinect, which provided emulated Vive trackers to try and make the feet and waist visible to Beat Saber Custom Avatar. All the results at the time were extremely poor; the avatar would twist itself/get mangled, which was just not a good look. I had to start thinking about moving soon after, so I dropped that project.

Since then, I got the HTC Vive Trackers and attempted the whole setup process again. I couldn't figure out how to get it working with Custom Avatar out of the box, and also by that time I had spent hours trying to get them configured and pairing properly on the computer. Through some more googling, I discovered a piece of software called [LIV](https://liv.tv/). While I was fully expecting to have to watch some more YouTube or jump on a Discord to figure out how to install the software, I was pleasantly surprised that you could just [download the software on Steam](https://store.steampowered.com/app/755540/LIV/). So that's what I did.

The [guide](https://guide.liv.tv/What-is-LIV-and-what-do-we-do-a1a2ba8313454e28aca16c989283c334) is fairly straightforward regarding setup. You have [install the LIV driver](https://guide.liv.tv/Install-LIV-Driver-b7e903e78867413285e6692e9251350a) which provides more virtual trackers into SteamVR, and typically you also need to [start the compositor](https://guide.liv.tv/Create-Avatar-Virtual-Camera-Profile-c09654baa68a45ffbbc8ad69460ccbd8) any time you want to stream. The compositor provides a nice window separate from the game window and separate from the Steam VR mirror display. For this reason I recommend disabling the Steam VR mirror display, and to reduce the resolution of the native game mirroring if you don't want to stress out your video card.

![Disable Steam Room VR Mirroring](/images/2020-09-06-full-body-tracking/steam-vr-no-room.gif)

Adding and configuring an avatar is very easy with LIV. I recommend for each new play session to re-calibrate your avatar to the trackers, since you're likely putting on the trackers slightly differently each time, and otherwise it will distort your avatar to viewers.

![Calibrating T-Pose of LIV Avatar](/images/2020-09-06-full-body-tracking/liv-calibrate-tpose.gif)

If you are wanting to fiddle with the LIV camera settings I recommend using a gamepad or controller.

![Configuring LIV Camera Part 1](/images/2020-09-06-full-body-tracking/liv-config-1.gif)

![Configuring LIV Camera Part 2](/images/2020-09-06-full-body-tracking/liv-config-2.gif)

![Configuring LIV Camera Part 3](/images/2020-09-06-full-body-tracking/liv-config-3.gif)

## Streaming with StreamLabs OBS

Now when it comes to connecting all the pieces together to actually stream your VR content, you'll need to probably get an account on a streaming website such as [Twitch](https://twitch.tv) or [YouTube](https://youtube.com). You'll also need streaming software; I used to use [OBS](https://obsproject.com/) but more recently I've switched to [StreamLabs](https://streamlabs.com/) which has roots in OBS. If you connect your Twitch account to StreamLabs, it will set up the output stream more or less automatically; alternatively if you have a prior OBS profile it can import those settings. If all else fails, you can refer to the [Twitch Streaming Configuration Suggestions](https://stream.twitch.tv/).

Within OBS or StreamLabs, you have the opportunity to set up different Scenes; this more or less shows different kinds of views on the stream, and is commonly used with Transitions for cases where you have a custom Scene for intermissions, breaks, etc. from gameplay. I am not a serious streamer, so I just use it to organize different game streaming "profiles" depending on what kind of game I want to stream. Within a Scene you can configure a number of different Sources, that can range from captured window output to custom media to custom browser-based UI widgets.

![Sample StreamLabs view](/images/2020-09-06-full-body-tracking/streamlabs.png)

Speaking of widgets...

#### Setting up Beat Saber Visual Stats in Stream

![Cropped visual of Beat Saber stats during play](/images/2020-09-06-full-body-tracking/beatsaber-stats.gif)

This one is wholly optional and has nothing to do with Full-Body Tracking but I found it to be a really nice indicator on the stream of specifically the song and difficulty level that I was currently playing. It's primarily powered by [Beat Saber HTTP Status](https://github.com/opl-/beatsaber-http-status) which exposes Beat Saber stats on a local websocket, and then from there you can host a UI that listens periodically to that port. It's specifically very nice for browser/HTTP-based UI widgets that can be added to your favorite capture software.

Interestingly I vaguely remembered this mod being available on ModAssistant, but I couldn't find it. However it seems at the time of this writing, the most recent version of HTTP Status supports one patch version behind the current one, so I suspect it's not shown for those reasons. So instead you can install it manually. If that doesn't work out of the box, it's fairly trivial to use `strings` and `xxd` to find the Beat Saber version hardcoded in the DLL, and change it slightly to support the most recent version.

On its own, this mod doesn't do anything, so you'll need to find something that reads from the websocket and provides an overlay. For that I just cloned [reselim's Beat Saber Overlay](https://github.com/Reselim/beat-saber-overlay), and published it via github pages. I then created a browser UI widget and pointed it to the appropriate URL, which for me is https://worldwise001.github.io/beat-saber-overlay/.

![Screenshot of StreamLabs Widget Configuration for Beat Saber Stats](/images/2020-09-06-full-body-tracking/beatsaber-stats.png)

## Final Thoughts

Overall this project happened over several hours of research distributed over several days; originally I embarked on this quest in April, and it took time for (a) the Kinects to arrive for testing, (b) the Vive Trackers to arrive for testing, and (c) for me to move to a different country and set up VR in a better space. But I am fairly pleased with the final results, and so I hope this post will help connect the dots of some of the articles that were scattered around the internet, or at the very least provide some entertainment value documenting my different roadblocks along the way.

Good luck and happy VRing!

![Motion Capture of Miku Waving](/images/2020-09-06-full-body-tracking/miku.gif)
