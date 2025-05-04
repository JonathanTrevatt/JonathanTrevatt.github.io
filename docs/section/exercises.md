---
title: CAD design principals
category: Engineering Guide
date: 2024-08-26T13:45:34Z
author: Jonathan Trevatt
tags: [CAD design principals, Engineering Guide, robotics]
summary: Summary of the article
---

![|700](image/exercises/1724647008882.png)
Create this:

    - Using extrude tool but not revolve tool.
    - Using revolve tool but not extrude tool.
    - Without using revolve or sketch tools.
    - Only using sketch and revolve 3d tools, and without using fillet or chamfer 2d sketch tools.
Now go back and change a dimension.
Now go back and make 3 holes at 60 degrees in the foot.

Create a tool that holds a round shaft at 90 degrees to a hexagonal shaft.
Modify it to hold 3 round shafts at 90 degrees to the hexagonal shaft, in a line.
(Teach rotation patterns, design for solving a problem, and potentially body combines/cuts, and teach design for iteration).

Create a square motor mount with edges that wrap around the motor.
- For screw holes:
  - Sketch position + hole + rectilinear pattern, or
  - Sketch position + hole + rotational pattern, or
  - sketch position + mirror2d + mirror2d + many holes, or
  - sketch position + hole + mirror2d + mirror2d
    (Discuss benefits and drawbacks to options, which would you choose and why - exercise is about the discision process, not about right or wrong)
- Teach shell tool
- (ask them to come up with as many ways as possible to make the same open top box, which one would they use, why, what if we want it open on 3 sides? Teach shell command using sketch guide to achieve that.)


(Teach 3d printing design principles.)
Make a knuckle sized 3d printable pin hinge (at least 2 parts).
(critique and discuss according to overhang, transition strength, print direction, strength, stress concentrations, printability, etc)
(Teach components and joints)
Do it again, but this time, articulate with joints.

Make a hinge joint without pins.

Create 2 toothless gears (with a rotation markers) and joint them.
Create a sliding joint between a pin and a slot.
(provide file with objects to assemble, including gear teeth)
Create watt engine gear train. base plate -> driving gear -> driven link -> driven link -> sliding gear -> base-plate
![|700](image/exercises/1724648599835.png)


For 3d printing:
What is wrong with this design?
![|700](image/exercises/1724889559958.png)
![|700](image/exercises/1724891824356.png)

- The chamfered/curved bottom edge of the slider nut is an overhang. Small overhangs from curves can be okay sometimes, but this one is in a sliding joint, so if nothing else, it will at least need post-processing (sanding).
- Parts do not have a bottom chamfer.
  - Thin objects can flex with the bed without popping off. A chamfer allows you to slide a plastic scraper under the part.
  - Chamfers remove elephants foot effect (~0.2-0.6mm, depending on material, printer, settings, etc). Elephants foot can be sharp or unsightly, and (in this example), elephants foot on the internal hole edge of the pulley would interfere with assembly.
- Sliding clearance is probably too low which will make the parts bind. Start with 0.2mm for a tight sliding fit, or 0.3mm for a loose sliding fit.
- There is no clearance for the pin joint. Parts will bind.
- The bottom of the slider is very thin, which, depending on print material and application/forces involved, the part will likely not be stiff enough to work properly without binding.
- The pin is way too thin for its height.
  - The part design requires this print orientation.
  - Small surface area between layers is very weak.
  - Aspect ratio matters. A tall thin part is weaker than a short thin part because the lever arm is smaller.
  - Very tall-thin parts may not print reliably or as accurately because they can move around at the tip while printing.
  - Make rotating joint pins at least 5mm diameter, and add a large chamfer at the bottom where possible.
Other general design notes:
  - The ball on the top has sharp angle changes in the layer height direction. This will accentuate stair-stepping and might look bad. If you don't mind doing a bunch of sanding, or if it is a purely functional part, this might not matter.
  - Think about how the parts are going to be assembled. In this case, it's pretty simple. But you can still add chamfers for assembly purposes.
  - If a pin joint is very short/low aspect ratio, you might need to make the pin longer to help stop the part coming off the pin.
  - For a stack of parts on a pin joint, you may need to make the pin taller to provide more clearance than you expect. If each part is 0.05mm taller on average than designed, then for 5 parts on a pin, the pin height will need to be raised by 0.2mm (in addition to the designed clearance).