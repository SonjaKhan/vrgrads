---
title: Asset Exploration and Prototyping
layout: blog_post
date: 2016-04-20
img: exploration.jpg
thumbnail: exploration-thumbnail.jpg
alt: image-alt
category: update
description: Early lessons learned
crumbs: 
- text: Home
  url: /
- text: Blog
  url: /blog
---

## What we accomplished 

This week we spent time going deeper into our exploration of pre-existing roller coaster and rail assets for building tracks, and also started designing a UI for the build experience. We bought two different assets this week: a mesh deformer ($8) and a tracks and rails kit from Zen Fulcrum ($45). Sonja worked on investigating the mesh deformer kit and testing the limitations of that kit, while Jenny and Riley worked on investigating the Zen Fulcrum kit and the provided animation abilities of that kit. Nick worked on prototyping a UI to allow users to select the next track-type to build with, given a set of predetermined track shapes. We also met up to discuss changes to our PRD and have since updated our PRD to add support for saving and loading a build session much sooner in the quarter.


## Plan for Next Week
For next week, we will continue our investigation of various roller coaster track kits, run experiments to test the limits and capabilities of each, and ultimately decide on a track kit to use moving forward. We want to pick a track kit soon so that we can start working on our deliverable for the week. Our goal is to have a simple working prototype of the build interaction for our application, that will allow a user to select and add a segment of track from a tool menu, and watch a simple animation of a box moving along the track, constrained by physics.


## Notes
The two track kits that we purchased allow a user to add new segments to an existing track, as well as curve or twist the track to get more interesting designs. Both kits also provided support for simple animation of a cart or train along the tracks, though from different angles. While the two track kits we purchased this week have similar basic functionality for building, they had different limitations. For instance, with the mesh deformer kit, we found that the shape of the track warped if the track was stretched too far. We could accommodate for this by setting an upper limit on how far a segment of track can be stretched, and guiding the user to add more intermediary pieces instead. 

The Zen Fulcrum kit did not seem to have this problem when stretched, but scaling of the assets is not currently supported, and this posed a few problems. First, even when scaled down, the track parts remember their original length and use those endpoints when connecting to other tracks. This makes it look like there are missing sections of track where two pieces ought to be connected. The underlying representation of the track, however, still sees these pieces as joined, even though it does not appear that way to the user. The Zen Fulcrum kit includes a script to “snap” a shape to the track, so that the shape can move along it. However, after scaling a track, the cart will ignore the apparent gaps between track pieces and still move along the original endpoints, so that it appears to be moving across empty space rather than falling through the gap. Secondly, the inability to scale is an issue because when viewed on the Hololens, the track segments appear to be very large. Playing with moving the track farther from the origin and from the viewing camera affected the user’s perception of the size of the track, and may be one workaround. 

The mesh deformer allows for scaling of the included assets in Unity, which was necessary because we have to scale down everything to about 3% of what would be considered normal in a normal 3D application. One interesting thing was that when our model was not scaled down (therefore very large) and far from the user, the origin shifted a lot when walking around. This problem appeared to fix itself when scaling down the model and placing it closer to the user, although there is still a bit of shifting. 

The major issue with the mesh deformer asset is performance. We created a predefined track made of approximately 10 curves, with the ability to add more. On a laptop the model took a few seconds to load at the start but adding additional curve segments during runtime was quick. On the HoloLens, however, loading the initial track took slightly longer and dynamically adding more tracks froze the application for about three seconds each time the user added a curve. This is an unacceptable amount of lag, and we want the building process to be as quick and enjoyable as possible.

While we could find workarounds for these various limitations, we want to instead invest a bit more time exploring other track assets and mesh kits so that we can spend less time overcoming these obstacles and more time working on fleshing out our critical features and exploring our stretch goals.

The initial UI prototype provides the user with a toolkit menu of sorts, displaying images of predetermined track options that the user can select to continue building off of their existing creation. At this stage, we think it is important for the UI to be simple and functional, but there are a few directions we would like to explore once the basic functionality is set. For instance, one idea would be to have a holographics chest or box of some sort containing the predefined track pieces, such that a user could grab the actual hologram of the next piece they would like to place, and snap it to the end of an existing track. This experience would more accurately model the process of building with physical train set kits.


<div class="row">
	<div class="col-lg-12 col-md-12 col-sm-12">
	    <img src="{{ "/img/mesh-deformer.png" | prepend: site.baseurl }}" class="img-responsive img-centered image-max" alt="" />
	</div>
</div>

The Mesh Deformer Asset

<div class="row">
	<div class="col-lg-12 col-md-12 col-sm-12">
	    <img src="{{ "/img/menu-prototype.jpg" | prepend: site.baseurl }}" class="img-responsive img-centered image-max" alt="" />
	</div>
</div>

Menu Prototype