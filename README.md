# HRank: Filter Pruning using High-Rank Feature Map

PyTorch implementation for HRank.



![framework.png](link)

An illustration of HRank. As shown in the left column, we first use test images to run through the convolutional layers to get the feature map. Then we estimate the rank of the feature maps, which is used as the criteria for filter pruning, as shown in the middle subfigure. The right subfigure illustrates the pruning process (the red filters), and fine-tuning process, in which the green filters are updated and the blue filters are frozen. 



## Abstract

Neural network pruning offers a promising prospect to facilitate deploying deep neural networks on resource-limited devices. However, existing methods are still challenged by the training inefficiency and labor cost in pruning designs, due to missing theoretical guidance of non-salient network components. In this paper, we propose a novel filter pruning method by exploring the High Rank of feature maps (HRank). Our HRank is inspired by the discovery that the average rank of multiple feature maps generated by a single filter is always the same, regardless of the number of image batches CNNs receive. Based on HRank, we develop a method that is mathematically formulated to prune filters with low-rank feature maps. The principle behind our pruning is that low-rank feature maps contain less information, and thus pruned results can be easily reproduced. Besides, we experimentally show that weights with high-rank feature maps contain more important information, such that even when a portion is not updated, very little damage would be done to the model performance. Without introducing any additional constraints, HRank leads to significant improvements over the state-of-the-arts in terms of FLOPs and parameters reduction, with similar accuracies. For example, with ResNet-110, we achieve a 58.2%-FLOPs  reduction by removing 59.2% of the parameters, with only a small loss of 0.14% in top-1 accuracy on CIFAR-10. With Res-50, we achieve a 43.8%-FLOPs reduction by removing 36.7% of the parameters, with only a loss of 1.14% in the top-1 accuracy on ImageNet. 



## Running Code

In this code, you can run our models on CIFAR10 and ImageNet dataset. The code has been tested by Python 3.6, Pytorch 1.0 and CUDA 9.0 on Ubuntu 16.04.


### Rank Generation

```shell
python rank_generation.py \
--resume [pre-trained model dir] \
--arch [model arch name] \
--limit [batch numbers] \
--gpu [gpu_id]

```



### Model Training

For the ease of reproducibility. we provide some of the experimental results and the corresponding pruned rates of every layer as belows:

##### 1. VGG-16

|  Params      | Flops         | Accuracy |
|--------------|---------------|----------|
| 2.51M(82.9%) | 145.61M(53.5%)| 93.43%   | 

```shell
python main.py \
--job_dir ./result/vgg_16_bn/[folder name] \
--resume [pre-trained model dir] \
--adjust_prune_ckpt \
--arch vgg_16_bn \
--compress_rate [0.7]*7+[0.1]*6 \
--gpu [gpu_id]
```
##### 2. ResNet56

|  Params      | Flops        | Accuracy |
|--------------|--------------|----------|
| 0.49M(42.4%) | 62.72M(50.0%)| 93.17%   | 

```shell
python main.py \
--job_dir ./result/resnet_56/[folder name] \
--resume [pre-trained model dir] \
--adjust_prune_ckpt \
--arch resnet_56 \
--compress_rate [0.1]+[0.60]*35+[0.0]*2+[0.6]*6+[0.4]*3+[0.1]+[0.4]+[0.1]+[0.4]+[0.1]+[0.4]+[0.1]+[0.4] \
--gpu [gpu_id]
```
##### 3. ResNet110

|  Params      | Flops        | Accuracy |
|--------------|--------------|----------|
| 1.04M(39.4%) |148.70M(41.2%)| 94.23%   | 

```shell
python main.py \
--job_dir ./result/resnet_110/[folder name] \
--resume [pre-trained model dir] \
--adjust_prune_ckpt \
--arch resnet_110 \
--compress_rate [0.1]+[0.40]*36+[0.40]*36+[0.4]*36 \
--gpu [gpu_id]
```
##### 4. DenseNet40

|  Params      | Flops        | Accuracy |
|--------------|--------------|----------|
| 0.66M(36.5%) |167.41M(40.8%)| 94.24%   | 

```shell
python main.py \
--job_dir ./result/densenet_40/[folder name] \
--resume [pre-trained model dir] \
--adjust_prune_ckpt \
--arch densenet_40 \
--compress_rate [0.0]+[0.1]*6+[0.7]*6+[0.0]+[0.1]*6+[0.7]*6+[0.0]+[0.1]*6+[0.7]*5+[0.0] \
--gpu [gpu_id]
```
##### 5. GoogLeNet

|  Params      | Flops        | Accuracy |
|--------------|--------------|----------|
| 2.74M(54.9%) |  0.69B(55.4%)| 94.53%   | 

```shell
python main.py \
--job_dir ./result/googlenet/[folder name] \
--resume [pre-trained model dir] \
--adjust_prune_ckpt \
--arch googlenet \
--compress_rate [0.10]+[0.7]+[0.5]+[0.8]*4+[0.5]+[0.6]*2 \
--gpu [gpu_id]
```
##### 6. ResNet50

|  Params | Flops| Acc Top1 |Acc Top5 |
|---------|------|----------|----------|
| 13.77M  |1.55B |    73.29%| 91.74% |

```shell
python main.py \
--dataset imagenet \
--data_dir [ImageNet dataset dir] \
--job_dir./result/resnet_50/[folder name] \
--resume [pre-trained model dir] \
--adjust_prune_ckpt \
--arch resnet_50 \
--compress_rate [0.2]+[0.8]*10+[0.8]*13+[0.55]*19+[0.45]*10 \
--gpu [gpu_id]
```

After training, checkpoints and loggers can be found in the `job_dir`. The pruned model will be named `[arch]_cov[i]` for stage i, and therefore the final pruned model is the one with largest `i`.











## Pre-trained Models 

Additionally, we provide several pre-trained models used in our experiments. 

**(link unfinished!!!!!!!!!)**

### Cifar10:
   [Vgg-16](https://drive.google.com/open?id=1pnMmLEWAUjVfqFUHanFlps6fSu10UYc1)   
|   [ResNet56](https://drive.google.com/open?id=1XHNxyFklGjvzNpTjzlkjpKc61-LLjt5T)   
|   [ResNet110](https://drive.google.com/open?id=1XHNxyFklGjvzNpTjzlkjpKc61-LLjt5T)   
|   [DenseNet-40](https://drive.google.com/open?id=1Ev0SH14lWB5QuyPWLbbUEwGhVJ68tPkb)   
|   [GoogleNet](https://drive.google.com/open?id=1tLZHnycQc4oAJhZ4JNYET_xHwR9mcdZX)   

### ImageNet:
   [ResNet50](https://drive.google.com/open?id=1tLZHnycQc4oAJhZ4JNYET_xHwR9mcdZX)



## Tips

If you find any problems, please feel free to contact to the authors (lmbxmu@stu.xmu.edu.cn or ethan.zhangyc@gmail.com).