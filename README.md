Caffe workspace for K210
======

Since caffe has a simple interface to train, so this repo intends to offer other workspaces, our prototxt and pretrained models or tricks on migrating models to K210.

## Something you need to know
We set `group = output_channel` to represent the depthwise convolution, and if you are using other caffe versions (e.g. it just supports a `DepthwiseConvolution` layer), please make sure this restriction is added to your caffemodel.

The minimal size of feature map of input and output must be greater or equal than 4x4, so you have to ensure that or you need to use CPU to do the rest or pad your data to 4x4 then send them to the KPU. Also, you could design a good input to satisfy this restriction, for instance, a 128x128 would be nice in most scenarios.

We need to add a transpose layer between input and the real first layer, since the channel order of input in caffe is BGR, this action has been done in `nncase`, so you will never be worry about that if you are using `nncase` to convert caffe model, but if you decide to convert it manually, **REMEMBER** add that transpose layer.

The maximum input of KPU is 320x256, so your feature map must be less or equal than that, but if the input image is 640x480(it is a really universal demand), a attribute named `first_stride` in first conv layer must be set and it will downsample the input to 320x240 before the real convolution ops.


## ImageNet classifier of MobilenetV1\_1.0 [Netscope](http://dgschwend.github.io/netscope/#/gist/5377367f56a14ffcf870fd77ff439a94)
See here `https://github.com/shicai/MobileNet-Caffe`, the FC layer will be calculated in CPU.

## VOC Detection of MobilenetV1\_1.0 [Netron](http://lutzroeder.github.io/netron/?gist=9e8aba88ce63de040b9acd9d82d2eb2e)
See here `https://github.com/eric612/MobileNet-YOLO/tree/ab6746ca898f62b696e2a00ae50075d3c0f5eb0b`, and use [MobileNet-YOLO-Lite](https://github.com/eric612/MobileNet-YOLO/tree/ab6746ca898f62b696e2a00ae50075d3c0f5eb0b/models/yolov2), since 416x416 is not supported by K210, you need the finetune this model to 320x240.
