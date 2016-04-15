---
title: Requirements
layout: blog_post
date: 2016-04-13
img: requirements.jpg
thumbnail: requirements-thumbnail.jpg
alt: image-alt
category: update
description: Product Requirements
crumbs: 
- text: Home
  url: /
- text: Blog
  url: /blog
---

## What we accomplished 


#### tl;dr 

This week we wrote out our [Product Requirements Document], which specifies our final deliverable, critical features, budget, and risk assessment, as well as a detailed week by week overview of our schedule and milestones. In addition, we filmed a promo video for our product. Check it out on our front page! 

#### Detailed overview: 

Nick explored existing user interaction techniques on the HoloLens by using existing applications such as HoloStudio, which lets users build their own holograms. Jenny wrote the script for our Kickstarter video and Riley, Nick, and Jenny worked on filming the video. Riley edited together the video clips with voiceover and sound. We all contributed to the product requirements document to agree on a game plan. Sonja built a simple application that lets users build a straight track by gazing at and selecting the end of the current track being built (this can be seen in our video), and explored possible representations for the tracks in our game - read more about this below!

## Plan for Next Week
For this coming week, we will purchase a couple of the assets listed in the requirements document and start playing around with their capabilities, especially on the scripting side and procedural generation of meshes. We will each prototype separately so that we can rapidly iterate and in the following week bring together the best parts of our designs.

## Notes
A lot of investigation went into how best to represent a track that easily supports building a track from the user side, storing it efficiently in the backend, and animating an object along the path. A common representation of curves in computer graphics is a spline, which is many bezier curves stitched together. We want both c0 and c1 continuity to ensure that the track segments are both connected and smoothly transition into each other. We could implement a curve system ourselves, and that is what we did for an early prototype. Here are a couple of screenshots of a simple system that procedurally extrudes a rectangle along 3 separate bezier curves with very poor texture mapping:

<div class="row">
	<div class="col-lg-6 col-md-6 col-sm-6">
	    <img src="{{ "/img/spline1.png" | prepend: site.baseurl }}" class="img-responsive img-centered image-max" alt="" />
	</div>

	<div class="col-lg-6 col-md-6 col-sm-6">
	    <img src="{{ "/img/spline2.png" | prepend: site.baseurl }}" class="img-responsive img-centered image-max" alt="" />
	</div>
</div>

After implementing this, we realized a few things. First, the geometry along a spline can easily intersect itself if a curve is too tight, so we will definitely want to restrict the placement of control points or provide pre-made pieces of track for users to connect together. Second, extruding a simple profile curve along a spline is not difficult, but if we want to take an existing mesh such as a short segment of track we buy from the asset store, deforming and repeating that mesh along a spline is a much more difficult and involved task. Third, in order to have high density meshes and fancy animations run in real time, we would probably have to optimize the code, and that adds complexity. We could spend time implementing this, and it would undoubtedly be a fun process, but it is not the focus of our project and using a tried and tested resource will allow us to build more features and polish the application. We initially found Curvy, which has all of the spline constructs we were looking for and comes with full source code that we could extend. However, after looking through some forums, we realized that it does not fully support Windows Universal, which is a necessity for HoloLens development. 

<div class="row">
	<div class="col-lg-12 col-md-12 col-sm-12">
	    <img src="{{ "/img/forum.png" | prepend: site.baseurl }}" class="img-responsive img-centered image-max" alt="" />
	</div>
</div>

We found another alternative, though twice the price, that has the same functionality. Looking at the forums indicates the developers are still responsive, and there is a decent community of users. The plugin is MegaShapes, which is a subset of MegaFiers that supports more complex mesh deformation and animation than what we need for our project. At $100 a seat, it will take up 40% of our budget, but without a good mesh deform and spline system, it will be difficult to incorporate other assets into our program so buying cool meshes would mean nothing if we could only render simple shapes along a spline. We will first purchase one license to ensure that the plugin does in fact align with our needs, and will reevaluate whether we want to purchase a license for each developer after experimentation.


[Product Requirements Document]: {{ "/documents/requirements_document.pdf" | prepend: site.baseurl }}