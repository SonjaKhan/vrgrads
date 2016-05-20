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

This week we continued adding some new features, but mostly worked on refining the current experience and making things more robust. This included adding more individual track adjustments, adding more states to the cursor, rethinking the definition of a curve, fixing the maximum vertex limit issue, simplifying animation to a box to make sure it follows the orientation of the track, and hooking up the animation to the menu controls. We also took the app into a larger space for testing - read more about this below. After a discussion with Aditya, he suggested displaying the preset track types in the menu as meshes instead of icons, which would give the user a better idea of how the selected piece would look. Lastly, we added basic support for sharing holograms.


## Plan for Next Week

Next week Nick will work on revamping the menu to show meshes, Riley will get animation working for multi-carriage vehicles, Jenny will work on improving performance when sharing holograms between devices, and Sonja will continue refining the track adjustments and making the user experience better.

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

### Sharing (Jenny)

This week I added support for sharing the track between devices, so that two users can view and build on the same track in real-time. Currently there is support to share events that add and delete a track, as well as share events to adjust the height, twist, and curvature of a specific segment. The original goal for this week was to just be able to add a “spectator” to the scene, but doing so requires sending messages between devices about changes, and adding multi-player support was a natural extension of that.

Talking to Isaac, Panji, Aditya, and asking questions on the Microsoft Forum were definitely a great help, as there are many parts to adding sharing that aren’t documented very well, such as how to actually add CustomMessages to send between devices, or which scripts and unity settings need to be added to get sharing to actually work. Extending the Sharing skeleton code was not at all difficult — the most frustrating part was the development workflow using two devices. It takes an astonishingly long time to establish a shared spatial anchor between devices, and there were many cases where it was clear that the devices were communicating but the coordinates still didn’t seem to be aligned. Waiting an extra 2-5 minutes and/or re-starting the server/devices sometimes did the trick.

There were two bugs that kept the sharing feature from being truly “demo-worthy” this week. First, once the shared anchor point was established, I found that the devices were able to see the track start at the same location, but were building in different directions. This was related to the way we were adding our track pieces and the fact that even after establishing a common anchor point, the xyz axis of the device remained relative to the user’s starting position when the app was deployed. We keep a “TrackCollection” gameObject as the parent of the track pieces that get aded, but the track pieces were not inheriting the rotation of the parent TrackCollection when created. Explicitly setting this rotation for new track pieces fixed the issue, though we’ll need to look into whether it would be better to explicitly send rotations and positions relative to the shared anchor via the network messages instead. Second, there is a significant lag, often in the second device to join the sharing session, and the scene flickers and turns a rainbow of color. Furthermore, while a solo user in “play mode” might experience a slight lag as the track renders, the lag becomes quite significant when sharing.

For next week, I want to focus on improving the performance, since at this phase, it’s not at all acceptable for our final demo. We also need to do stress testing and see whether we need some kind of lightweight consensus protocol to make sure devices see the same interleaving of track events. Adding a simple timestamp might be enough. Once our major menu and track features are in the polishing stage, it’ll be great to get extra help in testing. Testing sharing with only one person is difficult, since one can only see the perspective of one device at a time. Lastly, I need to add support for “catching up” new users that join a session once others have started building, and for handling spatial anchors if the session leader crashes. All of these issues together will be more than enough to fill the next two weeks.
