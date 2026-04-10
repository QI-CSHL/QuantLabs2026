# Glossary


```{margin} Looking for more definitions?
You can also check out the glossary for the [Bioimaging Guide](https://www.bioimagingguide.org/Glossary.html)!
```


```{glossary}
Anaconda
  Is a popular, open-source distribution of Python and R programming languages, providing a pre-configured environment with lots of tools and packages for data science, machine learning, and scientific computing (600+ packages, ~10GB). 

Conda
  Is an open-source package and environment manager. It installs software packages and manages isolated environments on your machine, preventing dependency conflicts between projects.

Convolution
   A mathematical operation on two functions that produces a third function, representing the amount of overlap as one function is shifted over another. The term refers to both the resulting function and the process of computiong it. 
   In image analysis, convolution is the process by which a digital image is modified using a kernel to obtain a filtered image.
   In microscopy, it usually refers to the process by which the shape of an object observed through a microscope is distorted by the PSF to generate the observed image.

Environment
  (Particularly, a *software environment*) Is the context in which your code runs, including the configuration settings, system resources, variables, and tools that shape how the program behaves. Because different softwares may have different (and many times mutually incompatible) context requirements, having distinct, independent environments allows you to run these different programs on the same computer.

Mask
  A binary image in which background pixels are black (pixel value is 0 in an 8-bit scale) and the foreground pixels are white (pixel value is 255 in an 8-bit scale).

Mamba
  Is a fast, robust, and open-source cross-platform package manager designed to solve dependency issues and install Python packages fast. It serves as a drop-in replacement for {term}`conda`, meaning that most `conda` commands work, by just replacing `conda` with `mamba` (i.e. using `mamba install numpy` instead of `conda install numpy`).

Miniconda
  Is a lightweight version of {term}`Anaconda` that includes only {term}`conda`, Python, and their dependencies (130+ packages, ~1GB).

Pixi
  Is a fast, cross-platform and reproducible package management tool built on top of the {term}`conda` ecosystem. It uses a `pixi.lock` file to lock dependency versions, ensuring a reproducible environment across machines.

Rotation
    Shifting of the image pixels' positions *around* a specific vector in the X,Y,Z space. 

Segmentation
  Method of dividing an image into multiple parts or regions. There are three different types of segmentation. 
    - **Semantic segmentation**, where all parts of an image are part of a class, common in cell biology will be detecting cells and background on an image.
    - **Instance segmentation**, the segmentation is object based, not just detecting were the cells are but diving each cell as a separate object.
    - **Panoptic Segmentation**, it can be defined as a combination of the prior two, because it identifies the object but also classifies them. An example in biology might be detecting all the cells on an image and classifying them as dividing vs not.

Threshold
  A number that divides a distribution of values of a into distinct categories. In image analysis it is usually referred to a *intensity threshold*, which divides all pixel intensity values into *foreground*  and *background*. 
  In a typical fluorescence microscopy image, where background is dark and the structures of interest ar bright, the foreground pixels will be those with intensity values **above** the threshold, while the background pixels will be those with intensity values **below** it.
  You can choose the value of the threshold manually but there are many automatic ways of calculating it based on the pixel intensity distribution of the image (e.g. the intensity histogram). Unlike manual thresholding, given the same image, all of these authomatic methods will always produce the same threshold value, which makes them reproducible. Some examples of automatic thresholdings include Otsu, Median, MinimumCrossEntropy, but there are MANY others.
  The advantage of using an automatic algorithm to define the threshold is that it can somehow adapt to changes between images so that the foreground/background sepparation is similar despite subtle variations in the intensity levels.

Translation
  Shifting of the image pixels' positions *along* a specific vector in the X,Y,Z space. 

UV
  Is a Python package manager, similar to Anaconda, although generally considered to be much faster. It allows to create and manage environment, and install tools and dependencies.

Watershed
  Image processing transformation applied to a grayscale image. The name refers metaphorically to a geological watershed, which separates adjacent drainage basins. The watershed transformation treats the image it operates upon like a topographic map, with the brightness of each point representing its elevation. It is primarily used to separate different objects in an image by finding the lines that run along the ridges or valeys.
  This method can be applied directly on the grascale image intensities or on a distance transform based on the thresholded mask and local intensity maxima/minima.


```

<!-- 
Terms that aren't in the book yet but we know we want to add (anything you type here won't show up in the built book)

Should we always use voxel instead of pixel? for the definitions whenever they can be applied to both?


--->
