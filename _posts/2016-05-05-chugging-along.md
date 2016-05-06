---
title: Chugging Along
layout: blog_post
date: 2016-05-05
img: chugging.png
thumbnail: chugging-thumbnail.png
alt: image-alt
category: update
description: Individual progress 
crumbs: 
- text: Home
  url: /
- text: Blog
  url: /blog
---

## What we accomplished 

At the end of last week, we implemented switching between two modes: build and play. Jenny added support for saving and loading the track configuration to and from XML and JSON. Nick made it so the menu changes in play mode to show a bunch of texture options so we can later fill this in with different options for rendering the track. He also added save and load buttons to the menu, to be hooked up with Jenny's work on saving and loading a track building session. Nick improved the menu by writing some custom shaders to make the menu render in front of the room mesh so it doesn't get occluded. Sonja implemented collision detection, added new track types to the menu with varying heights, added backend support for twist. Riley worked on animation.


## Plan for Next Week
Next week is our progress report presentation to the class, so we will focus on piecing together each of our components into a cohesive experience. This involves connecting the save/load functionality with the menu interface and incorporating animation into the app. We would also like to work on enhancing the existing experience, and plan to work on updating our cursor to the one in HoloToolkit, and also support fine-grained adjustments for each track piece during build mode.


## Notes

### Nick

This week I focused on a couple of different challenges. The first of these was to begin using the spatial mapping functionality present in the HoloLens. We have lots of stretch ideas involving the room geometry, such as automatically tunneling through objects (with appropriate texturing), allowing portals through walls, and more, but wanted to start simple, with occlusion. Given the functionality of the holotoolkit, adding this in wasn’t particularly difficult. However, playing around with the result showed some of the downsides of the current state of the technology. Pieces would clip in and out of view, as the mesh adjusted based on the latest readings from the headset. The edges of tables wouldn’t be detected well, and the tracks would only start being occluded a few inches in. Whiteboards were irregularly detected, with constantly shifting mesh approximations. Playing around with the tuning options, though, we managed to get it to perform acceptably (for now).

The next task I focused on, was adding functionality to the menu. Our current preset tile list worked really well for build mode, but wasn’t needed for play mode. Instead, we wanted to have a list of textures for the user to choose from. So, I added this in, and tidied the existing button behavior. This also laid the groundwork for having a third state, for loading in different existing tracks, where the tiles can show previews of existing saves. Along the way, though, testing the new menu tiles revealed a problem. The room mesh occlusion not only hid the track pieces, but also the menu. This would result in the menu getting hidden behind walls, or being rendered in pieces, due to intersecting with tables, whiteboards, or the printer. The ideal behavior would be to have it always render on top of everything, so I set out to find a way to enforce that. After lots of searching, and trying various options, the signs seemed to point to needing to modify the shader they were using. So I wrote two new shaders, which turned off the zbuffer, and forced overlay behavior. One replicated the simple solid color functionality of our menu elements, and the other allows sprite rendering for the icons.

Having fixed this, I moved on to adding a save and load area to the menu, so that Jenny could connect her functionality to the UI. Next, I plan to work on a couple of things:

1. Updating the cursor (we currently use the one from the Origami tutorial, and would prefer to use the holotoolkit one). It may need some adjustments to keep it rendering in front of the menu.
2. Improving the menu interaction paradigm. We are considering a variety of other approaches, including tagalongs, modified tagalongs, pinning, and more, for determining the menu placement.
3. Working with Sonja on allowing fine-tuned custom adding. We are considering ghosting a copy of the latest track piece onto the end of the track, and allowing the user to select and adjust this piece. They will then be able to confirm these adjustments, and add it to the track. At the same time, they can still use the presets to add in pieces, which will take the place of the current ghosted track.


### Sonja

I spent the majority of this week getting collision detection to work. It was a very iterative process to figure out how to get collisions to behave the way we wanted. The very first thing I tried was just attaching a mesh collider to each track segment. However, in Unity, mesh colliders cannot collide with each other unless they are convex. In addition, convex colliders are limited to meshes with <= 255 triangles. Our track segments were just under that cutoff, but the convex colliders were subpar on accuracy, especially for track pieces with high curve or elevation gain. The other problem with using mesh collider on mesh collider collision detection was that it would detect a collision for every track piece that was added because the ends of the track pieces touched. Checking the collision for where it occurred and trying to determine if it was an acceptable collision didn’t seem very fun, so I explored other options. 

The second thing I tried was using a box collider, which has worse accuracy than a convex mesh collider. The reasoning behind this was that it would be easier to scale the bounding box of the collider to not go out to the very edge of the mesh and therefore not interfere at the endpoints of the track segments. This worked surprisingly well, but was too restrictive on where we let users lay tracks. We also wanted to check that nothing collided with the area on top of a track where our vehicles would be travelling, and extending the box collider that far would have meant another hit on creative freedom. After thinking on this problem for a few days, I came up with another idea.

Unity’s colliders weren’t working for the collision detection I was trying to implement, so I looked beyond colliders and thought about how else I could detect collisions. I thought, why not just cast a ray from the path in the direction of the normal and if it intersects something within a certain distance threshold, count it as a collision? At first this sounded like a pretty expensive operation, and I almost discarded the idea. I had no better alternative so I gave it a shot, and after experimenting with layer masks and visualizing the rays I was casting, I got it working and it does not appear to impact the performance of our application (hooray!). I added some user feedback when a collision is detected (the menu turns red for a half second) so the player isn’t confused as to why a new track segment wasn’t added to their path.

The other thing I worked on this week was to add additional track types to our menu of preset track segments: “up”, “down”, “right & up”, “right & down”, “left & up”, “left & down”. Adding these allowed us to get a better sense of building in 3D space, and how this wide range affected menu placement, spacial mapping, and collision detection. I also added backend support for adding twist to track segments, although there is no UI for incorporating it into the path yet.


<div class="row">
	<div class="col-lg-12 col-md-12 col-sm-12">
	    <img src="{{ "/img/collision-detection.png" | prepend: site.baseurl }}" class="img-responsive img-centered image-max" alt="" />
	</div>
</div>
Visualization of collision detection. The green boxes are the box colliders, and the red lines are the linecasts.


Next week I will work on:

1. Interacting with individual segments of the track path to make modifications, including deleting track segments and modifying the twist. As a stretch goal, I will add the ability to modify the height and angle of a track segment
2. I’ll work with Nick to explore an additional/alternate way of adding pieces to the end of a track.

 

### Jenny

This week I worked on allowing a user to save an entire track to a file in a human-readable format. A user can now start a build session, save information about the track to a file, then load the track from a file to continue building where they left off. 

While MegaShapes themselves are not serializable, MegaSplines are. So rather than saving the MegaShapes themselves, we save the underlying MegaSpline, which consists of a series of “knots” that represents each track piece that a user has added. Then, on load, we simply re-build the track given the information stored in the MegaSpline.

There are serialization libraries available for saving entire game scenes and re-loading them, many of which are free, but I found that most of these were too heavy duty for what we needed. Since we just need to save the spline and potentially some information about which skins to use to render the track pieces, I ended up not using these serialization libraries and just rolled my own script for saving and loading. I wrote a version for producing both XML and JSON files, but will likely stick with the JSON format as it’s much easier to read, and will be more useful for sharing over a network.

Now that the basic functionality is there, we’ll want to think more carefully about what would be the most natural way to save and load a track, and how this will fit into the building workflow. For example, it might be a hassle to continuously hit a ‘save’ button, so we might consider saving the user’s progress as they go, rather than asking the user to remember to save after each session. This would also be a good practice if a user’s device were to run out of battery during a build session, or if they were otherwise interrupted.

We’ll also need to think about how much control to give the user about where they store their track, and how many tracks a single user is allowed to save. For example, right now we only allow a user to save and load a single track, and we auto-save it to a location unknown to the user. In future, we might allow the user to save multiple tracks, and choose to load a track from a list of their previous tracks.

Next week I plan to work on: 

1. Hooking up the code for saving and loading with the hololens UI that Nick built, so that a player can save or load a track via a menu button. This includes figuring out where to store the file on the hololens device.
2. Flesh out a user’s build-save-load workflow. This might include allowing multiple track files per user for different build sessions, and auto-saving as a user builds.
3. Exploring sharing data between hololens devices. Ideally we’d like to have a collaborative building experience be an option. To start, we’ll want to allow one person to build while others watch. This will also be great to have for the end-of-quarter demo that we learned about today.
