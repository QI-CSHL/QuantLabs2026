# Deep Learning for Microscopy Image Analysis

*Lab authors: Damian Dalle Nogare, Florian Jug, and Beth Cimini* . 

<small>This file last updated 2026-04-15.</small>

---

## Learning Objectives

- Appreciate how Neural Networks are trained
- Segmentation with Cellpose{cite}`Stringer2024-gd`
- Learn how to make your own `conda` environment and install packages from `pip`
- Denoising with CAREamics in python
- Bonus: Try in-browser classification with Piximi

**Lab Data** in [this folder](https://drive.google.com/drive/folders/14OWZZk2FkvKbqHotQDcfjJ2tG3LSQzej?usp=sharing) (DL4MIA)

Remember to **unzip** the data folder after downloading.

## **Overview**

Neural networks can do useful things. Their deployment within
user-friendly tools is, unfortunately, lagging behind. Hence, methods we
would like to apply to our data are not always available in Fiji or ilastik{cite}`Berg2019-no`
quite yet (or only with a fair amount of command line work and difficulty). The latest methods can only be used by the ones “brave”
enough to expose themselves to some amount of computer source code...

Today we will all be brave! 😊

We're very much looking forward to hearing about your successes and
struggles tomorrow during the Q&A session. Now, please take a seat, open
a browser, and buckle up!

## **Exercise 1: Remind yourself about what we’ve heard in the lecture**

1.  Visit
    [this neural network playground](https://playground.tensorflow.org)
    and look around. What terms did you hear before, what is new, and
    what is confusing?

2.  Please try to:

    a.  On the classification example that looks like a tiny
        checkerboard, try to get a test loss of 0.001 or less.  
        <img src="images/dl/image11.png" style="width:0.71893in;height:0.70313in" /><img src="images/dl/image4.png" style="width:1.13021in;height:0.68795in" />

    b.  For the spiral-shaped classification example, try to find a
        network architecture with the smallest amount of nodes (neurons)
        that will drop below 0.01 (1%) test error.  
        <img src="images/dl/image13.png" style="width:0.73438in;height:0.73438in" />
        <img src="images/dl/image8.png" style="width:2.33407in;height:0.73431in" />

    c.  Now switch from ‘Classification’ to ‘Regression’. What is going
        on here? Can you figure out how regression is different from
        classification?  
        <img src="images/dl/image6.png" style="width:1.47396in;height:0.74114in" />

    d.  Some other things to try if you feel it…

    - Add some noise to your data. What changes? Why?

    - Try to find a setup that overfits. How do you identify overfitting?

      <img src="images/dl/image14.png" style="width:2.28646in;height:0.75899in" />

    e.  All the important terms and concepts wrt. to training and
        validation are somewhere on this one page. Check if there is
        anything that makes no sense to you and ask us

## **Exercise 2: Play with Cellpose** 

```{note}
Once back home, you will need [this installation instructions](https://cellpose.readthedocs.io/en/latest/installation.html) to get started with Cellpose.
```

```{margin} Want to learn more about working with Cellpose?
Check out the [documentation](https://cellpose.readthedocs.io/en/latest/), or check them out on [the image.sc forum!](https://forum.image.sc/tag/Cellpose)
```

Here, at QI, we have taken this annoying step for you already. Hence,
you will find Cellpose pre-installed on the lab computers. To start it,
click the `cellpose.bat` file on the desktop.

You should now see something like
this:<img src="images/dl/cellpose_gui.png" style="width:6in" />

Open the file `001_img.tif` by dragging it onto the open window.
You can find this file in the `DL4MIA/easy` folder in the [Lab Data share](https://tinyurl.com/QIAnalysisLabData).

Like we did in the 3D lab yesterday, we need to tell cellpose (roughly) how large our objects are (you can do
so via the `cell diameter`) field. 

How might we estimate this? Keep in
mind that this diameter must be reported in pixels.

You can now segment this image by selecting one of the pre-trained
models from within the `dataset-specific models` box. Try segmenting this image using the `cyto2` model. How good are the results?

```{tip}
You can toggle the visibility of segmentation masks on and off
by hitting ‘**x**’ on your keyboard. Similarly, you can toggle cell
outlines with the keyboard shortcut ‘**z**’. Alternatively you can do so
in the `Drawing` box on the menu to the left.
```

How well did Cellpose segment your image? Where (if anywhere) did it
fail? Try some of the different dataset-specific models. Do any of these
work better? Worse? Why might that be?

Let’s now try some more challenging data. From
the `DL4MIA` folder in the [lab data share](https://tinyurl.com/QIAnalysisLabData), download the entire folder
named `hard`, and place it somewhere convenient (like the Desktop).

From within this folder, open the `test` folder and drag the file
`test_img.tif` to Cellpose to open it. Try segmenting it as well as
possible. Can you find setting and a model that work perfectly?

Spoiler, none of the models are perfectly suited to this data, but we
can iteratively retrain a custom model that will work better on this specific kind of data from within the Cellpose GUI…

Interested? Ok, let’s do it! 🙂

### Human-in-the-loop retraining

In the `hard` folder you downloaded earlier you will find a folder called
`train`. In this folder you will find a number of images. Open the image
`104_img.tif` in Cellpose. 

Note that we are **not** going to train a model
from scratch, instead we are going to finetune one of the existing
models (ideally starting from one that does a pretty good job already).

```{margin}
Human-in-the-loop training scheme in the Cellpose GUI:
* Segment an image using a model of your choice
* Correct segmentation errors manually
* Use the corrected image to retrain the original model
* Apply the retrained model on another (similar) image
* Correct segmentation erros (hopefully there are fewer this time!)
* Further retrain your model 
* Repeat this steps until the results are ['good enough'](https://carpenter-singh-lab.broadinstitute.org/blog/when-to-say-good-enough) for your analysis needs.
* Test your model on unseen data to evaluate generalizability!
```

Choose the model that you think gave you the best segmentations in the
previous part of this exercise and apply it to this image.
Because we want to re-train our own model, we need to provide it with `ground truth data`. In this case, this means showing it what kind of segmentation we would like to see, given the current image.
So, we need to manually curate the automatic segmentation provided by the model.

You can see now how starting from an already kind-of-good model will make the manual correction easier and faster.

You can correct the segmentation errors by
removing, adding or redrawing some of the segmentation masks. The corrected image can then
be used to further finetune (retrain) the model.

To correct errors in the segmentation:

1)  Delete a mask by holding down the ‘control’ key and clicking on it.

and/or

2)  Draw a new mask by right-clicking anywhere in the image and tracing
    an outline, ending where you began to draw.

Try correcting some of the segmentations. It might be easier if you
switch between masks and outlines (use the ‘z’ and ‘x’ keys as explained before).

Once you are happy with your corrected masks, take a look in the folder
containing all of the training images. You will notice there is a new
file there, called ‘hard\train\104_img_seg.npy’. This contains your
corrected segmentation and will become a new bit of ground truth used
during finetuning the model. But… how do you start this finetuning step?

In the Cellpose menu bar, go to `models →  
train new model with images and masks in folder`.

You should see a window like this one:

<img src="images/dl/cellpose_training_gui.png" style="width:5in" />

First, we need to select which initial model to use. In the screenshot
above, we are retraining the `cyto` model (but of course you may choose
to retrain any available model). You can, and should, give your new
model a name. You can also see which (corrected) images you are going to
retrain the model on under ‘filenames’, and the number of masks that
will be used for retraining in that image. Click OK whenever you are
ready to retrain and finetune the selected model!

During training you should see something like the following if you check the 
console (where you started Cellpose from). What is going on here? Remember 
back the lecture when we discussed training steps and epochs.
<img src="images/dl/cellpose_training_output.png" style="width:6in" />

Once done, Cellpose will automatically open the next image in the folder and use the freshly finetuned model to segment it. 

You can now repeat this process as often as needed. Cellpose will in each iteration
finetune the same original model, but will do so with an ever increasing
number of user labeled masks (the ones you have created). Eventually you
will either loose hope or find that Cellpose’s prediction become good
enough for you to be 😻!

Once you are happy with the results you are getting, apply your final
model to the test data we have segmented at the start of the exercise
(importantly: your model has not previously seen this image during
finetuning! Why is this important again?). Is the result better than
with the initial model you started with?


````{tip}
Want to use Cellpose when you get home, but having trouble with the `conda` installation? You have (at least) a couple of potential options!

```{note}
As of April of 2026, BAND is still using Cellpose 3, which is very good for most problems but struggles on certain cases (such as highly variable object sizes) more than Cellpose 4.
```

- EMBL has the Bioimage ANalysis Desktop (BAND) program, which allows you to check out virtual machines in the cloud. You simply visit a website, tell them the resources you need, and get a machine with [>20 helpful image analysis tools pre-installed](https://bandv1.denbi.uni-tuebingen.de/#/eosc-landingpage). 
  - **Upsides**: No installation, everything is correctly configured and ready to go, simulataneous access to lots of tools at once, you can ask for machines with GPUs
  - **Downsides**: They have limited capacity and sometimes machines aren't available due to EMBL courses. You have to upload your data to their servers, and download your results from them. 
- Cellpose can be used with CellProfiler, both in Python if you have both programs `pip` or `conda` installed, but [CellProfiler also offers a way to use a pre-built version using Docker or Podman](https://plugins.cellprofiler.org/using_plugins.html?installing-plugins-with-dependencies-using-cellprofiler-from-source#using-docker-to-bypass-installation-requirements) {cite}`Weisbart2023-kc`
  - **Upsides**: You can use Cellpose models without ever touching your terminal, and while keeping your data local - you only need to install CellProfiler and Docker (or Podman) `.app` or `.exe` files from their respective websites, download [the plugin](https://github.com/CellProfiler/CellProfiler-plugins/blob/master/active_plugins/runcellpose.py), and then point CellProfiler at the location of the downloaded file. 
  - **Downsides**: this only lets you run pre-trained Cellpose models, not train your own. Running Cellpose in CellProfiler via container is also MUCH slower than running it when installed via Python (though it will mostly be compute time, rather than human time, once you're in analysis mode and running all your images unsupervised). You're limited to the hardware you have locally.

````

## **Exercise 3: Denoising with CAREamics**

## Installing CAREamics


If you are familiar with `conda` and `pip`, the installation instructions can be found [here](https://careamics.github.io/0.1/installation/). If you are comfortable with what is there, go ahead and follow the instructions. Otherwise we will go through each step here. Keep in mind that the CAREamics instructions use `mamba`, whereas we will use `conda`. If that doesn't make sense to you, then read on to the next section!

In QI, as an alternative to conda/mamba, we have been using a new python package manager, called `UV`. UV can create ephemeral python environments very rapidly using the `uvx` command. The icons to launch cellpose and napari in previous exercises have used this behind the scenes. 

In the data folder, there is a file called `careamics.ipynb`. Place this is a reasonable place on the computer (say `C:\Users\Admin\Documents\Careamics` or a similar place. Then right-click on that folder and select `open in terminal`.

In the terminal that opens, type

`uvx juv run --with careamics[examples]==0.1.0 careamics.ipynb`

This should install an environment that contains careamics, and then launch the Jupyter Notebook.


## Using CAREamics in a Jupyter Notebook

Instructions on how to run CAREamics are contained within this notebook. Follow along with the notebook, but try to understand what is going on at each step! If you are confused, ask! If some problem arises, ask! If all works and you are done -- brag a little! 😸

Keep in mind that deep learning training is slow, and it might take some time (~20-30 minutes to train your network).


## **Bonus Exercise: Classifying images in the browser with Piximi**

[Piximi](piximi.app) {cite}`Moser2024-vt` is a web app currently in development for training and running deep learning models in your web browser. Under most circumstances (with Cellpose as the major exception), all compute happens locally - you load your images into your web browser, but they are NOT sent to the internet, they stay locally on your machine. While this has some disadvantages (namely, that you're limited to the resources on your own machine), this means you get the benefit of web applications (namely, no need to install anything) but don't have to worry about upload times or where in the cloud your data is stored.

```{margin} Want to learn more about working with Piximi?
Check out the [documentation](https://documentation.piximi.app/intro.html), or check them out on [the image.sc forum!](https://forum.image.sc/tag/Piximi)
```

Piximi includes 4 major functionalities. Three major ones you're used to thinking about are:
* **Segmentation and Object Detection**
* **Classification**, and
* **Measurement** 

In order to train deep learning models for object detection and segmentation, it also includes a 4th major component - an **Annotation** tool. 

```{margin}
Since Piximi is a web tool, new versions can go live all the time - for example, we plan to roll out tracking later in 2026. You can keep visiting [piximi.app](https://piximi.app/) to see what's available!
```

### Train a 10-class classification model using MNIST

Piximi is designed for biologists but can be used on non-biological images as well. Here, we'll use 1,000 handwritten digits from the classic [MNIST dataset](https://en.wikipedia.org/wiki/MNIST_database) {cite}`726791` , which consists of cropped images of digits from old census data and high school students.

#### Train a classifier
- Go to piximi.app
- Tell Piximi you want to open an example project 
- Select MNIST
<img src="images/dl/piximi_mnist.png" height="180px" />
- Scroll through the images - you'll see that most are categorized as a particular digit, but about 60 have been intentionally left un-categorized for testing purposes.
  - Are there any categorizations you aren't sure about or disagree with?

```{tip}
You can try to use the filter tool <img src="images/dl/filter.png" height="25px" />
on the right to inspect only one or certain categories at a time.
```

- Tell Piximi you want to fit a classifier for these images

<img src="images/dl/piximi_fit.png" height="180px" />

- You will now see Piximi's training dialog; you can choose to tune some of the hyperparameters before training (though we've chosen here reasonable defaults that should work well). Otherwise, hit <img src="images/dl/piximi_fit_classifer.png" height="40px" /> to train.
- After an initialization step, you will see a performance chart that looks like the below, as well as a loss graph. You can keep hitting `Fit Classifier` to keep adding more epochs of training.
<img src="images/dl/piximi_training_performance.png" height="180px" />
  - Are you overfitting? How can you tell?

#### Evaluate your classifier

Once you're satisfied with your training (either because it's great or because you're satisfied that it has plateaued), close the training dialog. Hit the `Evaluate Model` button to check your confusion matrix. 

<img src="images/dl/piximi_evaluate.png" height="90px" />

<img src="images/dl/piximi_confusion.png" height="180px" />

```{admonition} Questions for you
What patterns of mistakes do you notice? Are they the kinds of mistakes you would expect?
```

A confusion matrix helps you figure out patterns of mistakes, but it can only tell you about the performance of your model on data for which you've already provided the answer - it can't tell you about performance in your unlabeled data. It is _critical_ then to always apply your classifier to new, unseen data to see how it performs.

```{important}
It is quadruple-extra critical when only a small fraction of your data is labeled, which is NOT true here but is often true in biological situations and you may hope will be true in your future work (after all, if you have to hand label almost all of your data, then what's the point of training a model?)
```
- Hit the `Predict Model` button to apply the model to the unlabeled data

<img src="images/dl/piximi_predict.png" height="180px" />

- Evaluate the performance of the predictions - you may find that hitting one or both of these buttons helps you do that 

<img src="images/dl/piximi_hide_labeled.png" height="40px" /> 

<img src="images/dl/piximi_hide_other.png" height="90px" />

#### Fix (some?) mistakes

If and when (when), you find some errors in the predictions, you can fix them by assigning them a new category.

<img src="images/dl/piximi_recategorize_errors.png" height="180px" />

Depending on why you're using machine learning, you might choose to fix all the wrong images at this stage, or only some:

**_Is your goal to just get the classifications right and then use them for something else, and most of them have already been correctly classified?_** 
  - In that case, there's no harm in just fixing the few mistakes and then moving on to other downstream quantification steps (coming soon!). 
  - If this is your goal but there are a lot of mistakes, you might not choose to fix all of them at this stage, but just fix a subset and then try to train again so you can get to a point where the errors are at a small enough level that you CAN do final data cleaning by hand

**_Is your goal to create a robust, reusable classifier to use on other sets or in other contexts?_**
  - In that case, you might want to fix only a subset of the mistakes before retraining, so you can get a sense of if your model performance is improving.
  - If retraining, once you've done your chosen recategorizations, clear predictions (<img src="images/dl/piximi_clear_predictions.png" height="40px" />) and then hit fit again.

  ```{important} 
  If this is indeed your goal, you need to have some unseen **test** data somewhere else that you are not tuning on here! Once you've run any version of your model, at any stage, on unseen data, that data is now "seen data", and can't be used as a test set anymore. How you plan your data splits (and how much, and which, data you keep locked away as test set(s)) is critical to any kind of machine learning research
  ```

#### Save things for later

Reproducible science matters! 

You can therefore save your Piximi project file for later, as well as save your model for later use. You might find the former handy if you want to add more data later, and/or you just want to confer with someone else (including a paper reviewer, or future you) about how difficult data points were handled.

<img src="images/dl/piximi_save_project.png" height="180px" />

<img src="images/dl/piximi_save_model.png" height="180px" />

### Train a 3-class classification model on U2OS cells

```{note}
Sometimes this data set takes a long time to load, sometimes it doesn't! We're not sure why. Feel free to skip it (or move onto the next bonus and then come back to this one, leaving the browser window open in the meantime) if it's taking a long time and being annoying.
```

This data set is in some ways more challenging, but also shows a more biologically relevant classification scenario, alongside the ability to do more human-in-the-loop retraining since, unlike MNIST, the majority of the data is NOT already categorized for you.

- Optionally refresh Piximi, and then load the U2OS-cells cytoplasm crops example dataset (Open \> Project \> Example Project \> Human U2OS-cells cytoplasm crops)

<img src="images/dl/piximi_bbbc013.png" height="180px" />

```{admonition} Optional: fix how the images look
You need not do this, since it can be a bit slow, but it is necessary if you want to assess the performance of the no-GFP class (and will make things much easier if you are red-green colorblind).

Piximi's current defaults are to load two-channel images as red and green, and to rescale each image min-max individually. While we work to fix those bugs, here's how you can manually set the colors to something better (and more uniform)
- Hit Ctrl+A to select all cells
- Hit "Annotate" to open the annotation viewer
<img src="images/dl/piximi_annotate.png" height="180px" />
- Open the channel adjustment bar on the right (<img src="images/dl/Piximi_channel_tool.png" height="25px" />), and change color mapping to better lookup tables and values. Hit "Apply to all images open in the annotator" when you're done (and then wait a couple of minutes)
<img src="images/dl/piximi_channels.png" height="180px" />
```

- Some of the cells have already been labeled, what color represents the eGFP signal?

- Use human-in-the-loop classification (same procedure as in the previous exercise) to train a high-performing 3 class classifier.
How high can you get the evaluation metrics? How many rounds and how many corrected classifications does it take you to get there?


## **Bonus Exercise**

Install the promptable segmentation tool nnInteractive. Promptable segmentation is a type of segentation where we give the computer a "prompt" about what we want to segment. This might be a mouse click, or a bounding box, and then the computer will segment whatever it thinks best represents your prompt. This is useful, for example, for generating human-validated ground truth for future neural network training.

We will use an environment `yaml` file to install this. A `yaml` file contains all the instructions for conda to install a python environment. In the lab data folder, you will find a file called `nnInteracive.yaml`. Download that, and place it somewhere on the computer. Right click on the file and select `open in terminal`.

To install the environment, type into this terminal

`conda env create -f nnInteractive.yaml`

Once the environment is ready, activate the environment by typing `conda activate nnInteractive`. Then launch napari by typing `napari`. You should see the napari window launch.

nnInteractive can be found in the `plugins` menu under `nnInteractive (nnInteractive)`. Launch it by selecting this option. You should eventually see the plugin on the right side of the screen.

<img src="images/dl/nnInteractive.png" height="180px" />


Load some data that we would like to segment (any data will do). If you have multiple objects that you want to segment seperately, you will want to chekc the `instance aggregation` checkbox, as seen below.

<img src="images/dl/nnInteractive_instance_aggregation.png" height="60px" />


Now select one of the prompt types (I would start with `points`, see image). Try selecting the type of prompt, and start clicking on objects. Once an object is segmented, you can commit it to the labels layer uisng the `next object` button. Try different prompts or segmenting different objects. Which types work better? What type of segmentation are we doing here (instance or semantic?).


<img src="images/dl/nnInteractive_highlight.png" height="180px" />



When you're done, export your segmentation. How does this compare to other approaches (if you have the same data segmented already)? What are the pros and cons of this style of segmentation?

## **Bonus exercise**

### Interfacing CellProfiler with Cellpose via plugins and containers

#### Learning Objectives

- Learn how to download CellProfiler plugins from GitHub and set CellProfiler to use them
- Learn more about how to use software containers (such as Docker) to help CellProfiler access other tools

---

#### **Setup steps to do first**

1. **[WE HAVE ALREADY DONE THIS FIRST STEP FOR YOU, as these are large files (5-10 GB) and bandwidth is often limited in a workshop setting.]** Download at one of the RunCellpose containers from Dockerhub.

````{hint} How did we download the Docker container?
<details>
In Docker Desktop or Podman Desktop you can search for containers in the top search bar (see below).
Make sure you select a tag (version) that is supported by the plugin you are using and then select "Pull".
We recommend `cellprofiler/runcellpose_with_pretrained:4.0.6` for Cellpose.

If they are not appearing in search, you can instead pull them from the terminal with the command `docker pull CONTAINER` or `podman pull CONTAINER`, replacing `CONTAINER` with the container name and tag, e.g. `docker pull cellprofiler/runcellpose_with_pretrained:4.0.6`

```{figure} images/dl/DockerSearch.png
:width: 400
:align: center

Search in Docker Desktop for your desired container
```
The download will also happen automatically the first time you call a given Docker from CellProfiler (i.e. run a CellProfiler pipeline that uses the Docker).

</details>
````

2. CellProfiler has a number of plugins that act like modules within a pipeline, but they don't come bundled with the main installation. 

```{margin}
Read more about them in [CellProfiler plugins documentation](plugins.cellprofiler.org) to learn more.
```

>Plugins advance the capabilities of CellProfiler but are not officially supported in the same way as modules. A module may be in CellProfiler-plugins instead of CellProfiler itself because:
>- it is under active development
>- it has a niche audience
>- it is not documented to CellProfiler’s standards
>- it only works with certain versions of CellProfiler
>- it requires extra libraries or other dependencies we are unable or unwilling to require for CellProfiler
>- it has been contributed by a community member

To download the plugins that we'll use today, either:

2a. Visit the [CellProfiler plugins repository](https://github.com/CellProfiler/CellProfiler-plugins) and then download and unzip all the plugins by hitting the green `Clone` button and then `Download Zip`

**OR** 

2b. Clone (i.e. download whole) the Cellprofiler-plugins repository.
In your terminal type `git clone https://github.com/CellProfiler/CellProfiler-plugins.git`. 

**OR** 

2c.Alternatively, download just the [RunCellpose plugin](https://github.com/CellProfiler/CellProfiler-plugins/blob/master/active_plugins/runcellpose.py) by selecting the "Download raw file" button.

```{figure} images/dl/GitHubDownloadButton.png
:width: 400
:align: center

GitHub's "Download Raw Files" button
```

```{tip}
We strongly recommend making a dedicated folder to store your CellProfiler plugins, as loading can be slow if there are a lot of other miscellaneous files around (such if they sit in the Downloads folder, for example).
```

3. In CellProfiler's `File -> Preferences` menu (`CellProfiler -> Preferences` in Mac), set the `CellProfiler plugins directory` to the folder containing the plugins (make sure you use the `active_plugins` subfolder).

4. Close and reopen CellProfiler to load the plugins.

````{tip} **Software containers and Docker Desktop**

Sometimes, like in this exercise, we want to access software that we either don't want to install locally because it's painful if you're not pretty comfortable in Python (Cellpose) or that we CAN install locally but may not play nicely with other tools' multiprocessing setup (ilastik). 

Computer scientists will often use software **containers** to ship tools or data that are hard to install - [here](https://journals.sagepub.com/doi/10.1177/25152459211017853) is a good introduction and overview for non-computer scientists. You can think of containers as "a pre-configured operating system in a box". Because they come to you pre-configured, installation of any software happens once-and-only-once (by the creator of the container, not by you), and it should stay working for long after ie the latest Mac upgrade breaks a certain older software installation. Groups like [biocontainers](https://biocontainers.pro/) have already containerized many of the tools you know and love. There are a number of types of software containers, but one of the most common is called a **Docker** container; many containers made by Docker, which are therefore sometimes referred to as Docker containers, can nevertheless be run in other container programs, like Singularity and Podman. 

Most life scientists aren't aware of or don't use containers, especially because typical usage involves accessing them via your terminal. But Docker doesn't have to mean terminal! There are [containers that spit out interactive websites for you to use](https://github.com/COBA-NIH/docker_gradio_demo), and CellProfiler has plugins that are specifically set up to call other tools that live (and are executed) inside containers. 

To do this, CellProfiler needs to have the infrastructure for Docker containers up and running, which you can give it by installing a free program like [**Docker Desktop**](https://www.docker.com/products/docker-desktop/) or [**Podman Desktop**](https://podman-desktop.io/downloads). **[WE HAVE ALREADY DONE THIS FOR YOU IN THE LAB COMPUTERS!]** You don't need to make an account to use it, but you probably will need to reboot your computer after installation is complete. Then, whenever you want to use a container-calling plugin in CellProfiler, you simply must make sure that one of these programs is open and running, CellProfiler will take care of everything else!

```{figure} images/dl/DockerDesktop.png
:width: 700
:align: center

The Docker Desktop interface for Mac. Containers can be searched for in the top search bar!
```

```{warning}
Anytime you're installing a program that can run other programs, you must of course be careful. Containers give you access to a huge variety of tools that might otherwise be hard to install (or install together), but **exercise caution in only running containers you recognize or trust**!
```
````

#### **Running Cellpose from a container**

RunCellpose is by far Cellprofiler's most popular plugin, simply because **a)** Cellpose is awesome! and **b)** installing Python packages when you aren't very computationally comfortable isn't. 

You can use the plugin in either of two modes - using a local `pixi`, `conda` or `python` installation that contains both CellProfiler and Cellpose, **OR** using Docker. 

```{margin}
The run time with Docker is substantially slower (about a minute more per image, in our testing), but if installation would take you a long time and be frustrating, in this sense you can "trade" your personal hands-on frustration time for time where CellProfiler is running on your computer (but you aren't there). For many people, this is a good trade!
```

##### Start Docker Desktop
1. Start Docker Desktop. You don't need to do anything else here, just leave it running on the background.

##### Load the pipeline and evaluate segmentation
1. Open Cellprofiler
2. Drag `bonus_3_cellpose.cppipe` into the pipeline panel.
3. Drag the `images_Illum-corrected` subfolder from the main exercise into the Images module
4. Put CellProfiler into **TestMode** <img src="images/dl/StartTestMode.png" width="120"/> 
5. You will need to `Select Cellpose version` and `Select Cellpose Docker image`. (e.g. you must select Cellpose version **3** for the Docker `runcellpose_with_pretrained:3.1.1.2` to be an option.)
6. Open the eye icons <img src="images/dl/EyeOpen.png" width="50"/> next to RunCellpose and OverlayOutlines, and then hit <img src="images/dl/Run.png" width="120"/>
  - You may wish to put a pause next to SaveImages, or uncheck it, to keep it from saving images, but that's up to you

```{note}
If you didn't already pull the container in the Docker Desktop section above, the very first time you hit the RunCellpose module, it will need to download a ~13GB file, which may be slow depending on your connection. You only need to do this step once however!
```

```{figure} images/dl/RunCellpose.png
:width: 700
:align: center

The output of the RunCellpose module
```

4. This pipeline also performs classical instance segmentation using `IdentifyPrimaryObjects` (it has already been set for these images, but you're welcome to tune it further if you want!). Using `OverlayOutlines` and/or the `WorkspaceViewer` (lower left of the screen), evaluate segmentation on a few images. Where is CellProfiler doing better, and where is Cellpose doing better?
5. Use the <img src="images/dl/Info.png" width="35"> button to learn more about the different parameters you can pass to Cellpose (we don't offer all of them, but many!) - how does tweaking these affect your output? How about changing the model you're using, and/or the image you're segmenting?

6. You can also try to train a custom Cellpose model and use it in Cellprofiler, just change the `Detection mode` to `custom` and select the location of your model file.

```{margin} Want to know more about CellProfliler plugins and modules?
- Read the [CellProfiler-plugins paper](https://pubmed.ncbi.nlm.nih.gov/37690102/)
- Watch this [video](https://www.youtube.com/watch?v=fgF_YueM1b8) to learn how to write a module
```