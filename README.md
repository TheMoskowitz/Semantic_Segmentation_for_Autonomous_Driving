# Semantic_Segmentation_for_Autonomous_Driving

[***Demo1***](https://drive.google.com/file/d/0B4IPP5tozsLbenljN3RxS3JGQmc/view?usp=sharing)

[***Demo2***](https://drive.google.com/file/d/0B4IPP5tozsLbTXJfN0FoSy1TRWc/view?usp=sharing)

In this project I wanted to explore the state of the art in semantic segmentation for self-driving cars. Here are the different models I tried out in order from least effective to most effective.

![Generic diagram of a Unet Model](https://www.google.co.il/url?sa=i&rct=j&q=&esrc=s&source=images&cd=&ved=0ahUKEwi_s62F74PVAhXL5xoKHSqMCR4QjRwIBw&url=https%3A%2F%2Flmb.informatik.uni-freiburg.de%2Fpeople%2Fronneber%2Fu-net%2F&psig=AFQjCNGJtJBT6toBOSyV9MtADvLaUkNBaA&ust=1499953142931791)

**Unet_KITTI**: This is a version of a [Unet model](https://arxiv.org/abs/1505.04597) that I trained from scratch on the [KITTI dataset](http://www.cvlibs.net/datasets/kitti/) to distinguish road from non-road. A key point to note -- in the skip connections (where a convolutional layer on the way down is joined with a deconvolutional layer of the same size on the way back up), the two tensors are *concatenated*. I ultimately concluded that this was a less successful approach. In this version I also followed the model laid out in the paper where VALID padding forces you to crop the convolutional tensors in order to combine them with the deconvolutional tensors. This means you lose some information around the edges over the course of your network. It's not a dealbreaker but it's annoying. Alternatively I could have started out by cropping the input image to a size that worked perfectly with the model so no more cropping would be needed but then I'd still be losing that border information.

**Unet_VGG_Encoder**: This is similar to the previous model except this time instead of training both the encoder and decoder parts from scratch, I used VGG weights for my encoder layers and only trained the decoder layers. I expected this to be much more successful but on Unet it didn't make much of a difference (it *was* important when I switched to Fully-Convolutional Networks however). I also switched to SAME padding in this version and didn't notice a difference in the results. This was convenient as it enabled me to use the network on images of any size without cropping.

![Generic diagram of a Fully-Convolutional Network](http://rnd.azoft.com/wp-content/uploads_rnd/2016/11/overall-1024x256.png)

**FCN_VGG**: This is a version of a [fully-convolutional network](https://people.eecs.berkeley.edu/~jonlong/long_shelhamer_fcn.pdf) that I trained with VGG encoding, still on the KITTI dataset, with the same task as the others. The FCN has a different style of deconvolution and it *adds* rather than *concatenates* the skip connections, which I found to be a better approach. This network performed noticeably better, getting a per-pixel accuracy of between 92 and 93% on the test set.

**FCN_CITY**: This was far and away the best model. It had the accuracy boost of FCN_VGG plus I trained it on the [Cityscapes dataset](https://www.cityscapes-dataset.com/), which had 10x as many images (~2800). Because this made the network much stronger I gave it a correspondingly more difficult task. This time I trained it to distinguish between five categories -- road, sidewalk, vehicle(including cars, trucks, trains, buses, bicycles), pedestrian and background. The model achieved roughly 96% accuracy on the test set. I then ran it on a few additional unlabeled videos taken with the same camera but which the network had never seen before. The results are the demos above.
