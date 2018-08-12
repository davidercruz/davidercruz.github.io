---
layout: post
title:  "GSOC2018: Final Report"
date:   2018-11-13 01:17:16 +0100
categories: GSOC2018
---

It's the final day of GSOC 2018... It has been a great experience - I learned a lot about programming and
development and it also helped me understand some of the tools that I use everyday without thinking. 

##First evaluation

During the past 3 months, I helped implement on-the-fly transformations in MDAnalysis, as part of the GSOC
2018 program with NumFOCUS. These transformations greatly enhance the functionality of MDAnalysis, and are
a great step towards making it a complete package for treatment and analysis of molecular simulation data.
A detailed discussion on how this project could unfold can be read in MDAnalysis issue [#1215](https://github.com/MDAnalysis/mdanalysis/pull/1215).

In the first month - the time until the first evaluation - I added and modified the functions to add and 
apply transformations in the `ProtoReader` class. It was crucial to have a solid foundation for the
transformations API, and this step was the cornerstone of the project. 

 - The code and its evolution over the 4 weeks can be seen in [this pull request](https://github.com/MDAnalysis/mdanalysis/pull/1902). In this PR, a very simple translation function was
also added, as it helped with testing, and would serve as a template for future transformations

 - An issue was opened by my mentor [Jonathan Barnoud](https://github.com/jbarnoud) to add the newly 
implemented transformations to the MDAnalysis online docs - [issue #947](https://github.com/MDAnalysis/mdanalysis/issues/1947). It was fixed by me in [this PR](https://github.com/MDAnalysis/mdanalysis/pull/1948).

##Second evaluation

###Coordinate rotation

After making sure the transformations API was solid, it was time to add a library of commonly used
transformations. First was a rotation transform - `rotateby`. This transformation performs a simple rotation
of the whole coordinate system using a given directional vector and a given point as the axis of rotation.

 - The PR for this function can be checked [here](https://github.com/MDAnalysis/mdanalysis/pull/1937)
 
 - Later, I found an error in the rotation transformation calculation when using a custom point and vector. 
 The PR correcting this issue can be seen [here](https://github.com/MDAnalysis/mdanalysis/pull/2000)

###AtomGroup centering

 After the rotation, `AtomGroup` centering was the next challenge. The first centering function to be added
 was `center_in_box`. As the name says this function centers a given `AtomGroup` in the unit cell, by
 default. A `point` argument (changed later to `center_to`) can be given, and the `AtomGroup` will be
 centered on that coordinate.
 
 - the PR for the `center_in_box` transformation can be seen 
 [here](https://github.com/MDAnalysis/mdanalysis/pull/1946)
 
###PBC treatment - take 1 

 Another very important transformation - one that can be considered the mostly used when working with molecular
 dynamics simulation trajectories - is unwrapping. This transformation is used to correct artifacts cause by periodic boundary conditions. When molecules partly cross the boundaries of the unit cell (when using PBC)
their bonds are broken (visually only) and, in some molecular visualization packages, they appear as 
stretched over the unit cell. This is corrected with unwrapping functions. The `make_hole` function of MDAnalysis was capable of making the molecules, that were broken due to PBC, whole again. However, it had a severe limitation: it only supported orthogonal unit cells. 
Thus, I was set on extending the functionality of `make_hole` to triclinic unit cells.

- the PR that describes this attempt can be checked [here](https://github.com/MDAnalysis/mdanalysis/pull/1961)

Despite being very fast, it failed on more complex systems like the C20 fullerene. It later became deprecated
when [Richard Gowers](https://github.com/richardjgowers) ported `make_whole` to cython and extended its 
functionality to triclinic unit cells ([here](https://github.com/MDAnalysis/mdanalysis/pull/1965) is his pull
request).

##Final Evaluation

###More centering functions

After adding the first centering function - `center_in_box` - other two useful centering functions were
implemented: `center_in_axis` and `center_in_plane`.

`center_in_axis` centers a given `AtomGroup` on a given axis x, y or z. The `center_in_plane` transformation centers the given plane of the weighted center of the `AtomGroup` in the unit cell. These transformations are useful when working with membrane systems.

- the PR that describes both of these transformations can be accessed 
[here](https://github.com/MDAnalysis/mdanalysis/pull/1973). In this PR some changes are made to the argument
variable names and documentation of `center_in_box`.

###Fitting transformations

With the centering transformations now done, it was time to focus on molecule fitting. Transformations that
perform the fitting of a given molecule to a reference structure are useful when analyzing and visualizing
conformational changes over time. I added two new transformation functions: `fit_translation` and
`fit_rot_trans`. The first one removes the translations of a given `AtomGroup`, either in all directions
or in a given plane XY, XZ or YZ, by aligning its weighted center to the center of a given reference structure.
The latter removes both rotation and translation by performing a least squares alignment to the reference. This function can also take a plane as argument.

- the PR for the fitting transformations can be checked 
[here](https://github.com/MDAnalysis/mdanalysis/pull/1991)

###PBC treatment - take 2

The last transformations to be added were the `wrap` and `unwrap` functions. With the PR for the porting of
`make_whole` to cython now merged, I added the transformations that dealt with PBC. The `wrap`function
translates all the atoms to the interior of the unit cell - by default it does the reverse of `make_whole`.
`unwrap` simply uses the `make_whole` function, but in the context of the on-the-fly transformations API.

- [Here](https://github.com/MDAnalysis/mdanalysis/pull/2038) is the PR for these two transformations

###Demos and blog posts

With some of the transformations now merged with the 
[develop branch of MDAnalysis](https://github.com/MDAnalysis/mdanalysis), and others being reviewed, I was
asked to make a blog post in the [MDAnalysis blog](https://www.mdanalysis.org/) and a jupyter notebook showing
what someone described as the "pure magic" of the on-the-fly transformations.

- the PR for the blog post can be found [here]https://github.com/MDAnalysis/MDAnalysis.github.io/pull/87)

- the jupyter notebook demonstrating how transformations work can be accessed in 
[this PR](https://github.com/MDAnalysis/binder-notebook/pull/10)


##Closing words

The past 3 months have been a very rewarding experience. I have learned a lot about programming,
GitHub and software development on bigger projects. And, more importantly, I'm proud of my contribution to
MDAnalysis.

My mentors - 
[Jonathan Barnoud](https://github.com/jbarnoud) and [Richard Gowers](https://github.com/richardjgowers) - were
very helpful in the development of this project. I'm also thankful of 
[Max Linke](https://github.com/kain88-de), [Oliver Beckstein](https://github.com/orbeckst) for their reviews
and comments, and [Manuel Melo](https://github.com/mnmelo) for motivating me to apply to GSOC2018 and helping
me with the project and coding. 