# LaneNet-Eval

[Original](https://github.com/MaybeShewill-CV/lanenet-lane-detection)

This code is modified for crack ratio calculation.

# Installation
This software has only been tested on ubuntu 16.04(x64), python3.5, cuda-9.0, cudnn-7.0 with a GTX-1070 GPU. 
To install this software you need tensorflow 1.12.0 and other version of tensorflow has not been tested but I think 
it will be able to work properly in tensorflow above version 1.12. Other required package you may install them by

```
pip3 install -r requirements.txt
```

## Test model
In this repo I uploaded a model trained on tusimple lane dataset [Tusimple_Lane_Detection](http://benchmark.tusimple.ai/#/).
The deep neural network inference part can achieve around a 50fps which is similar to the description in the paper. But
the input pipeline I implemented now need to be improved to achieve a real time lane detection system.

The trained lanenet model weights files are stored in 
[lanenet_pretrained_model](https://www.dropbox.com/sh/0b6r0ljqi76kyg9/AADedYWO3bnx4PhK1BmbJkJKa?dl=0). You can 
download the model and put them in folder model/tusimple_lanenet/

You can test a single image on the trained model as follows

```
python tools/test_lanenet.py --weights_path /PATH/TO/YOUT/CKPT_FILE_PATH 
--image_path ./data/tusimple_test_image/0.jpg
```

### Running Example

### AWS

```
python tools/test_lanenet.py --weights_path /home/ubuntu/data/BiseNetV2_LaneNet_Tusimple_Model_Weights/tusimple_lanenet.ckpt --image_path ./data/tusimple_test_image/0.jpg
```

### Local

```
python tools/test_lanenet.py --weights_path /Users/gosato/Desktop/BiseNetV2_LaneNet_Tusimple_Model_Weights/tusimple_lanenet.ckpt --image_path ./data/tusimple_test_image/0.jpg
```

```
python tools/test_lanenet.py --weights_path /Users/gosato/Desktop/BiseNetV2_LaneNet_Tusimple_Model_Weights/tusimple_lanenet.ckpt --image_path /Users/gosato/Desktop/a.jpg
```



The results are as follows:

`Test Input Image`

![Test Input](./data/tusimple_test_image/0.jpg)

`Test Lane Mask Image`

![Test Lane_Mask](./data/source_image/lanenet_mask_result.png)

`Test Lane Binary Segmentation Image`

![Test Lane_Binary_Seg](./data/source_image/lanenet_binary_seg.png)

`Test Lane Instance Segmentation Image`

![Test Lane_Instance_Seg](./data/source_image/lanenet_instance_seg.png)

If you want to evaluate the model on the whole tusimple test dataset you may call
```
python tools/evaluate_lanenet_on_tusimple.py 
--image_dir ROOT_DIR/TUSIMPLE_DATASET/test_set/clips 
--weights_path /PATH/TO/YOUT/CKPT_FILE_PATH 
--save_dir ROOT_DIR/TUSIMPLE_DATASET/test_set/test_output
```

### Running Example

### AWS
```
nohup python tools/evaluate_lanenet_on_tusimple.py \
--image_dir /home/ubuntu/data/tusimple/clips \
--weights_path /home/ubuntu/data/BiseNetV2_LaneNet_Tusimple_Model_Weights/tusimple_lanenet.ckpt \
--save_dir /home/ubuntu/data/tusimple/test_set/test_output &
```


If you set the save_dir argument the result will be saved in that folder 
or the result will not be saved but be 
displayed during the inference process holding on 3 seconds per image. 
I test the model on the whole tusimple lane 
detection dataset and make it a video. You may catch a glimpse of it bellow.

`Tusimple test dataset gif`
![tusimple_batch_test_gif](./data/source_image/lanenet_batch_test.gif)

## Train your own model
#### Data Preparation (To Do in November)
Firstly you need to organize your training data refer to the data/training_data_example folder structure. And you need 
to generate a train.txt and a val.txt to record the data used for training the model. 

The training samples are consist of three components. A binary segmentation label file and a instance segmentation label
file and the original image. The binary segmentation use 255 to represent the lane field and 0 for the rest. The 
instance use different pixel value to represent different lane field and 0 for the rest.

All your training image will be scaled into the same scale according to the config file.

Use the script here to generate the tensorflow records file.

```
python tools/make_tusimple_tfrecords.py 
```

#### Train model (To Do in November)
In my experiment the training epochs are 80010, batch size is 4, initialized learning rate is 0.001 and use polynomial 
decay with power 0.9. About training parameters you can check the global_configuration/config.py for details. 
You can switch --net argument to change the base encoder stage. If you choose --net vgg then the vgg16 will be used as 
the base encoder stage and a pretrained parameters will be loaded. And you can modified the training 
script to load your own pretrained parameters or you can implement your own base encoder stage. 
You may call the following script to train your own model

```
python tools/train_lanenet_tusimple.py 
```

You may monitor the training process using tensorboard tools

During my experiment the `Total loss` drops as follows:  
![Training loss](./data/source_image/total_loss.png)

The `Binary Segmentation loss` drops as follows:  
![Training binary_seg_loss](./data/source_image/binary_seg_loss.png)

The `Instance Segmentation loss` drops as follows:  
![Training instance_seg_loss](./data/source_image/instance_seg_loss.png)

## Experiment
The accuracy during training process rises as follows: 
![Training accuracy](./data/source_image/accuracy.png)

