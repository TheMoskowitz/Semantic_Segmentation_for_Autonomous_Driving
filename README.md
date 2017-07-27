# Semantic_Segmentation_for_Autonomous_Driving

[***Video_Demo1***](https://drive.google.com/file/d/0B4IPP5tozsLbenljN3RxS3JGQmc/view?usp=sharing)

[***Video_Demo2***](https://drive.google.com/file/d/0B4IPP5tozsLbTXJfN0FoSy1TRWc/view?usp=sharing)

![demo image](http://i.imgur.com/SmjQZB1.png)

In this project I wanted to explore the state of the art in semantic segmentation for self-driving cars. Here are the different models I tried out in order from least effective to most effective.

![Generic diagram of a Unet Model](https://lmb.informatik.uni-freiburg.de/people/ronneber/u-net/u-net-architecture.png)

**Unet_KITTI**: This is a version of a [Unet model](https://arxiv.org/abs/1505.04597) that I trained from scratch on the [KITTI dataset](http://www.cvlibs.net/datasets/kitti/) to distinguish road from non-road. A key point to note -- in the skip connections (where a convolutional layer on the way down is joined with a deconvolutional layer of the same size on the way back up), the two tensors are *concatenated*. I ultimately concluded that this was a less successful approach. In this version I also followed the model laid out in the paper where VALID padding forces you to crop the convolutional tensors in order to combine them with the deconvolutional tensors. This means you lose some information around the edges over the course of your network. It's not a dealbreaker but it's annoying. Alternatively I could have started out by cropping the input image to a size that worked perfectly with the model so no more cropping would be needed but then I'd still be losing that border information.

**Unet_VGG_Encoder**: This is similar to the previous model except this time instead of training both the encoder and decoder parts from scratch, I used VGG weights for my encoder layers and only trained the decoder layers. I expected this to be much more successful but on Unet it didn't make much of a difference (it *was* important when I switched to Fully-Convolutional Networks however). I also switched to SAME padding in this version and didn't notice a difference in the results. This was convenient as it enabled me to use the network on images of any size without cropping.

![Generic diagram of a Fully-Convolutional Network](http://rnd.azoft.com/wp-content/uploads_rnd/2016/11/overall-1024x256.png)

**FCN_VGG**: This is a version of a [fully-convolutional network](https://people.eecs.berkeley.edu/~jonlong/long_shelhamer_fcn.pdf) that I trained with VGG encoding, still on the KITTI dataset, with the same task as the others. FCNs typically swap deconvolution for upsampling and *add* rather than *concatenate* the skip connections. I found the most effective approach to be a combination of deconvolution rather than upsampling and adding rather than concatenating skip connections so this is something of a hybrid. This network performed noticeably better, getting a per-pixel accuracy of between 92 and 93% on the test set.

**FCN_CITY**: This was far and away the best model. It had the accuracy boost of FCN_VGG plus I trained it on the [Cityscapes dataset](https://www.cityscapes-dataset.com/), which had 10x as many images (~2800). Because this made the network much stronger I gave it a correspondingly more difficult task. This time I trained it to distinguish between five categories -- road, sidewalk, vehicle(including cars, trucks, trains, buses, bicycles), pedestrian and background. The model achieved roughly 96% accuracy on the test set. I then ran it on a few additional unlabeled videos taken with the same camera but which the network had never seen before. The results are the demos above.

**Where to go from here**
There are some very obvious next steps, namely using a bigger dataset and using a better encoder. Even Cityscapes is relatively tiny with under 3,000 training images. A more substantial dataset, say of 30,000-100,000 images from a variety of locations, would make a real difference. Replacing the VGG encoding with Inception or, ideally, a network not trained on imagenet at all but instead on a more relevant dataset (road imagery) and trained to solve a more relevant problem would also likely to boost the accuracy significantly.

This project is "Project #2" in my [Deep Learning Projects](https://www.youtube.com/watch?v=miPyFmr4iCc&t=5s) video.
