---
title: Piecing It Together
layout: blog_post
date: 2016-05-12
img: piecing.png
thumbnail: piecing-thumbnail.png
alt: image-alt
category: update
description: Merging our features
crumbs: 
- text: Home
  url: /
- text: Blog
  url: /blog
---

## What we accomplished 

This week we made very good progress on improving the look and feel of our application, as well as adding more functionality and bringing components together. Jenny incorporated her saving/loading work into the main menu, and Sonja and Jenny worked on fixing bugs in the save/load experience. Riley brought in animation to the play mode of the application, so when a user switches from build mode to play mode, a toy train will start animating along the path. Nick worked on refining the menu motion so that when it goes out of your view window, it will follow your gaze and always be just out of the frame. He also added additional track lofts so now we have more options than just the toy train look in play mode. Nick also added some animation UI options to the menu, but they are not hooked up to the animation yet. Sonja updated the cursor to give the user better feedback on their actions via visual and audio cues. She also added a couple individual track controls: delete and twist. Delete allows the user to delete the selected track segment and all track segments following. Twist allows the user to modify the orientation of a track piece, and is useful for creating banked turns (and other fun spirals) on the track.

We also discovered a problem while testing in the lab this week, which is that there is a vertex limit on meshes in Unity, and we hit it when building a medium length track in play mode.


## Plan for Next Week

Riley will work on fixing the bugs related to animation and improve functionality and integrate it further with the app experience. Currently the train animation is positionally correct, but the orientation does not follow the orientation of the path. In addition, the animating model is always a toy train even if the user switches to a roller coaster, so she will swap out the animating object to match the track being rendered. Finally, Riley will hook up the animation with the buttons in the menu that Nick created so the user can play/pause/restart the animation without having to switch back to build mode first. If there’s time, spatial sound will be incorporated.

Jenny will continue to investigate sharing. The goal for next week is to understand how to send custom messages between devices, and scope out the complexity of adding support for multi-player building to see if this will be feasible to implement for the demo. This will include exploring options for maintaining consensus between devices.

Nick will work on investigating a workaround to the vertex limit. This will involve a lot of exploration to get the seams to be invisible when breaking up our mesh.

Sonja will continue working on individual track adjustments, including adding height and curvature adjustment options as well as setting constraints on these adjustments and making sure to incorporate collision detection.

We got permission to test our app in the atrium, so we will be taking a couple Hololenses to this larger space to explore what our app experience should be, and figure out if there are any hidden bugs that we haven’t caught in the lab.


## Notes

### Nick

Over the last week, I worked on a couple of different features. First, we wanted to play with the menu motion. Leaving it at a fixed offset from the user had worked okay, but meant that it would always be located in the same direction. This could result in having to turn around to build. We brainstormed a couple of different behaviors, and decided to try a few. The one I implemented this week keeps the menu just at the edge of vision, when out of sight. When inside the camera frustum (in view of the player), I keep it in a fixed position, relative to the user. As they move, so does it. However, when it moves outside the frustum, it freezes just outside of view, and moves with the player. This makes it so, as soon as the player looks back towards the direction of the menu, it begins moving into view again. Although not perfect, this behavior seems closer to intuitive, and allows you to position the menu, if somewhat naively. We talked with the Professors about potentially having a way to recenter it in the view of the user, that could potentially complement this approach. Still an ongoing investigation, though.

Next, I added in an additional track type. To do this, I purchased a new asset, that contained some steel coaster assets. Using that, I was able to make a new MegaShapes Loft, and simply create a manager to handle switching these lofts out upon user changes. I hooked this up to the UI, and added in a few copies of the new coaster loft with some color changes, and got that working fairly well. While testing, though, we found that we bump into the vertex limit per MegaShape loft pretty quickly. This happens for both the new track type, as well as the old one. For an atrium-size demo, though, as well as general use, we’ll want to allow building long tracks. As such, my next project is to break the shape into multiple lofts. It shouldn’t be too hard, but the edges will be challenging to align appropriately, and take some time to perfect.

I also added in some controls to allow playing/pausing/resetting the animation!

<div class="row">
	<div class="col-lg-12 col-md-12 col-sm-12">
	    <img src="{{ "/img/coaster-big.png" | prepend: site.baseurl }}" class="img-responsive img-centered image-max" alt="" />
	</div>
</div>


### Sonja

My goal for the week was to allow a user to twist the track during build mode, and in the process I ended up redesigning our cursor and how it interacts with objects within our app. I did this because in order for the user to manipulate individual track segments, there needs to be clear visual cues to give feedback to the user, and one of the best ways is to use the cursor. Our old code had the cursor checking the type of the GameObject being gazed at and deciding what to do based on the type. I moved this logic to the GameObjects themselves, and any object that is selectable must inherit from a selection class. The old cursor was taken from the Origami project in the Holograms 101 course, which was a low-poly ring mesh that was not an overlay layer so it was hidden behind our menu, which has a higher render order to prevent it from getting occluded by the spatial mapping mesh. We also only showed the cursor when it intersected with an object. I changed the cursor to be a quad instead of a mesh so that we can swap out the icons depending on what the user is doing. I tried to mimic the behavior of the default cursor, which changes shape when a hand is present, and gives feedback when an airtap happens. I even added a short sound for the airtap, although we will probably explore better sound assets later on. Another change with the cursor is that it is now always showing, even if the user is not looking at a selectable object. Finally, the cursor is rendered in the overlay layer, so it is always visible.

I thought a lot about how to best allow the user to interact with a track segment and manipulate it in a way that was easy and intuitive. I initially thought of implementing a slider that would indicate how the user was modifying the track. However, this would have taken a lot of screen space and would also be redundant because they would have visual feedback anyway. I turned to some existing apps to get inspiration on UI. I looked at the Holograms app, which lets you place holograms and resize/move it. I also looked at Galaxy Explorer, the open source app that Microsoft built for the //build conference. Both apps changed the cursor to an icon when in “adjust” mode, which I really liked. I created a small menu that pops up when the user selects a track, and then the user can either click one of the buttons to delete the current track segment until the end, or click the other button to adjust the twist of the track. This required using navigation events, which was new, but it worked out pretty smoothly.

<iframe width="560" height="315" src="https://www.youtube.com/embed/XSUE2JFjIZs" frameborder="0" allowfullscreen></iframe>
