## Overview
This workshop will walk you through creating PCB artwork using Inkscape and KiCad.

There are two workflows:
1. Single-layer import style
2. Multi-layer footprint style

The second workflow is not easy. These instructions trace the detailed procedure for the NYCR logo example. If you get stuck, checkpoint files are provided. That means you can skip steps and practice the techniques one step at a time.

When you repeat the procedure for your own graphics, you will not be able to skip steps.

## Obtain an SVG file
This example will use the NYCR logo. I found it by
- nycresistor.com
- drag-and-drop the logo to desktop
- It is already in SVG format!

You can repeat this process or use the checkpoint file included in the repo.

> Checkpoint: NYCR-logo-horiz-original.svg

## Preparing the artwork (single-layer import style)
- In KiCad, File > Import > Graphics > Select the SVG file
![importing](images/importing.png)
- Select the layer (F.Cu, F.Mask, etc.) and scale
- Move it around *and rescale*


## Preparing the artwork (multi-layer footprint style)
### Pre-processing groups/layers in Inkscape
- Open NYCR logo in Inkscape
- Ungroup elements until letters are separate
- Regroup elements based on which letters go together
- Create layers (F.Cu, F.Mask, etc.)
- Scale everything to desired size to fit on the board. You will not be abe to rescale after exporting the footprint.

> Checkpoint: NYCR-logo-2.svg

### Editing steps
These are specific to the NYCR logo. You will need to adapt them to your own graphics.

Step 1: **NYC**: Single-layer silk screen
- Move "NYC" to `F.Silk` layer (Select elements, right click, Move to Layer...)

Step 2: **RESISTOR**: Metal multilayer
- Move "RESISTOR" to `F.Mask` layer.
- Then *duplicate* and move to `F.Cu` layer.
- Change the color of the duplicate
- (optional, preferred) *outset* it to make it visible. (It is ok if the lines are not straight.)

Step 3: **Graphic**: Putting it together
- If it is a hole, use Shift-Ctrl-K to break path. Then deselect the outer circle.
- Silkscreeen in the middle. Duplicate and move to `F.Cu` layer.
- Change the color of the duplicate
- (optional, preferred) *outset* it to make it visible.
- Use *subtraction* and *rescale* operations to put annuli on `F.Cu` and `F.Mask` layers.

> Checkpoint: NYCR-logo-5.svg

### Convert SVG to KiCad module
- Download svg2mod
```
pip install svg2mod
```
[svg2mod Documentation](https://pypi.org/project/svg2mod/)

- Convert to kicad module
```
svg2mod -p 1.0 -o nycr-logo.pretty/NYCR-logo.kicad_mod nycr_logo.svg
```

If precision is too low, it will look jagged. Decrease the precision value (e.g., `-p 0.2`) to get a smoother curve.

![low precision](images/precision.png)

> Checkpoint: NYCR-logo.kicad_mod

### Import module into KiCad footprint manager
Edit footprint libraries: add nycr-logo.pretty

![library menu](images/library%20menu.png)

![adding project library](images/adding%20library%20dialog.png)

![selecting the right one](images/select%20pretty%20folder.png)

Confirm that it looks like this:

![confirm](images/confirm.png)

<!--
~~Next go into the footprint manager and import the module~~
![importing the module](images/importing.png)

Don't import the module, just copy it to the library folder. -->

### Add the footprint to the board
- Click "Place" button (hotkey: `A`)
- filter for "nyc"
- your footprint should appear in the list
- put it on the board somewhere

![place footprint menu](images/place%20footprint%20menu.png)

![adding footprint](images/place%20footprint.png)

## Kicad: from footprint to gerbers

### Make the board outline
- In Kicad, select the outline layer (Edge.Cuts)
- Select the rectangle tool
- Pick a size (a credit card is 3.375" x 2.125")
- Right click, select "Fillet" and set the radius (a credit card has radius 0.125", about 3.2mm)

You can pick different dimensions. You can even use a circle. It's up to you. I recommend staying smaller than a credit card if you want to carry it around.

### Review in 3D viewer
- Practice moving the camera, play with raytracing, change board soldermask display color, etc.

![3d viewer menu](images/3d%20viewer%20menu.png)

![3d view](images/3d%20viewer.png)

### Plot the gerbers
- Go to File > Plot
- Select these options
- Select the output directory "NameOfProject-tapeout"
- Click "Plot"

![plotter settings](images/plotter%20settings.png)

- Zip the gerber directory

![tapeout compress](images/tapeout%20compress.png)

## Your turn: BYO graphics
- Obtain your own design
- Edit it!
- Follow the instructions above
    - Start with single layer import
    - Then do the whole Inkscape and svg2mod process
- Put it in the `nycr-logo.pretty` folder so you don't have to repeat adding library steps
- Place the footprint on the board
- You can delete the NYCR logo if you want


## Instructions for ordering
### OSHPARK
- multiples of 3
- About $7 per board

1. Go to https://oshpark.com/
2. Drag and drop the gerber zip file into the window
3. Enter your email
4. Review each layer
5. Change options (defaults recommended)
6. Checkout (~$20 + free shipping)
7. Wait for delivery
8. Enjoy your new PCBs!

### JLCPCB
This is cheaper, faster, with more options (color, thickness, etc.). It also scales better for larger quantities (~70¢ per board @ 50 boards). Most of the cost is in tariffs and shipping, so we are going to prefer OSHPARK for prototyping.

If you want to do a more advanced production run, see the slides for JLCPCB instructions, but I recommend starting with prototyping.