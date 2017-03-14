# Physics and Animation Project 1
# Abdullah Bin Abdullah

## Introduction
The purpose of this project is to recreate Babbage's Difference Engine Number 2's calculating portion of the machine in Unreal using physics objects and animation. The Difference Engine is made to solve polynomials using the method of finite differences.

## The Babbage Difference Engine Number 2
The Babbage Difference Engine Number 2 contains 8 columns which are numeric storage units. Each column has thirty one gears called figure wheels that are used for the calculation. The storage unit starts from the least significant digit at the bottom and the most significant digit at the top. Negative numbers are dealt with using the 10s complement method. Each figure wheel contains 40 teeth and each quarter contains numbers from 0 to 9. Numbers are added by having one wheel be the Accumulator and the second being an Addend.

![alt text](screenshots/NumericStorageUnits.png "The numeric storage units in the Difference engine. Screenshot from MechanicalComputing on Youtube")

If you put two figure wheels together and turn them by a certain amount, the accumulator wheel will contain the result of the rotation but the Addend wheel will lose its value in the process. To counteract this, Babbage added a third wheel called the Sector wheel which restores the value to the Addend wheel which is needed for the second phase of calculations.

The sector wheel was made in a way where it can interact with either both figure wheels, one figure wheel or neither wheels during the calculation. The sector wheels are put in between the columns of figure wheels and alternate when they are interacting with the figure wheels.

The way numbers are carried over to the digit above them in a column is by using a carry mechanism. The figure wheel contains a tab when the wheel is rotated past nine and this tab hits a switch that activates a lever that 'warns' the system that the number will be carried over. The carry switch is then hit by a column that is built to both reset the switch and move the wheel to the new value. If the carry switch wasn't activated then the column wouldn't hit the switch and the digit value wouldn't be changed.

## Issues Encountered
The Unreal project uses static meshes from qwertyzzz18 who published the 3D models of parts of the Difference Engine on Thingiverse for 3D printers. Not all of the parts of the Difference Engine is included as he came up with a problem of materials not being strong enough to create a sturdy carry mechanism. So for this project I was managing with only the Figure Wheel and Sector Wheel.

Problems I encountered had to do with collision detection and deciding on what objects should simulate physics. For collision detection, Unreal's implementation when auto-generating doesn't take into account the teeth of the gear Although it follows the shape of the wheel. There was an option to have complex geometry as the collision geometry but this feature doesn't allow for physics to be simulated on the object. The only option left was to create the collision boxes myself. I didn't add collision geometry around the whole shape as I don't think anything else will interact with any other part other than the teeth of the gears. If the situation that needs these other parts to be interacted with come up then I could add more collision shapes into the static mesh.

![alt text](screenshots/CustomCollisionBoxes.png "Collision boxes represented by the purple lines")

Other issues I found was that the sector wheel's pivot point is not at the origin point so I had to open the file in blender, realign it and then reimport it into the Unreal project. This was easily done and I didn't have to change any structures that I made in Unreal.

## Construction of the Calculating Section of the Difference Engine in Unreal
During planning I was deciding on how to manipulate the behaviour of the Difference Engine. In the original machine, the Addend wheel drives the rotation and then the Sector wheel resets it into place. This would mean that eventually all the parts in this project will have to be done using blueprint animation and none of the parts will be driven by physics. Unreal does allow you to manipulate physics objects but I didn't implement a way to control the amount of rotation when that is done. There is also the option to dynamically set and unset the objects as physics objects in order to use animation functions like the timeline. My implementation has it so that the sector wheels contains the initial rotation and the reverse rotation. This leaves the figure wheels to have the physics properties and the sector wheels to have the animation code.

The way I implemented the blueprint for the sector wheel is to have a timeline that increments two floats, one for the angle of rotation and another for the height. This code runs off the EventTick so that means that the values are calculated every frame. 

![alt text](screenshots/SectorWheelBlueprints001.png "Two branches to check if the sector wheel is active and if it needs to revert its rotation")

I set the height to be applied after the rotation is done but that means that there won't be any animation as it changes its height.

![alt text](screenshots/SectorWheelBlueprints002.png "The rotation being applied")

To run the reverse rotation for the sector wheel, I set a bool that checks a branch if it is flagged and runs the timeline in reverse. 

![alt text](screenshots/SectorWheelBlueprints003.png "The first instance where height is applied to the position")

After the rotation has been reverted, the height is applied once again and the sector wheel is set to inactive. This prevents the timeline from applying anymore changes to its rotation or positions. At this point this Sector wheel calls a function in the second phase Sector Wheel to set it to active.

![alt text](screenshots/SectorWheelBlueprints004.png "The second time the height is applied to the position and activating the second phase sector wheel.")

The second phase Sector Wheel is similar to the first phase but it runs the rotation timeline in reverse order. At the end of this blueprint, it resets the first phase sector wheel to active as well. This creates a loop between the first phase sector wheel and the second phase sector wheel, which is good to demonstrate the mechanics of the engine.

![alt text](screenshots/FullSectorWheelBlueprint.png "The full sector wheel blueprint.")

## Conclusion
The missing piece of the demo is the carry mechanism to carry numbers up the column. Ways to improve the demo would be to reduce the number of polygons on the imported models, either make the timeline code more general to include both phase 1 and phase 2 calculations or to make it so all the gears are physic objects and apply rotation indirectly through a different part that would closely emulate how the original Difference Engine works.

## Bibliography
http://www.thingiverse.com/thing:7609 - qwertyzzz18's 3D models of part of the Babbage Difference Engine 2

https://en.wikipedia.org/wiki/Difference_engine - Information on the Difference engine

https://www.youtube.com/watch?v=vdra5Ms__9s - The video on the mechanics of the calculating section of the Difference Engine by MechanicalComputing