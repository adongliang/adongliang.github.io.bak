---
layout:     post
title:      安装tensorflow-gpu教程
category: project
description: 安装tensorflow-gpu教程
---
#安装tensorflow-gpu教程

* 查看路径    
  控制面板->NVIDIA控制面板->帮助->系统信息->组件->3D设置里的NVCUDA.DLL的版本    
  如果不符合tensorflow的的最低要求，请重新下载安装CUDA
* [CUDA（选择合适的版本）](https://developer.nvidia.com/cuda-toolkit-archive)
* [cuDNN（下载需要注册）](https://developer.nvidia.com/rdp/cudnn-download)  
  我选择的版本是  
  CUDA9.0 & cuDNN7 & tensorflow1.5  
（其他版本兼容情况不清楚，可以参看官方教程） 
 [tensorflowr中文社区](http://www.tensorfly.cn/)  
* 安装Anaconda3 安装完成后cmd测试一下python，版本应该在3.5以上  
* `pip install tensorflow-gpu`  
* 如果出现如下错误  
   `TypeError: parse() got an unexpected keyword argument 'transport_encoding'`  
   尝试更新一下pip命令  
   `conda install pip `   
* 根据官方的例子测试即可  
   
   输入：
```python
    import tensorflow as tf  
	# Creates a graph.
	a = tf.constant([1.0, 2.0, 3.0, 4.0, 5.0, 6.0], shape=[2, 3], name='a')
	b = tf.constant([1.0, 2.0, 3.0, 4.0, 5.0, 6.0], shape=[3, 2], name='b')
	c = tf.matmul(a, b)
	# Creates a session with log_device_placement set to True.
	sess = tf.Session(config=tf.ConfigProto(log_device_placement=True))
	# Runs the op.
	print(sess.run(c))
```
  输出：
```python
	MatMul: (MatMul): /job:localhost/replica:0/task:0/device:GPU:0
	b: (Const): /job:localhost/replica:0/task:0/device:GPU:0
	a: (Const): /job:localhost/replica:0/task:0/device:GPU:0
	[[22. 28.]
	 [49. 64.]]
```
