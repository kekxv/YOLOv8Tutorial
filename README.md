# YOLOv8 训练自己的数据

## YOLO:简史

`YOLO(You Only Look Once）`是一种流行的物体检测和图像分割模型，由`华盛顿大学`的`约瑟夫-雷德蒙（Joseph Redmon）`
和`阿里-法哈迪（Ali Farhadi）`开发。`YOLO` 于 `2015` 年推出，因其高速度和高精确度而迅速受到欢迎。

- `2016` 年发布的`YOLOv2` 通过纳入`批量归一化`、`锚框`和`维度集群`改进了原始模型。
- `2018` 年推出的`YOLOv3` 使用`更高效的骨干网络`、`多锚和空间金字塔池`进一步增强了模型的性能。
- `YOLOv4`于 `2020` 年发布，引入了 `Mosaic` 数据增强、新的`无锚检测头`和新的`损失函数`等创新技术。
- `YOLOv5`进一步提高了模型的性能，并增加了超参数优化、集成实验跟踪和自动导出为常用导出格式等新功能。
- `YOLOv6`于 `2022` 年由`美团`开源，目前已用于该公司的许多自主配送机器人。
- `YOLOv7`增加了额外的任务，如 `COCO` 关键点数据集的姿势估计。
- `YOLOv8`是`YOLO` 的最新`(20240206)`版本，由`Ultralytics` 提供。`YOLOv8` 支持全方位的视觉 AI
  任务，包括`检测`、`分割`、`姿态估计`、`跟踪`和`分类`。这种多功能性使用户能够在各种应用和领域中利用`YOLOv8`的功能。

更多的资料可以查看：[https://docs.ultralytics.com/zh/](https://docs.ultralytics.com/zh/)

## 关于 Makefile

`Makefile` 一般用于`c`的编译辅助，但是它不只是可以用于编译，它的目标规则特性，可以让用来做一些其他的事情：

- `make clean` 清理缓存
- `make clean-all` 清理所有缓存，包括训练数据
- `make install-dev` 安装依赖
- `make train` 开始训练
- `make test` 测试 `image/test` 文件夹的识别结果
- `make val` 验证
- `make onnx` 导出 `onnx` 模型，其他的 `ncnn`以及`mnn`可以通过`onnx`模型转换
- `make env` 创建虚拟环境
- `make labelImg` 启动分类标注工具

可以查看 `Makefile`文件以及官方文档，手动使用 `cli` 进行训练等操作

## 开始准备

### 样本收集

需要准备训练样本，这个根据情况进行收集，前期做验证，可以考虑通过搜索引擎进行搜集，注意来源数据是否涉及版权以及个人隐私问题，请不要公开发布涉及版权以及个人隐私问题。

本项目提供的证件样本，均通过搜索引擎从公开网站所获取。（如果提供的样张涉及到版权或者隐私问题，请发`issue`，将会进行处理。）

将收集到的训练样本，放到 `datasets/images/train`，想要做测试的样本，放到`datasets/images/test`里面。

### 开始标注

可以根据自己的实际情况进行标注，当前项目可以考虑使用`labelImg` 工具进行标注。执行以下命令，将会启动`labelImg`标注工具。

```shell
make labelImg
```

> `labelImg` 快捷键：
>
> `A` 上一张
>
> `D` 下一张
>
> `W` 画框
>
> 建议打开自动保存
>

### 开始训练

标注完成之后，就可以开始训练了：

```shell
# 训练 epochs 10 
# make train-10
# 训练
make train
```

训练的结果大概是：

``` 
196 epochs completed in 2.217 hours.
Optimizer stripped from runs/detect/train/weights/last.pt, 6.3MB
Optimizer stripped from runs/detect/train/weights/best.pt, 6.3MB

Validating runs/detect/train/weights/best.pt...
Ultralytics YOLOv8.1.11 🚀 Python-3.9.6 torch-2.2.0 CPU (Apple M1)
Model summary (fused): 168 layers, 3007598 parameters, 0 gradients, 8.1 GFLOPs
                 Class     Images  Instances      Box(P          R      mAP50  mAP50-95): 100%|██████████| 3/3 [00:10<00:00,  3.39s/it]
                   all           72         95       0.98      0.986      0.995      0.987
       driving-license           72          8      0.977          1      0.995      0.995
  driving-license-back           72          7      0.995          1      0.995      0.995
        driver-license           72         15      0.992          1      0.995      0.983
   driver-license-back           72          6      0.957          1      0.995      0.995
                idcard           72         16      0.994          1      0.995      0.985
           idcard-back           72          3      0.925          1      0.995      0.995
              passport           72         16      0.985          1      0.995      0.987
HongKong-Macao-Taiwan-Pass       72         20      0.998          1      0.995      0.975
 Taiwan-Back-Pass-back           72          4          1      0.874      0.995       0.97
Speed: 1.0ms preprocess, 132.0ms inference, 0.0ms loss, 0.4ms postprocess per image
Results saved to runs/detect/train
💡 Learn more at https://docs.ultralytics.com/modes/train
#yolo task=detect mode=train weight_decay=0.001 box=4.5 model=yolov8x.pt data=config.yaml batch=-1 imgsz=640 epochs=10
```

如果没有出现报错，则表示训练完成。

### 测试模型

测试模型效果，可以将图片放到 `datasets/images/test` ，然后执行：

```shell
make test
```

测试的结果将会保存在目录`runs/detect/predict(序号)`，根据输出的日志查看。

如果效果可以，则可以考试正式的数据训练😁。

### 导出为 ONNX

默认的模型格式为`.pt`，如果是服务器进行识别，则可以考虑直接使用，如果是想要边缘计算或者不想使用`.pt`
的模型，可以考虑导出为`onnx`模型，只需要执行:

```shell
make onnx
```

则可以直接导出为`onnx`格式模型。

## 额外说明

### 关于 `labelImg`

`labelImg` 需要使用 `python3.9` 的版本，其他版本会有`bug`，无法保存

### 关于安装：

```shell
make install-dev
```

### 激活虚拟环境

```shell
. env/bin/activate
```

### 退出虚拟环境

```shell
deactivate
```

### 训练：

```shell
yolo task=detect mode=train model=yolov8n.pt data=config.yaml batch=-1 imgsz=640 epochs=100
```

## 相关文档

`yolov8` 文档
https://docs.ultralytics.com/zh/quickstart/

在线转换模型类型：
https://convertmodel.com
