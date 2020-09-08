+++
categories = ["Programming", "GSoC"]
date = "2020-09-08"
description = "How I got into Google summer of code and what I did"
featuredalt = ""
featuredpath = "date"
linktitle = "My GSoC journey"
title = "My GSoC journey"
slug = "My GSoC journey"
type = "post"
+++

Hi,

With the Google summer of code final results out today (8 September 2020). I thought I should make a blog post my experience in Google Summer of Code project, how I got into the program, what I did and how it went. It was an amazing experience and I highly recommend any student to participate in the program.

## What is GSoC ?
From the GSoC website:
> Google Summer of Code is a global program focused on bringing more student developers into open source software development. Students work with an open source organization on a 3 month programming project during their break from school.

Which is great description about what GSoC is.

Google summer of code is an initiative that Google has been doing every year since 2005. Over the years, more than 15000 students from 109 countries around the world contributed more than 36 million lines of code to many open source projects in vast variety of fields.

Each project is unique to each student and once an organization accepts you into a project, you will be paired with mentors who will guide you and help you finish your project. Mentors are also usually very knowledgable about the subject you're working on (for me, my mentor was the one who created a big part of the codebase I was working with). 

## The application process
Each year, Google announces the start of the application period after open source organization apply and get accepted into the project.
To apply, you're required to make a proposal document about what you're going to implement and what makes a great fit to do the project.
Each organization has its own set of guildlines on what the proposal should contain. Most students need to contact mentors during or before the application period to discuss the scope of the project. 
Personally, I contacted my mentor and made a video call where we discussed the scope of the project and the features they wanted to implement. 
Most organizations also require doing a competency test and making contributions to the project before or during the application period increases your chances of being accespted especially in competetive projects (Some projects have more applicants than others).

## How I got into GSoC ?
During the firt half of 2019, I started learning 3D graphics programming using OpenGL from Youtube and various websites (because the Cherno opengl playlist was very cool :) ). I tried implementing a graphics engine. It was a painful journey with satisfying results (Everyone thought I was crazy for doing that lol). Some day later that year I had a discussion with some friends and my game engine and Google Summer of Code, Since then I decided to apply in GSoC 2020 since one of my friends told me I will definetely get accepted (I wasn't confident at the time but it eventually happened lol). I looked into what are 2019's project after GSoC 2019 ended and decided to apply for the QGIS 3D project with the OSGEO organization since I met all the requirements of that projects. I contacted the mentors, created my proposal and then shared with the community for suggessions and reviews.

## What I worked on ?
The open source project I was working on is [QGIS](https://github.com/qgis), which is a popular free open source geographical information system for making maps and visualizing them. 
The project has 3D viewer to see the terrain in 3D and other things like buildings.
My main work was to add new features that didn't exist before in the 3D viewer:
- Displaying textures on buildings.
- Exporting the 3D scene in .obj format.
- Add new rendering techniques: Directional lights, skybox rendering and shadow rendering.
- Enabling python scrippting for the 3D view (I didn't implement this one :( ).

I didn't get to implement all the features I listed in my initial proposal but what I implemented was loved by the community and I received multiple tweets about it.

## Feature 1: Displaying textures on buildings
Previously, QGIS users could only use Phong shading as materials for building geometries generated from vector layers. My goal was to make it possible to add texture images to the geometry of buildings. I started by reading the internal QGIS code and experimenting with Qt3D internals. First I experimented with just adding a texture on all triangles and got it working.

![image](https://res.cloudinary.com/daqxkqlym/image/upload/v1599574659/single_textured_materials_di73d1.png)

Then I moved on to the separation between walls and roofs, I solved this problem by calculating the angle between each triangle’s normal and the horizontal plane, if that angle is small enough we consider that the triangle belongs to a wall, otherwise it’s a roof.

![image](https://res.cloudinary.com/daqxkqlym/image/upload/v1599574532/roof_and_walls_material_hozanj.png)

For how the user interacts with the texturing and provides different texture for walls and roofs, I used the rule based rendering and added a combo box from which the user can specify which facade to render (meaning to render a wall or a roof). I also added some parameters to adjust the scale of the texture and its rotation.

After I finished the implementation, My mentor's company Lutra Consulting tweeted about my work ([Link to the tweet](https://twitter.com/lutraconsulting/status/1267456378031013891)).

## Feature 2: Directional lights
QGIS 3D viewer already supports points lights which are quite simular to point light. It wasn't too much work to just duplicate the point lights implementation and it got merged quickly into the master branch after some discussion.

## Feature 3: 3D Scene export
For the blender export I made a custom .obj files exporter. My implementation was quite tedious to implement because there were different types of layers and entities and I had to handle materials as well. I relied on extracting geometry information from the vertex buffer and the index buffer, making that work with DEM terrain, flat terrains and vector layers. For the materials I used the .mtl specification to export colors and textures. I added some UI to handle the resolution of texture and terrain.
Here are some screenshots of what I was able to export:
- Digital elevation model (DEM) terrain exported with .OBJ smoothing option on and off in Blender:
![image](https://res.cloudinary.com/daqxkqlym/image/upload/v1599574586/scene_export_smoothing_baoe8u.png)

- Vector layer export:
![image](https://res.cloudinary.com/daqxkqlym/image/upload/v1599574584/scene_export_3_x9b4vj.png)

- Exporting terrain texture (The texture image reflects the 2D image of the map and the user can specify the exporting resolution):
    - Blender:
![image](https://res.cloudinary.com/daqxkqlym/image/upload/v1599574658/terrain_blender_mkptbt.png)

    - MeshLab:
![image](https://res.cloudinary.com/daqxkqlym/image/upload/v1599574682/terrain_meshLab_azwis2.png)

- Rule based rendering and material export in MeshLab:
![image](https://res.cloudinary.com/daqxkqlym/image/upload/v1599574658/vector_meshlab_xuo1ur.png)
![image](https://res.cloudinary.com/daqxkqlym/image/upload/v1599574489/rule_based_renderer_ffsdqk.png)

After the feature got merged into master, One of the core contriutors Nyall Dawson tweeted about my work and the community loved it ([Link to tweet](https://twitter.com/nyalldawson/status/1288986741462900738))

## Feature 4: Skybox rendering
First I experimented with Qt3D skybox but found some difficulties using that (there was a bug that was fixed in later releases) and it wasn’t also extensible (for different types of skyboxes), then I copied the implementation of the skybox entity from Qt3D and made it work inside QGIS 3D viewer. 
I introduced 2 ways of providing skyboxes :
- 6 distinct faces for the skybox cube map: The user gives each face of the skybox individually.
- 1 panoramic image: The user simply gives one panoramic image (like an .HDR image form https://hdrihaven.com/) and the renderer takes care of making the skybox.

Here are some gifs of what I was able to make:
![image](https://res.cloudinary.com/daqxkqlym/image/upload/v1599574676/skybox1_zv3oom.gif)
![image](https://res.cloudinary.com/daqxkqlym/image/upload/v1599574647/skybox2_nlubeg.gif)

After this feature was merged, I got a tweet about it from Tim Sutton, one of the most well know persons in the QGIS community ([link to tweet](https://twitter.com/timlinux/status/1296064098170544128)).

## Feature 5: Shadow rendering
This was the hardest feature to implement and I couldn’t even finish it in time without some visual artifacts.

Previously, I learned about some shadow rendering techniques and shadow mapping was the technique I’m most familiar with, so after discussing with mentors I decided to try and implement it.

To implement it, I first started with experimenting with Qt3D frame graphs and converted an example implementation of shadow mapping provided by Qt3D but made with QML. I made that implementation into a [Github repository](https://github.com/NEDJIMAbelgacem/Qt3D-shadow-maps-CPP)

Then I started to port that implementation to QGIS by making some post processing effects. 

After that I tried implementing the shadow rendering but using a static light camera which resulted in a bad shadow resolution. To try and fix that I calculated the view frustum in world coordinates and used that to compute a bounding box that is used to move the light camera to where the user is looking at. The results were not perfect but better than having the shadow in a very low resolution. Also in the shader, I made the shadows look soft using a technique called percentage closer filtering.

Here are some screenshots of the implementation at the time of writing the final report: 

![image](https://res.cloudinary.com/daqxkqlym/image/upload/v1599574523/shadows1_fy1pej.png)
![image](https://res.cloudinary.com/daqxkqlym/image/upload/v1599574518/shadows2_cmblau.png)
![image](https://res.cloudinary.com/daqxkqlym/image/upload/v1599574495/good_shadows1_agfus7.jpg)
![image](https://res.cloudinary.com/daqxkqlym/image/upload/v1599574493/good_shadows2_pcvxk8.jpg)

It does look good in these 2 images, but there were some other issues not visible here like self shadowing and peter panning that were not visible here. These issues prevented me from completing the work in the coding period. Later I added parameters to reduce artifactes.

## Conclusion
The Google summer of code was a great learning opportunity for me and a great way to get into open source. I am grateful for my mentors Martin Dobias and Peter Patrik for their help and guildance throughout the project. The community of QGIS is awesone and I would love to make more contributions in the future.

I hugely recommend any student to apply to the GSoC program and I will most probably try to get into GSoC again next year.