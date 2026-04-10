# Image Processing and Basic Segmentation

*Lab authors: Hunter Elliott, Marcelo Cicconet, Beth Cimini, & Esteban Miglietta* . 

<small>This file last updated 2025-03-29.</small>

---

## Learning Objectives

- Experimenting with filtering
- Get experience with CellProfiler{cite}`Stirling2021-sg`
- Bonus: Detecting edges and ridges
- Bonus: Using encapsulated {term}`segmentation` modules in CellProfiler

**Lab Data** in [this folder](https://tinyurl.com/QIAnalysisLabData) (Image_Processing_&_Basic_Segmentation)

Remember to **unzip** the data folder after downloading.

---


## **Basic Segmentation**

As discussed in lecture, classical {term}`segmentation` typically consists of a stereotyped set of steps
1. Smoothing
2. Thresholding
3. Filling holes in threshold masks
4. Local maxima detection in smoothed image
5. Watershed
6. Filling holes in detected objects

Here, we will demonstrate all these steps in CellProfiler, and let you play with how the parameters you choose to see how they affect the {term}`segmentation` you get.

```{important}
There are two main ways to move around in CellProfiler once in test mode - keep 
- If you hit the `Step` button  <img src="images/processing_detection/Step.png" height="30px" />, the next module (but only the next module) will run
- If you hit the `Run` button  <img src="images/processing_detection/Run.png" height="30px" />, all the remaining modules will run (unless you've added a `Pause` <img src="images/processing_detection/Pause.png" height="30px" /> button somewhere)
```

### Load files
- Open a new CellProfiler window, or open a clean starting version by going to File -> New Project  
- Load the `Basic_Segmentation` folder into the Images module as above
  - This will load 3 sets of images, each with a DAPI image and and actin image
- Load the `basic_segmentation.cppipe` pipeline file into the pipeline panel as above

```{note}
CellProfiler `.cppipe` files are just text files. You can open them in a basic text editor such as Notepad to see what's inside. It also makes them easily shareable with your labmates or even to attach as supplemental information on a paper.
```

- Enter test mode by hitting the `Start Test Mode` button  <img src="images/processing_detection/StartTestMode.png" height="30px" />

```{tip}
Curious about how CellProfiler figures out that this is 3 sets of 2 channels each, and not 6 individual images or one 6 channel image? Look at the `NamesAndTypes` module!
```

### Smooth the images and find local maxima

- Run the `Smooth` module - without going nuts, how does your result change if you change the method? What about the smoothing diameter?
- Run the `FindMaxima` module to find local maxima in the smoothed image created above 
  - You may find it helpful to increase the maxima preview size and/or color. 
  - Is it clear how all of the settings control where maxima are found?

### Threshold and fill holes in the threshold masks

- Run the `Threshold` module on the smoothed image
- Run the `RemoveHoles` module on the thresholded image to remove any holes in the masks
  - If there aren't any holes, try setting the `Threshold Correction Factor` in the `Threshold` module to something like 1.1 or 1.2 (from 1.0) to create some
    - What do you think that setting may do, mathematically? 
    - Click the help button (<img src="images/processing_detection/Info.png" height="25px" />) to learn if you were right.

### Watershed into objects, and fill holes in the object masks

- Run the `Watershed` module to turn your masks into actual CellProfiler objects
- Run the `FillObjects` module to remove any holes that may remain in the objects themselves

### Evaluate your performance across multiple images

- It is **_critical_** when doing {term}`segmentation` to evaluate your objects against your raw images - otherwise, you cannot be certain your preprocessing steps have not introduced errors. Run the `OverlayOutlines` module to see how you did.
  - If not well, where do you think you need to make changes?
- It is also **_critical_** to see if your settings work well across not just one image, but all of your images. Evaluate your performance on all 3 image sets.
```{tip}
To move between image sets in CellProfiler, use the `NextImageSet` button  <img src="images/processing_detection/NextImageSet.png" height="30px" />
``` 
  - If your performance is not equal across all 3, in what ways?
  - If your perfomance is not equal across all 3, is there anything you notice about the ones where it does vs does not perform well?
  - Were there modules you thought were superfluous for one image set but ended up helping for another?


```{caution}
How do you know when your {term}`segmentation` is "good enough"? It's a SUPER common but a very complicated question! You can check out [this blog post](https://carpenter-singh-lab.broadinstitute.org/blog/when-to-say-good-enough) for one attempte at helping come up with a set of rules for making that decision.
```

## **Bonus Exercises - Segmentation**

### Bonus Exercise: Encapsulating all of {term}`segmentation` into one module

- At the end of your {term}`segmentation` piepeline, you'll see two modules that are there but inactive - they have an empty checkbox <img src="images/processing_detection/InactivatedModule.png" height="20px" /> . Click this box to enable the `IdentifyPrimaryObjects` module - it should now look like this: <img src="images/processing_detection/Check.png" height="20px" />
- Run the `IdentifyPrimaryObjects` module - how does it do at identifying your nuclei directly from the DNA image?
- Under the hood, `IdentifyPrimaryObjects` is doing all the steps we previously did, plus some filtering out of objects based on criteria you set (like whether they touch the edge). Can you `Identify` (😉) which setting corresponds to each of our previous steps?
```{hint}
Two of the steps are combined in a single setting!
```

### Bonus Exercise: Using seeded watershed to build Cells from Nuclei

- After you've enabled `IdentifyPrimaryObjects`, you can also enable `IdentifySecondaryObjects`, which is designed to take an initial, smaller, internal object (most often a nucleus) and build a larger object around it (most often a cell). Enable <img src="images/processing_detection/Check.png" height="25px" />and run this module.

```{margin} What happens when there's more than 1 nucleus per cell?
Check out this [Ask Erin/Dear Beth episode](https://www.youtube.com/watch?v=NIuECUW_OeI&list=PLCucasmZ3XNn-tvZDSUBoOtMLoFxopdiy&index=5) for some tricks on how to deal with this situation.
```

- What settings correspond to our {term}`segmentation` steps, as in IdentifyPrimary?
- Are there any settings that are new? Click the help button (<img src="images/processing_detection/Info.png" height="25px" />) to learn about what these do and how they work.

### Even harder {term}`segmentation`

Nuclei are relatively easy to segment relative to cells - they are bright, fairly uniform, and often reasonably well spaced.
How well can conventional {term}`segmentation` work on cells, and how easily can it be done?

- Download the `DL4MIA/hard/train` from [this link](https://tinyurl.com/DL4MIAhard) 
- Start a new CellProfiler project (or open a new CellProfiler window) and drag and drop that folder of images into the Images panel
- Drag and drop the `advanced_untuned.cppipe` file into the CellProfiler pipeline panel. Other than the input module settings, no {term}`segmentation` settings have been tuned at all in this pipeline.
- Try to create an accurate {term}`segmentation` of these cells - you will want to turn the advanced settings on. How well can you do? What settings seem to make the most difference?

```{hint}
Here is what an experienced image analyst came up with - so at least this level of accuracy is possible!
<img src="images/processing_detection/CellProfiler_AdvancedIDP.png"/>
```

### Bonus: throw some measurements in the mix

Now that you have segmented your cells, you can measure lots of things in each one individually!

- Use the (<img src="images/processing_detection/AddModule.png" height="25px" />) button to open the `Add modules` window.
- Use the search bar and look for `MeasureObjectIntensityDistribution` (or find it under the Category "Measurement" on the list on the left panel). Double click the module or press the (<img src="images/processing_detection/AddToPipeline.png" height="25px" />) button.
- Select what image to measure on and what objects to measure in.
- Press "Add another heatmap display" 
- Execute the module by pressing <img src="images/processing_detection/Step.png" height="30px" />
```{hint}
These are the results obtained from the example segmentation. How do your results compare to these? How about you labmates'?
<img src="images/processing_detection/CellProfiler_MeasureObjIntDist.png"/>
```
- How can you interpret the results shown? Check the  (<img src="images/processing_detection/Info.png" height="25px" />) button for the module or for each parameter to understand the output better.

---
## **Bonus Exercises - Filtering**

### Bonus Exercise: Steerable Filtering and Ridge Detection

*In this section, you will segment microtubules by using filters to
accentuate “ridge-like” structures in the image.*

```{note}
For this exercise, you'll need a plug-in (SteerableJ) that runs on an older version of ImageJ. Please launch `ImageJ_SteerableJ_Win/ImageJ.exe` (or `ImageJ_SteerableJ_Mac/ImageJ.app` if you're on a Mac), available with the data for this lab. Or you can follow [these instructions](http://bigwww.epfl.ch/demo/steerable/download.html) 
```

#### Basic thresholding

*First, try segmenting the microtubules with simple thresholding (for
comparison to steerable filters)*

- Load one of the images from `Image_Processing/Microtubules/easy` into ImageJ

- Subtract the background (Process \> Subtract Background…). To view the
  background image, select “Create background (don’t subtract)”, and
  “Preview”. What is a good choice for the length scale (radius)?
  Deselect these and press OK. Why is background subtraction important
  for basic thresholding?

- Threshold the image. You can test this out manually (Image \> Adjust
  \> Threshold…). Is there a threshold that will accurately segment the
  microtubules?

---

#### Steerable Filtering

- Re-load the image in ImageJ

- Launch SteerableJ (Plugins \> SteerableJ). There are multiple
  parameters that can be tuned:

  - *order:* Do you want to use an even or an odd order? Hint: look at
    the image of the filter kernel in the top left panel. Note: Higher
    order filters are more selective for orientation.

  - *mu:* This parameter controls the sensitivity of the filter. A
    higher value makes the filter less prone to false positives, but
    also reduces localization precision.

  - *sigma:* This is the scale of your structure. Match this to the size
    (width) of the ridges in your image.

- After tuning your parameters, press ‘Run’.

- Now try thresholding the filtered image. Is it easier to find a good
  threshold?

- The SteerableJ plug-in has a button to “Refine Features” which will
  further clean up the image by applying non-maximum suppression. Press
  ‘Refine features’ to view this output.

- Steerable filters are designed to be sensitive to the orientation of
  detected features. The initial output is a projection, which displays
  only the magnitude of the filter response, not its orientation. Press
  ‘Show Orientation’ to view the orientation of maximal response of the
  filter. To view the raw data associated with this image, press
  ‘Display rotation’. Every slice in this z-stack is the response at a
  particular orientation. How could you use this to filter lines of a
  given orientation?

#### *References:*

- {cite:t}`93808`

- {cite:t}`1307008`

### Bonus Exercise: Image filtering in harder data

- Use what you’ve learned to attempt to outline the cell *and* its
  nucleus in the image `Image_Processing/Cells/Nadia 20131122_RhoAMEFs_18kPa_001_w477_t01.tif`

  - What filter is appropriate here - an edge or a ridge filter?

- Use what you’ve learned to detect some of the red and green isolated
  axons in the image `Image_Processing/Neurons/arpy01_Chat_082_B_i_cropped.tif` while
  avoiding both the large mass of continuous red fluorescence near the
  injection site, and the autofluorescence of the cell bodies in green.
  (You will not need the blue DNA channel)
