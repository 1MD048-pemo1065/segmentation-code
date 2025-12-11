# Segmentation training

This repository contains a Jupyter notebook for training a U-Net model on a subset of the data from the Tran et al. research paper.

## Description

The U-Net implementation is a slightly modified version of the one described in the research paper by Visuña at al., available at https://doi.org/10.1016/j.engappai.2025.112503. The model is a simplified version of the original U-Net model, having just 4.4 million parameters. In their paper, they trained their model using manually and automatically annotated ground truth images, using time-lapse microscopy images as raw input to the model, similar to the ones used in the Tran et al. paper. For my implementation, I used the output segmentations from the Omnipose model as ground truth, meaning that the Omnipose model acts as a teacher model, while the U-Net model is the student model. The goal of the training is to distill the knowledge of the Omnipose model into the much smaller and simpler U-Net model.

## Prerequisites

The following Python libraries must be installed prior to running the notebook:

* `torch`
* `torchvision`
* `pillow`
* `numpy`
* `tqdm`
* `matplotlib`

These can also be installed by running the first cell in the notebook. There you must choose whether to install PyTorch with CUDA support or with CPU support.

The dataset files posted on Studium must be downloaded. Unzip the files into a single `dataset` folder. The folder hierarchy needs to be changed slightly, and should have the following structure:

```
dataset
   ├── HR\_REF\_masks
   │   ├── Pos101
   │   ├── Pos102
   │    ... 
   ├── HR\_REF\_raw\_data
       ├── Pos101
       ├── Pos102
        ... 
```

That is, the `PosXXX` folders must go udirectly under each experiment folder. For each `raw_data` folder there must be a folder with the same name, but with `raw_data` replaced with `masks`. The code will automatically load images from each experiment under the configured `dataset` folder.

## Running the notebook

Look for the following lines in the notebook and replace the parameters with values suitable for your environment:

```
EPOCHS = 250
DATASET_PATH = "/path/to/dataset"
```

On a fast GPU, using all data published in Studium, 250 epochs took a bit over 6 hours to complete, so it's probably a good idea to lower that a bit.

Once the values have been set, all cells can be executed sequentially (the first one for installing dependencies may not be needed).

After finishing training, the best model should have been saved, and a test segmentation can be performed on an image from the validation set. A training plot is also created.

## My results

I trained the U-Net model for 250 epochs using all data published to Studium. The dataset split was done based on the PosXXX folder name (folders ending in 09, 10, 19, 20 were chosen as validatiomn set).

The model achieved a Dice score of 95% on the validation set, and 96% on the training set, so overfitting seems to not have been an issue. Since the model is quite simple with just 4.4 M parameters, this may also have helped to reduce overfitting.

## Notes

Sometimes the training seemed to break down, and no progress was made at all. This instability has been attempted to be fixed by adding BatchNorms, but it has not been thoroughly tested, so if the training gets stuck on a Dice score of 0, restarting it has been the only option in my testing.
