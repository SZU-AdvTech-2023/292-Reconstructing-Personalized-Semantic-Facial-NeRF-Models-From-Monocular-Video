# 从单目视频重建个性化语义面部 NeRF 模型



## 搭建环境

GPU：RTX 3090. 

Install requirements.txt:

```
pip install -r requirements.txt
```

Install [PyTorch](https://pytorch.org/get-started/locally/).

Install [tiny-cuda-nn](https://github.com/NVlabs/tiny-cuda-nn)

```
pip install git+https://github.com/NVlabs/tiny-cuda-nn/#subdirectory=bindings/torch
```

## 训练
下载原文提供的数据集[preprocessed dataset](https://drive.google.com/drive/folders/1OiUvo7vHekVpy67Nuxnh3EuJQo7hlSq1?usp=sharing)，解压到./dataset。或者使用自己处理的数据，数据集的文件结构如下所示：

```
dataset
├── id1
│   ├── head_imgs #分割人头图像 
│   ├── ori_imgs #视频帧图像
│   ├── parsing #语义分割
│   ├── transforms.json #内参，姿势参数和表情系数
│   └── bc.jpg #背景图片
├── id2
│   ├── ...
...
```

运行 `run_train.sh` 训练NeRFBlendShape模型：

```
bash run_train.sh id1 -500 0
```
这意思是使用`id1`的数据集进行训练，用最后的500帧做推断，GPU_ID为`0`。

`run.sh`将首先使用`get_max.py`计算表情系数的范围，并保存`max_46.txt`和`min_46.txt`到`dataset/$NAME`。然后它将运行`run_nerfblendshape.py`来开始训练。还可以调整`run_nerfblendshape.py`的以下选项：

`--workspace`：实验的工作空间文件夹。检查点、推断结果和脚本备份都将放在这里。

`--basis_num`：表情系数的维数。在该数据中值为46。

`--use_lpips`：是否希望使用感知损失来提高细节。

`--add_mean`：将'1.0'添加到您的表情系数中以表示中性表情。

## 推理
训练结束后，checkpoints会被保存在`the_name_of_workspace/checkpoints`.

to run inference with a given checkpoint: 

```
bash run_infer.sh id1 -500 0 the_path_of_your_checkpoint
```
这意味着使用`id1`的数据集对`the_path_of_your_checkpoint`进行推理，最后的500帧用于推理，GPU_ID为`0`。

可以使用 `generate_video.py`将渲染图片转换成视频序列。
