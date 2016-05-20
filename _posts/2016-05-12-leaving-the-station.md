---
title: Leaving the Station
layout: blog_post
date: 2016-05-19
img: station.png
thumbnail: station-thumbnail.png
alt: image-alt
category: update
description: Exploring larger spaces
crumbs: 
- text: Home
  url: /
- text: Blog
  url: /blog
---

## [Demo Plan](/documents/demo_plan.pdf)

## What we accomplished 

This week we continued adding some new features, but mostly worked on refining the current experience and making things more robust. This included adding more individual track adjustments, adding more states to the cursor, rethinking the definition of a curve, fixing the maximum vertex limit issue, simplifying animation to a box to make sure it follows the orientation of the track, and hooking up the animation to the menu controls. We also took the app into a larger space for testing - read more about this below. After a discussion with Aditya, he suggested displaying the preset track types in the menu as meshes instead of icons, which would give the user a better idea of how the selected piece would look.


## Plan for Next Week

Next week Nick will work on revamping the menu to show meshes, Riley will get animation working for multi-carriage vehicles, Jenny will work on getting more consistent anchor points between two shared hololenses, and Sonja will continue refining the track adjustments and making the user experience better.

<br />

## Notes

### Atrium Testing

Earlier in the week (Sunday), we made our first foray outside of the labs, in order to test in the enlarged space of the Atrium. One of the main things we wanted to test was the ability of the HoloLens to create/render/work with extremely large coasters, of the kind that we want to allow in our demo. Last week, though, we had run into an issue with Unity’s vertex limit (see below for more detail). Having resolved that, and given the likely emptiness of the Atrium (better for an initial test, to allow us free movement), we headed over. The three of us present for this test each wore a device, and set out to independently build coasters testing various aspects of the system. Some of the things we focused on included testing the height range (we built from the floor up through the roof), testing the width range (we built coasters stretching back and forth across a significant portion of the Atrium), testing the twist and elevation controls (building the most convoluted track possible), and testing building multi-level coasters (building part on the ground floor, and moving to the second story to build a large section there). Out of these tests, as well as general playing around (we swapped HoloLenses regularly, in order to build off of each other’s creations, and try to break things), we collected a bunch of observations (as well as photos and video). A selection of these is documented in the following section.

#### Observations

- Lighter colored tracks allow you to see detail better, and tend to show up more visually powerful
- The menu behaves oddly (with our new stay slightly on the screen behavior) when you turn sharply ~180°
- There is a (surprisingly light, but noticeable) lag when the user switches into play mode, after building a large track. - We would like to either hide this, or visually indicate that the device is working on building their model.
- Cursor does not correctly let go of state when a finger goes off the screen and interactions are occurring
- We may want to play around with indicating the sidedness of individual track modifications (as they modify the forward joint, but this isn’t always visually easy to tell without making a modification)
- We want some way to shift the origin (moving the entire track if any is built)
- We need to play around with the interaction of track segment modifications:
- Currently it allows you to select an option, and then switch to a different track segment, and be modifying the same option.
- This is good for power users, but strange if you deselect the segment, and then select another 5 minutes later. 
- Potentially addressable by removing the menu selection when you deselect the current track segment by reselecting it
- The “reset” icon for the animation does not display on the hololens, for some reason
- “Kinky” edges can occur when modifying neighboring track segments, as the tangent does not adjust (known issue)
- We need to fix normals of a few meshes
- We need to get the icons consistent




### Unity Vertex Limit (Nick):

Because of their use of 16-bit indexes for vertices, they limit any individual mesh to 65,534 vertices (a few reserved indexes by Unity). The MegaShapes lofting system, however, tiles the basic mesh, deforming it along the spline, and composites these pieces into a single mesh behind the scenes. While playing around in the lab, we ran into this limit fairly quickly, then. The first approach was to try splitting into multiple loft layers, for a single loft, which still resulted in a single mesh. As that ran into the same limit, we then needed to figure out a way to segment the spline, such that each segment had a mesh below the limit, and such that the segments were the exact perfect length to tile the mesh perfectly (the MegaShape system only tiles the complete mesh, and will leave a gap if less than the length of the mesh). Luckily, the tiling occurs in a constant scale fashion, and distances are maintained. Because of this, we were able to set it up to tile as many segments as needed (up to a size just under the limit) in order to allow rendering of longer tracks. While getting the initial bit working was relatively straightforward, due to some nice scaling coincidences, we needed a more robust calculation in order to allow good scaling, as well as the ability to negatively offset track pieces (for the wooden train piece, for example). This took a little more work, requiring digging into the MegaShapes code to understand their calculation. Ultimately, we managed to get this working, to allow perfect tiling of our meshes on segment edges, even with offsets.


### Track Adjustment & Curves (Sonja)

Now that we let a user adjust curve properties after they’ve built, there are issues that arise that were not present for preset tiles. At first there were no limits for the user in how much they could twist, elevate, or curve the track, which resulted in very sharp edges and extremely long track segments. This is undesirable because it results in very ugly meshes, so I limit the twist, elevation, and curve of each track segment relative to its neighbors. This prevents the track from getting into a weird looking state, but might be too restrictive. The restriction can be relaxed by increasing the limits, but we will have to play test in order to see what a good range is. The other issue was collision detection. In the preset tiles, we prevent the user from adding a track if there will be a collision, but with track adjustments, the track is already present. I pondered running collision detection for each update and turn the track red if a collision state was detected, but this seemed expensive and unnecessary. I decided to only check for a collision after the user was done adjusting. If it detects a collision, the track segment will turn red for one second and will revert to its previous position before the adjustment started. I also made collision detection more fine-grained because we were able to build tracks in colliding configurations during our atrium test. We received feedback regarding the video on our last blog post, which shows me testing out the interaction for twisting a track piece, that the twist action seemed rather unresponsive or hard to activate. To address this, I implemented multiple cursor states to reflect what the HoloLens sees about a user’s hand. When there is no hand detected, it is a cursor with arrows to show which direction the navigation occurs. When a hand is detected, this cursor is augmented with an additional ring (much like the default cursor). Finally, when a user is actively navigating, the entire cursor turns a different color. There isn’t much I can do about the HoloLens not detecting the gestures sometimes, but providing as much visual feedback (and eventually audio feedback) will help the user understand why an action is or isn’t taking place.

The other thing I worked on this week was to refine the way we represent curves. Up until now, I had defined bezier control points without thinking too much about what the resulting curve looked like as long as it was at the right angle and height. However, stitching four 90 degree curves doesn’t look like a circle, and circles are desirable because they look prettier than a closed loop that’s not quite a circle. I redesigned the curvature code to always be the arc of some circle (larger radius for smaller angles) [1] [2], so now a user can create a circular track of arbitrary size. I am still working on incorporating this into how I calculate height. 

[1] https://people.mozilla.org/~jmuizelaar/Riskus354.pdf 

[2] http://www.itc.ktu.lt/index.php/ITC/article/viewFile/1707/3105


