Status View
===========

This sample projects demonstrates how to load a NIB and position it using constraints.

Additionally, there are 2 other views which are placed into the parent view to show
how layout and animations can work with a view without constraints and one with
constraints while also handling orientation changes.

## Animating with Frames & Constraints

Animating a view to a different position before constraints was done by changing
the value of the frame using the origin or size properties. Now changing a
constant value on a constraint will cause the position change. One gotcha is
it is necessary to call ```setNeedsLayout``` within the animation block on the view
which is moving. Otherwise it will jump to the position suddenly instead of
moving gradually to the position over the animation duration. It may also be necessary
to call ```layoutIfNeeded``` which is done redundantly in this project to ensure
animations occur as intended.

If you do see sudden jumping and you are calling ```setNeedsLayout``` you may be
calling it on the wrong view. Double check that it is calling ```setNeedsLayout``` 
on the view affected by that constraint.

## Rotation & Orientation Gotchas

Rotation to a different orientation was tricky. First the classic gotcha
is the fact that the width and height stay the same in landscape mode, 
meaning the height of the screen in portrait remains the height though
in landscape mode you may think of it as width. When calculating
position you will want to swap out height and width values for the screen
to get accurate positioning values. See the ```SSTViewController``` class for
the ```blueBoxDownFrame``` and ```blueBoxUpFrame``` methods for an example of how
calculations change.

Another gotcha is the check for portrait or landscape orientation. You'll
notice in those frame calculation methods that ```UIDeviceOrientationIsLandscape```
is used instead of ```UIDeviceOrientationIsPortrait``` because the orientation
can start out as Unknown when the app is started. When it is rotated to 
landscape the mode is finally set. Rotating back to a portrait orientation
changes the value which makes it reliable. Since the app starts out in
portrait orientation it is possible to check if the orientation is not
landscape to see if it is portrait. By assuming that the check for
portrait is accurate the frame calculations were behaving very oddly
until I checked the return values of both of these functions.

In order to detail what is happening I created the ```logDeviceOrientation```
method to log out various details. This method could be called in
```viewWillAppear``` and in the ```willAnimateRotationToInterfaceOrientation:duration:```
methods to reveal what is happening.

It appears that constaints do a better job at handling changing in position during
orientantion changes. When the blue box is moved using just a frame the position
after an orientation change needs to be asserted again otherwise it moves off to
an unexepected position. Nothing needed to be done about the red box which is
controlled by constraints.

## Strange Behavior

The status view which appears by coming into view from the top, showing for a moment
and then moving out of view did not work with changing orientations. It would always
default to going out of view with the contant value of -20. To ensure the value stayed
as intended a boolean value is used to set the intended position and assert that position
when the orientation notification fires.

Once the rotation notification fired and the contant value was set based on the boolean
value it seemed to have a side effect on the hiding animation, which used a delay value
as a part of the animation. Setting that value may have canceled the animation so it has
been changed to have show and hide methods with completion blocks so the animation is not
disturbed due to a change in the value when the device is rotated and constant is set.
The boolean value helps to assert the proper value and maintains the intended behavior.

License: BSD  

------

Brennan Stehling  
[SmallSharpTools](http://www.smallsharptools.com/)  
[@smallsharptools](https://alpha.app.net/smallsharptools) (App.net)  
[@smallsharptools](https://twitter.com/smallsharptools) (Twitter)  
