---
title: CAD design principals
category: Engineering Guide
date: 2024-07-26T08:35:46Z
author: Jonathan Trevatt
tags: [CAD design principals, Engineering Guide, robotics]
summary: Summary of the article
---

- Chamfers are often preferable to fillets/rounds. For machined parts, fillets are more expensive to manufacture. For 3d printed parts, a chamfer can be set to 45 degrees (the most common overhang limit), but a fillet/round in the z-direction will always cause some overhang, and the top and bottom of the curve will have pronounced layer stair-step effects.

Sharing models for 3d printing:

- Every CAD modelling program has their own native format, which stores all of the information about your model that is supported by the software. Some CAD programs support imports from other CAD program formats, but they may not implement all of the features.
- "Intermediate" formats are used to move data between different software because they are widely supported. These don't generally support all of the features of the CAD software. Some are better than others.
- Every slicer has their own native formats which store geometry and settings and such.
- Slicers convert the geometry into machine code, commonly called G-code.
There are many 'flavours' of G-code, with varying levels of cross-compatibility, but a good slicing program will automatically use the right flavour for your selected printer.
G-code written for one machine might print incorrectly or even cause damage when run on another machine.

If you are sharing a model to be edited, and you know that the other person uses the same modelling program, then use the native format for your modelling software.
If you are sharing a model to be printed (or edited in another program), use an intermediate format. I have described some of the many options in more detail below, but to summarize:
Use STEP, 3MF, or AMF.
3MF and AMF are better for colour, texture, and transparency, and allow native multimaterial printing.
STEP is better for manufacturing and further editing, supporting more accurate curves (by supporting NURBS), dimensional tolerance data, and annotations.

---------------------------
Fusion supported file formats:
  f3z, 3mf, ipt/iam, fbx, obj, skp, smt, stp/step, stl, usdz
OrcaSlicer supported file formats:
  3mf, stl, stp/step, svg, amf, obj
File formats in common:
  3mf, stl, obj, stp/step

- STL/3D Systems stereolithography (1987):
  - The most common, but generally bad.
  - Does not store size, scale, units, material, texture, etc.
  - Defines raw, unstructured triangulated surface by the unit normal and vertices in 3D right-hand-rule cartesian coordinate system.
  - Like vector graphics that only supports straight lines. This means that the resolution of curves is set when the file is exported, and cannot be adjusted within the slicer.
- PLY/Polygon File Format/Stanford Triangle Format (1994)
  - Similar to STL, but also supports color and transparency, surface normals, texture coordinates data confidence values, and more.
- AMF/Additive Manufacturing Format (2011)
  - Open standard successor to stl
  - Based on XML (a plain text format, though, it can be compressed) - easy to modify.
  - Describes volumes as triangle mesh defined by vertices (like stl).
  - Native support for units, color, transparency, materials (including composites), lattices, and constellations (multiple objects), and curved triangles.
- 3MF/3D Manufacturing Format (2021)
  - Multipart and multi-material support
  - Supports thumbnails
  - XML-based (easy to modify)
  - Full support for textures, colours, and transparency
- IGES/Initial Graphics Exchange Specification (1970's)
  - Outdated and not suitable for complex designs
- STEP/Standard for the Exchange of Product Data (1980's)
  - Supports units, material properties and tolerances, colours (but not transparency or texture), NURBS (but not subdivision surfaces), assemblies, surface normals.
  - Supports product manufacturing information (PMI) - dimension tolerances, annotations, etc.
  - Is well supported.
  - Does not support multiple materials in a part (though, this could be implemented in some slicers)
- OBJ
  - Old but widely supported
  - Supports units, multiple objects, and dated support for materials.
- File formats which support animation features, but are not generally supported by slicers
  - USD/Universal Scene Description
  - glTF/Graphics Library Transmission Format (2021)
  - ABC/Alembic (2022)
    - Supports polygon meshes, subdivision surface, parametric curves, NURBS patches and particles
  - FBX
    - Owned by Autodesk and widely supported
  - BLEND, by blender
----------------------------------

- When designing for 3d prints:
  - Think about the print direction from the start of the design. It is usually best to design a flat bottom surface to print on the build plate. But, there can also be some benefits to printing a part suspended on a diagonal.
  - Ensure that bottom layer features are at least 3 or 4 layers wide (otherwise, there may not be enough support to print the layers above cleanly).
  - If there is not enough surface area contacting the build plate, the print may not adequately adhere.
  - The plastic will shrink slightly as it cools. Upper layers cooling (and shrinking) will cause tension on the upper side of lower layers. This can cause the print to curl up at the edges (and come off the build plate), mostly in the travel direction of the nozzle. If this is a problem, you can try segmenting a long part to provide some strain relief during printing (similar concept to the divisions cut into concrete slabs).
  - Overhangs (angles in the vertical plane) should be >45 degrees to print properly.
  - Considerations for designing angles in the layer plane:
  Sharp corners in the layer plane cannot print precisely because of the layer width, which could be important for mating parts.
  Some rules of thumb:
    - The corner will always print with a round (of at least the layer width).
    - Assuming the slicer settings put layers strictly inside your designed corners, the distance between the designed corner and the printed corner is x=r((1/sin(theta/2)) - 1) (where theta is the corner angle, and r is the radius of the layer width). E.g., for a layer width of 0.2mm (r=0.1mm) and a 15 degree corner, the actual printed corner position will be 0.67mm shorter than designed.
    |Angle (degrees):| Corner prints shorter by:|
    |---|---|
    |15|6.7r|
    |30|2.9r|
    |45|1.6r|
    |60|r|
    |90|0.41r|
    |120|0.16r|
    - Do not design corners formed by tangents to circles. The angle is infintely small, with error depending on layer width and the circle radius.
    - Design a fillet on mating corners to form internal and external curves instead of corners (see section on designing circular features).
    - Radius of external curves should be > layer width (usually 0.42mm) to print accurately.
    - Radius of internal curves should be > 3 * layer width (usually 1.2mm) to print accurately.
  - Considerations for designing circular and curved features (shafts, holes, etc):
    - Consider whether the feature actually needs to be circular. We can sometimes tend to design something as circular because that is how it would be done with traditional manufacturing, even if, say, a polygon, would make more sense for a 3d printed part.
    - Holes always print smaller than designed, due to multiple reasons.
      - Holes through the layer plane have an overhang < 45 degrees. This causes messy bridging. For small holes, this can look like a flattened top. This issue can be mitigated by modifying the hole so that is has a 90 degree corner cut out above the top surface (45 degree overhang on either side).
      - Holes through the vertical plane are smaller than designed (primarily) because molten plastic is can have elastic tension like a rubber band. When a small diameter circle is printed, the tension pulls the plastic towards the centre. This effect is decreased for larger circles due to lower angles and more time to cool.
      A common way to mitigate this issue is to redesign the hole as a 'polyhole'. I.e., to have a polygonal hole with straight sides instead of a circular hole. Because it is printed with straight lines, tension does not pull the hole smaller. It also leaves a space to hold glue or lubricant in a shaft-hole pair. Generally, the smaller the hole, the fewer sides you want on the polygon (more information [here](https://hydraraptor.blogspot.com/2011/02/polyholes.html)). Orca-slicer has a feature to automatically change holes to polyholes.
      - Holes printed through the vertical plane can also be smaller than designed (in the bottom layer) due to elephants foot. Similarly, elephants foot will cause shafts to be larger than designed on the bottom layer.
    - Vertical plane circular features can differ from designed because (for cartesian coordinate printers), the circles are actually realised as many-sided polygons (with straight sides). Depending on the slicer settings, this polygon could be internal or external or in the middle of the designed curve. The number of sides of the polygon is also a slicer setting that affects this. In most cases, this effect will be too small to worry about.
    - Holes and shafts printed horizonatally will have a stair-stepping effect on their curves, which could be internal or external or averaged to the designed curve, depending on slicer settings. Holes and shafts printed in the vertical direction would have a more smooth and uniform surface finish.
    - Shafts printed in the vertical direction will be weaker than shafts printed in the horizontal direction.
    - Shafts printed in the vertical direction should be designed with fillets and chamfers on the bottom interface to prevent them from easily snapping off.
    - Shafts printed in the horizontal direction have overhang on the underside. The shaft can be modified with a flat surface to adhere to the build plate, and to make the overhang at least 45 degrees. This will cut off approximately 15% of the diameter of the circle.
  - Parts and features are stronger and more flexible in the horizontal plane than the vertical. I.e., along the fibres/layers. Parts are more likely to fracture along the layer lines.
  - If printing something with critical dimensions (e.g., mating parts), make sure to properly calibrate and level your printer. It takes time, but will save headaches.
  - Considerations for designing mating parts - there is inaccuracy in the manufacturing (printing) of each part, so clearances will be needed in every direction for mating parts. The actual clearances needed will depend on the part and the application and will likely require some amount of trial and error. But here are some general rules to start from for a well tuned printer printing 'HyperPLA' using recommended settings.
    - Dimensional error for parts in series (e.g., the height of a stack of parts on a shaft) depends on the sum of the errors of each part (E=e1+e2+e3+...).
    Allow an additional 0.1mm clearance per mating surface. E.g., an assembly of 4 stacked parts will have 3 mating surfaces. So if you want the assembly to have a height of 10mm, then the sum of the heights of the 4 parts should be approximately 9.7mm.
    - For parts mating in parallel, the error will tend to look more like E=1/(1/e1 + 1/e2 + ...). I.e., The total error will tend to decrease as you add more mating parts. E.g., imagine a part manufactured to be held in a certain position by interfacing with another part via pins and holes. If you have one pin-hole pair, the manufactured part will be less precisely positioned as compared to having many mating surfaces.
    - Include a chamfer on one or both parts to make them easier to assemble.
    - Include a >=0.3mm chamfer on the bottom layer of mating parts to prevent elephants foot from changing the designed fit.
    - For mating circular pins and holes printed in the vertical direction:
      - For a loose running fit (with a small amount of slop), allow 0.3mm diametric clearance.
      - For a running fit (little to no slop, but more friction), allow 0.2mm diametric clearance.
      - For a friction fit (for parts designed to be taken apart often, but not intended to move), allow 0.1mm diametric clearance.
      - For an interference fit (needs to be forced together - not intended to be separated often), allow no clearance.
      - For friction and interference fits (where the parts are not intended to move or act as pin joints), consider whether a non-circular pin and hole would be better.
    - For a friction fit for thin slots (<~1.5mm width), the clearance needed along the length of the slot is less than that needed for the ends of the slot, since the ends will print smaller than designed. You can design oversized holes at the ends of the slot so that the actual fit of the slot and its mating part will depend only on the clearance along the wall. This will give you more reliable behavior.
    - When designing objects with small features or tight fits, choose a random seam pattern rather than aligned. You cannot directly choose where your slicer puts an aligned seam, and it may put it in a spot where it will interfere. A random seam pattern will be more consistent. Note that the effective diameter of a shaft is increased by the seam, and that it is increased by twice as much by a random seam than by an aligned seam.
  - Different types of 3d printing has different quirks and print artifacts to consider. For example, laser sintering might not have overextrusion, but they do have porosity in a way that FFF/FDM does not. Here are some quirks of FDM printing to be aware of:
    - Molten material has viscocity and elasticity and tension. Since the material does not cool instantly, it gets stretched and pulled by the motion of the nozzle. Holes print smaller than designed partly because this tension pulls material towards the centre of a curve, like a rubber band.
    - Elephants foot - where the bottom few layers are squished wider than others. This is usually caused by a bottom layer that is too thick (but can also be cause by improper level or too high temperature at the start of the print). It is common to increase flow rate (and correspondingly, temperature) to deliberately print a thicker first layer on a print. This increases the pressure of the molten plastic on the first layer, which helps it to adhere better. It also helps account for deviation in the beds flatness to produce a flat layer of plastic to build on. But it can also lead to elephants foot. This can be mitigated by adjusting the 'initial layer horizontal expansion' setting to a negative value, which scales the size of the first layer down, or by adding a chamfer on the bottom print surface. Can also be removed by printing with a raft, for when adding a chamfer is not possible.
    - Layer lines. The printed filament has an oblong cross-section. When stacked on top of each other, two oblongs have a crease where they meet. This is a layer line.
    - Seams. Over-extrusion when the nozzle moves between layers is common. This is called a seam. It can be mitigated by adjusting retract settings in the slicer. Orca-slicer has the option to have the seams aligned, or to have the seams randomised. A randomised seam presents as a random pattern of ~0.2mm bumps across every surface.
    - Under-extrusion. Chronic under-extrusion can be fixed by adjusting slicer settings. If the flow rate of material through the nozzle momentarily decreases, this can present as holes in a surface as the extrusion thins. This can be caused by unhomogeneity of the filament. I.e., the filament diameter or consistency varies. It can also be caused by moisture in the filament.
    - Ghosting.
    - Stringing - very fine fibres sticking out of the print. This can be caused by high nozzle temperatures and/or filament sticking to the nozzle.
    - Print failure due to failed adhesion to the print bed. This can be caused by many things, including insufficient levelling, wrong temperature of build plate or nozzle, part bending due to cooling induced tension, and more.
    - Print failure due to nozzle clog. Can be caused by bad filament or low nozzle temperature or carbonised (burnt) filament in the nozzle.
    - Print failure due to poor adhesian between layers. Caused by low nozzle temperature or low box temperature (for some materials) or bad bed level or overhang or bridging.
    - Bridge/overhang stringing. Strings of extruded filament loosely attached due to overhang or bridging (printing over a gap). Can be mitigated by adjusting design or temperature or fans or bridging speed, etc.
- Exporting files, and naming errors
- Importing meshes to edit

List of printing effects to be aware of:
![|700](image/GeneralCADadvice/1721103988538.png)

1. 90 degree corner is not sharp. This is because:
   1. The cross-section of the extruded filament is circular, not square.
   2. The plastic doesn't cool instantly, and molten plastic has elasticity, and thus can be under tension like a rubber band. As the nozzle moves away from the corner, the tension causes it to pull into a circlular shape.
   Also note that (although not visible here), turning a corner causes slight over-extrusion on the inside of the turn.
2. Circular hole is smaller than designed. This slot was designed to be 1.2mm wide with (1.2mm diameter) semicircular ends, giving a 0.1mm clearance all the way around a 1mm wide wall. But on the ends, it is actually a friction fit. The wall printed very close to the designed 1mm. In general, circular holes (in a well calibrated printer) print smaller than designed because of a number of compounding reasons. One of the primary reasons is that a curved string under tension will straighten, tightening the circle. One way to counteract this is to use polygonal holes instead of circular. This way, tension pulls the plastic tangentially to the hole, rather than radially. I have found that, generally, when designing 3d printed circular holes to fit over shafts, a clearance of 0mm gives interference that would be difficult to remove if you are able to force it together, 0.1mm gives a friction fit that can be removed, 0.2mm gives a freely rotating pin joint which has no slop, but has a bit of friction and could use lubricant, and 0.3mm gives a freely rotating pin joint with less friction, but a small amount of slop.
3. The actual slot size in the middle of the slot varies between 1.1 and 1.2mm - possibly in part due to elephants foot, where the bottom layer of a print 'smooshes' flatter and wider than desired.
4. This print defect was probably caused by a temporary clog in the nozzle. After the failure due to the clog, subsequent layers may not adhere and the nozzle rubs against and potentially burns the loose filament. In this case, it was able to eventually build a solid enough base for the upper layers and the entire print was not ruined.
5. Microscopic enlongated holes along the layer lines caused by momentary underextrusion. This can be caused by a too low printing temperature, inconsistent filament, or moisture in the filament.
6. Stringing caused by filament sticking to the nozzle. Can be caused by overextrusion or too high printing temperature.
7. 'Seams' - overextrusion where the nozzle stops and moves up to the next layer, in this case, about 0.2mm diameter and maybe 0.05mm depth. Can be mitigated with retraction settings. Orca slicer allows you to choose whether to have seam positions line up, or randomised (as shown in this image). Unfortunately, you cannot choose a specific location for the seams. Lined up seams probably look better, but random seams where chosen in this design so that clearance between pins and holes are more consistent.
8. Layer lines. Surface indents where layers meet and bulge in the middle of each layer.
9. Holes through layer plane prints inaccurately. The circle (and all curves) is rasterised. I,e, split into a series of stair-steps, since each layer has a defined height. The top of a circle has more than 45 degree overhangs, so without support, the top of the circle flattens out low due to gravity.

- Solutions to fitting two parts together, e.g., a tube on a tube:
  - Good assumptions for clearances over whole surface + trial and error
  - Good assumptions for clearances + sandpaper. Try using 3 nubs to hold in place -> less sanding to get it perfect.
  - A cantilever clip mechanism, so that you don't need the clearances to be perfect.
  - A spring mechanism, so the clearance don't need to be perfect, and it fits on a wider range of tubes.
  - Use chamfer to make self-centering
  - Using other parts for security
    - screws, to pull two parts together
    - Squishy inserts (like an o-ring) - can be combined with clip

- Types of fit:
  - Clearance
  - Loose-running
  - Running
  - Friction
  - Interference (can be used for clips)

- Error can affect accuracy or precision. What I call accuracy errors (like skew and scale issues) decrease as print size decreases, and can generally be nearly completely removed in a high quality well calibrated printer. Precision error, is error that does not change with print size, like layer lines and seams and over/underextrusion, and can be much more difficult or impossible to remove, since it is inherent to the printing technology. Therefore, the error compared to the part size is much larger for small parts than large ones. This is a problem when designing small parts with tight clearances because normally irrelevant printing effects become can stop your parts from assembling or performing properly.

Slicer settings:
https://wiki.creality.com/en/software/creality-print/parameter-quality
- Layer height should be a multiple of Z-precision. Otherwise, some layers will be thicker than others and you will get a striped effect.

Slicer settings vs design:
There are some operations and features that can be baked in to the design, or can be set in the slicer settings.
For example, reducing the width of the bottom few layers to compensate for elephants foot. You can design your part with a bottom surface chamfer. Or, you can use the elephants foot compensation in your slicer.
- If you rely on the slicer settings, then by default, your part may not print correctly, unless the user is using appropriate settings. But, it lets the user optimise the settings for each material or printer.
- If you design the features in, then you do not have to rely as much on the user having the correct slicer settings, but they won't be optimised by material or printer.
- There are also differences in the features available with the two options. For example, a slicer will generally compensate by applying an x mm offset on the first n layers (x and n are user options) for every surface touching the build plate. This can compensate for the selected resolution (layer width). Using the model design, you have much better control over the nominal geometry of the change (you can use a chamfer or a fillet or you can only apply it to some edges, etc). But, you can't compensate those design elements depending on the slicer settings (and it's tedius to go back and forth).
- There are similar arguements for other settings.
- In short, use your best judgement, erring on not relying on slicer settings, and be careful not to design your parts around faulty calibration of your slicer settings and printer.
- For the specific case of elephants foot, I do both. I calibrate the printer to minimise elephants foot, but I also use a bottom chamfer because it makes it easier to remove from the print bed.
- For hole tolerances, adjust the X-Y hole compensation until it measures correctly on calipers. Then use offsets in your design to determine the fit.
- When using rough resolution, I sometimes get an effect I call elephants head. It is similar to elephants foot, but much less pronounced and at the top layer. It also doesn't seem to have any built-in settings to compensate for it in orcaslicer settings. So put a top chamfer on.

![|700](image/Designfor3dprinting/1725920415350.png)
![|700](image/Designfor3dprinting/1725920480548.png)


### Smoothing and surface finish

3D printing leaves surface artifacts and generally unsmooth surface finishes. Different technologies are different. The most common is stair-stepping due to layered processes. Sintered powder type printers tend to have a rough but consistent surface finish. FDM prints tend to feel smooth horizontally but rough vertically because of layer lines caused by inconsistent layer width though the height of the layer. Resin printers still have layer lines, but they can have resolutions high enough for the lines to be invisible to the naked eye.
For an aesthetic print, you might want a different surface finish. There are a few ways you can do this.
Note that any smoothing process will appear to reduce the crisp details of the parts. Sharp angles will be eroded and rounded over.
More than likely, you will want/need to combine multiple techniques.

- Calibration and print settings
  - Reduce layer height. This is the obvious one. If you need more detail, max out your printers detail settings. The extra time printing will save you time sanding.
  - Set print orientation to optimal to reduce stair-stepping.
  - Seam location. Setting seam location to random puts bumps distributed over surfaces. Aligned makes them all line up in a row. Choose your poison.
  - Speed. Going too fast can cause vibrations, like ghosting, that can show up in the surface finish.
  - Vibration calibration. Some printer firmwares allow you to run a test to see what motion frequencies and directions cause the worst vibrations, and uses this data to reduce vibrations during printing.
  - Filament that has spent more time in the hot-end will cause overextrusion. Use a high-flow hotend, print with same speeds across features, print fast.
  - Use 'print outer layer first' setting with 'inner-outer-inner'. This will purge the filament that has spent more time in the hot-end waiting between layers into an inner layer, then print a consistent outer layer, then fill the cavity between them. This will also improve dimensional accuracy.
  ![|700](image/Designfor3dprinting/1725509881135.png)
  - Using a single drive gear extruder gives more consistent extrusion because duel gears never mesh perfectly.
  - Use properly dried filament for more consistent extrusion.
  - Other calibration steps.
- Exotic slicer settings and G-code manipulation.
There are settings in most slicers that changes the surface finish, but they generally only work for the top-most surfaces.
  - 'smoothing/ironing' - The printer uses the nozzle to go back over the top surface like an iron, to re-melt and smooth out layer lines.
  - 'Fuzzy surface' - similar to smoothing, except with vibrations to get a fuzzy finish.
  - Non-planar printing - normally, the z-position (height) of the nozzle only changes in discrete levels (set by the layer height) between layers. This causes the stair-step effect. In non-planar printing, the nozzle follows a contour in the z-direction instead. The software is just no there yet though and it is not available in slicers.
  - At this link [here](https://github.com/teachingtechYT/PrusaSlicer/releases/) there is a plugin for Slic3r and Prusa3d slicers to add a non-planar layer on the top surface of a model.
  - ![|700](image/Designfor3dprinting/1725505852332.png)
- By removing material
  - Sanding.
- By filling with extra material
  - Primer/paint.
  - Painting with resin/epoxy specially made for this purpose (expensive).
  - Painting with long dry (>=1 hour) 2-part epoxy.
  - For resin, you can paint on more of the base resin (or resin mixed with talc, to change viscocity), then cure in UV again.
- By filling with itself, i.e., using a process to smooth the hills into the valleys
  - By heating/melting
  (times, temperatures, and effectiveness varies by material)
    - Place in oven.
    - Blast surface with blow torch.
  - By using a solvent
  (effective solvents and side-effects on model vary by material
   acetone is a solvent for ABS
   PLA needs a more dangerous, expensive, and hard to find solvent
   some specialised materials will dissolve in ethanol)
    - Applied via vapour bath.
    - Applied directly.
- Use a different printing process
  - Resin printers have different constraints (generally smaller, higher operating cost, messy, toxic, needs curing, and some of the factors than influence print speed are different). But if they aren't a problem, a high resolution resin printer can print layers that are too small to discern by eye.

A sanding procedure:
1. Scuff with 60-grit. Will give rough finish for bog to stick.
2. Apply thick layer of spray bog/putty filler.
3. Sand away nearly all of it with 120 grit, so that the putty is only in the gaps.
4. Apply primer.
5. Sand with 400-grit.
6. Apply paint (suggested MTM range 'montanna' paints).
Use double sided tape to hold in place for spraying, so it doesn't fall and get ruined.
Wear PPE!
![|700](image/Designfor3dprinting/1725510388928.png)
![|700](image/Designfor3dprinting/1725510329999.png)

Infill:
![|700](image/Designfor3dprinting/1725856343195.png)
<https://www.printables.com/model/20840-prusaslicer-infill-type-charms-with-name>
![|700](image/Designfor3dprinting/1725856498555.png)
<https://www.printables.com/model/221206-prusaslicer-infill-sample>

- Most people seem to agree that gyroid is the best for various reasons.

Circles:
- Axis in X-Y:
When printing circular features in this direction there will be stair-stepping and overhang (on top for a hole/hollow, underneath for a plug/pin/shaft). This problem gets worse as circle size increases. There are a few ways to deal with this, depending on your requirements:
  - Ignore it.
    - If tolerance requirements are low and you don't care how it looks. E.g., A hole for loosely holding a smaller tube in place.
    - If the hole size is small enough that it won't matter. E.g., a hole intended for an M3 screw to self-tap.
  - Supported print.
    - If the rounded feature is not too deep and narrow to remove support, and does not wind like a long channel, and you don't care about the poor surface finish or the post-processing to remove the supports.
  - Teardrop.
    - A hole can be printed with a 90 degree wedge tangent to the circle on the upper side.
  - D-shaft.
    - A pin/shaft can be designed with a bottom section removed so that it can be printed flat against the print bed with the walls making 45 degrees (or whatever your overhang cutoff is). This is suitable for both keyed shafts and rotating pin joints.
    - Similarly, a matching D-shaped hole is suitable for a keyed hole, and an elongated D shape may be suitible for a pin joint hole.
  - Split and glue.
    - This can be used for holes and fills, depending on your design.
    You can split a body into parts that can be printed on a flat face, then fixed together again afterwards (e.g., with glue). This adds post-processing steps and the parts have a chance end up misaligned if you haven't designed registration features.
  ![|700](image/Designfor3dprinting/1726186806249.png)
  ![|700](image/Designfor3dprinting/1726186870641.png)
- Axis in Z:
Curves printed in this direction have different effects that dominate.
  - Overhang and stair-steps are not a concern.
  - Surface tension causes the lines to pull in towards the centre, so holes tend to print slightly smaller than designed.
    - Compensated with X-Y hole compensation slicer setting.
  - Curves made of straight lines visible on the part.
    - A mesh file defines curved surfaces by approximating them as straight lines between vertexes. Increasing file resolution makes lines smaller and less visible.
    - The slicer also approximates curves using straight lines as well. Increasing resolution setting improves this. Or, use the 'arc-fitting' option, which approximates curves with circular arcs (G2 and G3 G-code commands) instead of straight lines. The arcs are fit within a tolerance and do not necessarily pass through the vertices. I assume that this setting is not on by default because some very old printers may not support G2/G3 commands.
    - STEP files natively support fully defined NURBS (curves). The slicer will still need to approximate the curve in G-code, but this elliminates the input file resolution being the issue.

Screw threads:

Wall order:
![|700](image/Designfor3dprinting/1727748487788.png)
![|700](image/Designfor3dprinting/1727748520905.png)
Outside first -> more dimensional accuracy, but bad for overhangs


Potential problems with this print?
![|700](image/Designfor3dprinting/1728512531795.png)
![|700](image/Designfor3dprinting/1728513165726.png)
Prongs of comb are very thin. At the start of the print, they will all be connected on the same layer, which will give them stability. But they may not print well above the chamfer.

Extreme overextrusion example:
![|700](image/Designfor3dprinting/1728513100180.png)

Printing pocket holes without support:
  - Flip orientation (if possible).
  - Print normally without support. If aesthetics aren't important, e.g., to capture a M6 nut, this might be okay.
  - Add chamfer. Suitable for low force applications, since a captive nut will have a hollow behind it. ![|700](image/Designfor3dprinting/1729218256625.png)
  - Sacrificial bridging. Bridge/fill hole with thin extrusion (~0.4mm), then cut or drill out. (Requires extra machining step, and not always pretty).
  - Sequential bridging. Use multiple thin overlapping bridges for the overhang to minimise bridge distance. ![|700](image/Designfor3dprinting/1729218400799.png)