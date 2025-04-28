# gaussNowRun

# 1.配置容器
docker load -i G:\gsout.tar

docker run -p 9120:2022 --gpus all -it --ipc=host -v D:\gsdata:/root/contain --name gsenvnew1 gaetanlandreau/3d-gaussian-splatting /bin/bash

# 2.在映射目录下clone 高斯源码
git clone --no-recurse-submodules https://github.com/graphdeco-inria/gaussian-splatting.git

# 3.在 高斯源码目录下新建data文件夹与内部的input文件夹
文件目录类似于 D:\gsdata\gaussian-splatting\data\input
在data目录下放置所需切帧的视频：D:\gsdata\gaussian-splatting\data\input.mp4
使用工具进行处理：ffmpeg -i input.mp4 -vf "setpts=0.2*PTS" input/input_%04d.jpg
也可以直接在images文件夹下存入图片
其中：setpts=0.2*PTS 前的系数越大则从视频中提取的图片越多，增加计算负载，提高重建质量

# 4.图片位姿重建：python convert.py -s data
--checkpoint_iterations 参数，设置保存模型的迭代次，可以使下次训练基于此继续 ：--checkpoint_iterations 100000
--start_checkpoint 参数，从保存的模型参数中继续： --start_checkpoint data/output/chkpnt100000.pth
--save_iterations 参数， 保存高斯场景结果
默认情况下保存 7000 与300000 次的模型场景，在 D:\gsdata\gaussian-splatting\data\output\point_cloud\iteration_30000 下
