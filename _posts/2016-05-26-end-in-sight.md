---
title: End in Sight
layout: blog_post
date: 2016-05-26
img: end.png
thumbnail: end-thumbnail.png
alt: image-alt
category: update
description: The final stretch
crumbs: 
- text: Home
  url: /
- text: Blog
  url: /blog
---

## [Last Week's Blog](/update/2016/05/19/leaving-the-station/)

## What we accomplished 

This week Riley improved the animation to have variable speed as the vehicle traverses the track based on the slope. We enforced a minimum speed so the train doesn’t get stuck. Jenny made progress with sharing, fixing the issue where the orientations of the two tracks were mismatched and realizing that the flickering and lag issue was a result of a bad build after deploying from a  different computer. Nick created 3D meshes for the main menu, which is an improvement over the mismatched 2D icons we previously had. He also fixed a bug with the menu placement and started working on being able to build on either side of the track, which will be useful for the sharing experience. Sonja improved the user experience for delete, made new icons for the track adjustment buttons, fixed the occlusion issue for the track segment menu that was brought up during last week’s check-in, and discovered a bug with normals when exploring a different method for adjusting height.


## Plan for Next Week

Riley will create prefabs for the coaster track so we can replace the boxes with meshes, and add spatial sound to the animation. Jenny and Nick will test the sharing setup in the atrium with the server running in the lab, and also see if it’s difficult to establish a shared anchor point in a larger space. They will also incorporate the sharing setup into the master branch. Jenny will also look at the save/load interaction for incorporating a preset atrium-sized track into the demo experience, and work on creating a toggle to turn sharing on/off. Sonja will fix the normal issue and revise the experience of adjusting height.

<br />

## Notes

### Sonja

This week I mostly worked on improving the track adjustment UI and UX, and updating the behavior of track height. 

The first improvement I made to track adjustments was to update the way track segments get deleted. Previously, clicking on the delete button would immediately remove the selected track segment and all following segments. Now, clicking on the delete button will select it, and all track segments to be deleted will turn red. The delete action will take place only after clicking on the selected delete button again. This addition is meant to give a user more feedback on what the button does, and also to prevent them from accidentally deleting something they didn’t intend to. 

The second improvement was fixing the occlusion issue of the track segment menu. Because it’s placed just above the center point of a track segment, segments with height change can cut through the menu and occlude it. Not only does it look weird, but it adds a barrier for the user to select the buttons. The menu was also getting occluded by other non-selected track segments. While this is the correct behavior based on the depth of the objects in space relative to the user, it doesn’t make for a very good experience if the buttons are hidden. I updated the renderer and collision detection to move the track segment menu in front of the track to fix this issue. 

The third improvement was creating icons for the track segment adjustments. I had previously used filler textures from the open source GalaxyExplorer project, but those textures did not match up with our actions. At first I tried to find free icons online but there wasn’t a consistent set of icons that conveyed what I wanted, so I jumped into the world of vector graphics and attempted to make my own icons. After discovering that Photoshop is terrible for vector graphics (I don’t have Illustrator), I used another program, Pixelmator (which is amazing and so easy to use, highly recommend, and it’s cheap too!). You can see the result below:

<div class="row">
	<div class="col-lg-3 col-md-3 col-sm-3">
	    <img src="{{ "/img/delete-icon.png" | prepend: site.baseurl }}" class="img-responsive img-centered image-max" alt="" />
	</div>
	<div class="col-lg-3 col-md-3 col-sm-3">
	    <img src="{{ "/img/twist-icon.png" | prepend: site.baseurl }}" class="img-responsive img-centered image-max" alt="" />
	</div>
	<div class="col-lg-3 col-md-3 col-sm-3">
	    <img src="{{ "/img/height-icon.png" | prepend: site.baseurl }}" class="img-responsive img-centered image-max" alt="" />
	</div>
	<div class="col-lg-3 col-md-3 col-sm-3">
	    <img src="{{ "/img/curve-icon.png" | prepend: site.baseurl }}" class="img-responsive img-centered image-max" alt="" />
	</div>
</div>

Finally, I worked on height. The previous behavior was that the “height” of a track was always relative to the positive Y axis, regardless of the orientation of the track segment. I wanted to change the height to be relative to the normal of the track, so that instead of the track getting higher and higher each time a user pressed the “up” button, it would wrap around and create a loop-de-loop. In doing this, I discovered a bug with the way the normal is calculated. In order to calculate the normal, we calculate the binormal from the tangent and the Up vector. However, when the tangent is the Up vector, this becomes problematic and results in the normal flipping 180 degrees. I chatted with Nick about the best way to solve this, and he came up with the idea of keeping a dictionary of reference vectors to use for portions of the track. This method works brilliantly, and doesn’t seem to have a significant performance overhead. The asset we use, MegaShapes, also has the same bug for their lofts, so I will need to go in and update their normal calculation to match our desired behavior. In addition, I will work on creating a better formula for what height adjustment should look like (similar to redesigning the curvature last week).


### Nick

I worked on two different major features this week, while polishing off a couple of bugs and helping a bit with sharing. The first project was to fix the menu icons. Instead of updating them to be actual representative icons, we got the idea (thanks Aditya!) to replace them with models of the actual track piece. This would help better show the 3D structure. Sonja helped lay the framework for the mesh generation, and then I connected the pieces, and added them to the menu. It worked, and was definitely helpful, but we had more ideas. On top of showing the model, we wanted to rotate it appropriately so that it matched the end of the track, and would show exactly what the added piece would look like. The meshes, however, were built starting from the origin. To put them in the right place, I had to offset them. This meant that rotating, though, would not rotate the shape around the perceived center, but would look strange. Rather than trying to dynamically recenter the mesh, or change the mesh generation code (which we need the way it is for the other track mesh pieces), I instead used a trick to recenter with a parent object, by offsetting it (dynamically finding the correct offset from the mesh), and then rotating the mesh normally. This worked nicely, but we realized it would get out of sync as the menu moved, because the orientation wasn’t updating. Performing this regular update, though, looked a bit strange, until I tried using slerp instead of lerp, which fixed the issue.

Slerp also awesomely helped me fix a menu positioning bug, because lerp could take the menu through the user if you moved fast. Slerp, though, would move it along the desired path around the user.

The next major thing I added isn’t a visible feature yet. Once we got sharing working, we realized we ran into a timing issue. The sharing system handles message sending and ordering correctly, but doesn’t seem to allow you to send messages to everything (including yourself). As such, all other devices would get the message, but the sender would need to apply it from the sending thread. As such, if two users added a track at the same time, they would add their track first, before getting the network message. Not having an elegant solution, we came up with an alternate paradigm, that we decided worked even better. Instead of allowing all users to build on the same track, we will limit sharing to two people. These two will build from the same origin, but in opposite directions. They’ll see the other person’s track, but not be able to modify or interact with it. This also means they have full control over their track, and don’t have to coordinate or take turns building. Instead, both can be building at the same time with this model. It also solves the timing issue, because they’ll be adding to different tracks. To support this, I set the system up to use two splines, one of which is considered to be owned by the remote users. This required a fair amount of refactoring. On top of this, the loft generation and animation needed a full spline in order to perform their functions correctly, so I needed to merge the two splines when switching to play mode. I got this working, though, and now we’ll be able to integrate this with the sharing system.


