## Charades Starter Code for Activity Classification and Localization

Contributor: Gunnar Atli Sigurdsson

* This code implements a "Asynchronous Temporal Fields for Action Recognition" in Torch
* This code extends the framework from github.com/gsig/charades-algorithms

*Update 8/11/17: Bugfixes added. Please download pretrained models again, and start models from scratch.*

Details of the algorithm can be found in:
```
@inproceedings{sigurdsson2017asynchronous,
author = {Gunnar A. Sigurdsson and Santosh Divvala and Ali Farhadi and Abhinav Gupta},
title = {Asynchronous Temporal Fields for Action Recognition},
booktitle={The IEEE Conference on Computer Vision and Pattern Recognition (CVPR)},
year={2017},
pdf = {http://arxiv.org/pdf/1612.06371.pdf},
code = {https://github.com/gsig/temporal-fields},
}
```

Combining the predictions (submission files) of those models using combine_rgb_flow.py
yields a final classification accuracy of 22.4% mAP (evalated with charades_v1_classify.m)


Evaluation Scripts for localization and classification are available at [allenai.org/plato/charades/](http://allenai.org/plato/charades/)

Submission files for temporal fields and baselines for classification and localization that are compatible with the official evaluation codes on [allenai.org/plato/charades/](http://allenai.org/plato/charades/) are available here: [charades_submission_files.zip](https://www.dropbox.com/s/aw55dauebl87sth/charades_submission_files.zip?dl=1). This might be helpful for comparing and contrasting different algorithms.

Baseline Codes for Activity Localization / Classification are available at [github.com/gsig/charades-algorithms](https://github.com/gsig/charades-algorithms)


## Technical Overview:
 
The code is organized such that to train a two-stream network. Two independed network are trained: One RGB network and one Flow network.
This code parses the training data into pairs of an image (or flow), and a label for a single activity class. The network is a VGG-16 network. For RGB it is pretrained on Image-Net, and for Flow it is pretrained on UCF101. The pretrained networks can be downloaded with the scripts in this directory.
For testing. The network uses a batch size of 25, scores all images, and pools the output to make a classfication prediction or uses all 25 outputs for localization. Better accuracy maybe optained by increasing this number and smoothing the output.

All outputs are stored in the cacheDir under checkpoints/. This includes epoch*.txt which is the classification output, and localize*.txt which is the localization output (note the you need to specify that you want this in the options).
Those output files can be combined after training with the python scripts in this directory.
All output files can be scored with the official MATLAB evaluation script provided with the Charades dataset.

Requirements:
* csvigo: luarocks install csvigo
* loadcaffe: luarocks install loadcaffe
* optnet: luarocks install optnet 

Optional requirements:
* Facebook Lua Libraries, for speedups and fb.debugger, a great debugger
Please refer to the original res-net codebase for more information.


## Steps to replicate the network on Charades:
 
1. Download the Charades Annotations (allenai.org/plato/charades/)
2. Download the Charades RGB and/or Flow frames (allenai.org/plato/charades/)
3. Download the Imagenet/UCF101 Pre-trained Image and Flow models using ./get_models.sh
4. Duplicate and edit one of the experiment files under exp/ with appropriate parameters. For additional parameters, see opts.lua
5. Run an experiment by calling dofile 'exp/tfields_rgb.lua' where tfields_rgb.lua is your experiment file
6. The checkpoints/logfiles/outputs are stored in your specified cache directory. 
7. Combine one RGB output file and one Flow output file with combine_rgb_flow.py to generate a submission file
8. Evaluate the submission file with the Charades_v1_classify.m or Charades_v1_localize.m evaluation scripts 
9. Build of the code, cite our papers, and say hi to us at CVPR.

Good luck!


## Pretrained networks:

While the RGB net can be trained in a day on a modern GPU, the flow net requires nontrivial IO and time to converge. For your convenience we provide RGB and Flow models already trained on Charades using exp/tfields_rgb.lua and exp/tfields_flow.lua

https://www.dropbox.com/s/hc14c2tzanye0cy/tfields_flow.t7?dl=1
https://www.dropbox.com/s/r2et0psu1931a1h/tfields_rgb.t7?dl=1

* The flow model was obtained after 83 epochs (epochSize=0.1)
* The flow model has a classification accuracy of 17.2% mAP (via charades_v1_classify.m)
* The rgb model was obtained after 10 epochs (epochSize=0.1)
* The rgb model has a classification accuracy of 18.3% mAP (via charades_v1_classify.m)

Combining the predictions (submission files) of those models using combine_rgb_flow.py
yields a final classification accuracy of 22.4% mAP (via charades_v1_classify.m)

To fine-tune those models, or run experiments, please see exp/rgbnet_resume.lua, exp/rgbnet_test.lua, exp/flownet_resume.lua, and exp/flownet_test.lua


