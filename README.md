# SynthSeg

This repository contains code to train a Convolutional Neural Network to segment MRI scans of different contrasts and 
resolutions. The network is trained with synthetic scans obtained by sampling a Gaussian Mixture Model (GMM) conditioned
on label maps. Because the parameters of the GMM (means and variances) are sampled at each minibatch from prior 
distributions, the network is exposed to images yielding differences in contrast (depending on the GMM priors), and 
learns robust features. 

Aditionally, the variability in training input data is further extended by performing data augmentation during the 
generation process. This includes spatial deformation of the label maps, random right/left flipping, random cropping,
random blurring, bias field corruption, and gamma augmentation.

We provide here the generative model `labels_to_image_model`, along with a wrapper `brain_generator`, which allows to 
easily synthesise new images. This repository also contains functions to train a Unet with the synthetic scans, as well 
as functions for evaluation, validation (which must be done offline), and prediction.


----------------

### Requirements
 
This code relies on several external packages (already included in `\ext`):

- [lab2im](https://github.com/BBillot/lab2im): contains functions for data augmentation, and a simple version of 
 the generative model, on which we build to build `label-to_image_model`
 
- [neuron](https://github.com/adalca/neuron): contains functions for deforming, and resizing tensors, as well as 
functions to build the segmentation network [1,2].

- [pytool-lib](https://github.com/adalca/pytools-lib): library required by the *neuron* package.

All the other requirements are listed in requirements.txt. We list here the important dependencies:

- tensorflow-gpu 2.0
- tensorflow_probability 0.8
- keras > 2.0
- cuda 10.0 (required by tensorflow)
- cudnn 7.0
- nibabel
- numpy, scipy, sklearn, tqdm, pillow, matplotlib, ipython, ...


----------------

### Content

- [SynthSeg](SynthSeg): this is the main folder containing the generative model and training function:

  - [labels_to_image_model](SynthSeg/labels_to_image_model.py): contains the generative model `labels_to_image_model`.
  
  - [brain_generator](SynthSeg/brain_generator.py): contains the class `BrainGenerator`, which is a wrapper around 
  `labels_to_image_model`. New images can simply be generated by instantiating an object of this class, and call the 
  method `generate_image()`.
  
  - [model_inputs](SynthSeg/model_inputs.py): contains the function `build_model_inputs` that prepares the inputs of the
  generative model.
  
  - [training](SynthSeg/training.py): contains the function `training` to train the segmentation network. This function
  provides an example of how to integrate the labels_to_image_model in a broader GPU model. All training parameters are 
  explained there.
  
  - [metrics_model](SynthSeg/metrics_model.py): contains a keras model that implements the computation of a Soft Dice 
  loss function.
  
  - [predict](SynthSeg/predict.py): function to predict segmentations of new MRI scans. Can also be used for testing.
  
  - [evaluate](SynthSeg/evaluate.py): contains functions to evaluate the accuracy of segmentations with several metrics:
  `dice_evaluation`, `surface_distances`, and `compute_non_parametric_paired_test`.
  
  - [validate](SynthSeg/validate.py): contains `validate_training` to validate the models saved during training, as 
  validation on real images has to be done offline.
  
  - [supervised_training](SynthSeg/supervised_training.py): contains all the functions necessary to train a model on 
  real MRI scans.
  
  - [estimate_priors](SynthSeg/estimate_priors.py): contains functions to estimate the prior distributions of the GMM
  parameters.
  
- [scripts_SynthSeg](scripts_SynthSeg): usecase explaining how to use the `BrainGenerator` wrapper to easily 
generate images. This script uses uniform priors of wide range, thus yielding images of highly varying contrats (often 
unrealistic).

- [script_PVSeg](scripts_PVSeg) script explaining how to use the `BrainGenerator` in the case of specified prior
distributions for the GMM.

- [ext](ext): contains external packages, especially the *lab2im* package, and a modified version of *neuron*.
----------------

### Citation/Contact

If you use this code, please cite the following paper:

**A Learning Strategy for Contrast-agnostic MRI Segmentation** \
Benjamin Billot, Douglas Greve, Koen Van Leemput, Bruce Fischl, Juan Eugenio Iglesias*, Adrian V. Dalca* \
*contributed equally \
accepted for MIDL 2020 \
[ [arxiv](https://arxiv.org/abs/2003.01995) | [bibtex](bibtex.txt) ]

If you have any question regarding the usage of this code, or any suggestions to improve it you can contact me at:
benjamin.billot.18@ucl.ac.uk


----------------

### References

[1] Anatomical Priors in Convolutional Networks for Unsupervised Biomedical Segmentation
Adrian V. Dalca, John Guttag, Mert R. Sabuncu, 2018

[2] Unsupervised Data Imputation via Variational Inference of Deep Subspaces
Adrian V. Dalca, John Guttag, Mert R. Sabuncu, 2019
