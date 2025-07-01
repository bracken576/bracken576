# What is nnU-Net?

**nnU-Net (no-new-Net)** is a self-configuring deep learning framework designed for **medical image segmentation**. It automatically adapts its architecture, preprocessing, training, and postprocessing steps to any given dataset, eliminating the need for manual customization.

---

### What It Does
- Automatically configures U-Net-based architectures (2D, 3D, or 3D cascade)
- Handles the full pipeline:
  - **Preprocessing** (e.g., normalization, resampling)
  - **Architecture selection** and **training strategy**
  - **Postprocessing**
- Achieves **state-of-the-art segmentation** across diverse medical imaging datasets

---

### Intended Users
- **Medical imaging researchers**
- **Clinicians** working with radiological data
- Anyone needing medical image segmentation, including those with **limited deep learning expertise**

---
---

## Pipeline Breakdown

### **Here's a deeper look at each stage:**

#### Preprocessing
- **Resampling**: All input images are resampled to a consistent voxel (volumetric pixel) spacing, ensuring uniform scale across images.
- **Normalization**:
  - **MRI**: Z-score normalization (mean 0, std 1)
  - **CT**: Clipping to percentile ranges followed by min-max scaling
- **Cropping**: Automatic foreground cropping reduces memory usage and focuses the model on relevant regions.
- **Augmentations include**:  random rotations, random scaling, random elastic deformations, gamma correction augmentation and mirroring.

#### Architecture Selection & Training Strategy
- **Adaptive U-Net variants**:
  - Chooses between 2D U-Net, 3D U-Net, or 3D U-Net Cascade based on dataset properties. You can also specify one of the architectures if you already know which one is best for your data.
- **Dynamic architecture configuration**:
  - Patch size, number of pooling layers, and convolutional kernel sizes are automatically tuned.
- **Training**:
  - Uses a standardized training pipeline (e.g., SGD with Nesterov momentum, learning rate scheduling)
  - Implements loss functions suited for class imbalance (e.g., Dice + Cross-Entropy loss)

#### Postprocessing
- **Connected component analysis**:
  - Removes small, isolated false positives from the segmentation output, as long as the ground truth supports it. Meaning that there is always one continuous trace within the segmentation rather than multiple traces.
- **Ensembling**:
  - Combines predictions from models trained with different configurations or folds to improve robustness.

---

### Comparison

I have worked with a U-Net structure before and done different segmentation models. So, I'm going to compare some elbow MRI images that I have segmented capsule for before and compare it with the nnU-Net results in a saggital view.

#### My 3-D U-Net
![](images\mine_compar_6314_gif.gif)

#### The nnU-Net
![](images\6314_shorter_looped.gif)

As you can see above the nnU-Net framework does a lot better than mine and for good reason, it has been trained on multiple datasets and has a lot better cleaning in the postprocessing than mine. However, I still don't feel bad about how mine did with only training on 18 images.

### Intended Use

As I have studied nnU-Net and used it through these past couple months I have received insight into its intended use. It is meant for non-technical research. It's supposed to get quick results for biomedical researchers so that they can continue with their intended research rather than waste time fine tuning a model. It is really nice to have when you have a small data set to train and validate on. 

This has also allowed me to see some flaws in how it is built. Since the intended use is for non-technical researchers you would think that it would be easy to set up and be simple to get into training and testing. While it is more simpler than a traditional setup I think that it still has room for improvement, since it requires the user to set up a json file containing training paths and other information needed for pre-processing and training, it also uses a lot of bash commands and has a default of 5000 epochs for training. So, my fix is to just create a package that will allow for an increased ease of use for reasearchers. 

### Conclusion

I may not be able to publish a technical paper on a new machine learning framework by using nnU-Net but it has been really nice to look at it for ideas. I had no idea about voxel spacing normalization or that you could remove isolated segmentations and just keep the main tracing. Looking through the research paper and through the techniques used in the making of nnU-Net has allowed me to take away tricks that I can use in different models. It has also made me look into making a package and publishing a package, you can look at my other post on that here : [Making Python Packages (2025)](making_python_package.md)

