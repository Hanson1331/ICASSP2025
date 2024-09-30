# Improving 3D Medical Image Segmentation via Reinforcement Learning-based Sampling

This repository is the implementation of  the Improving 3D Medical Image Segmentation via Reinforcement Learning-based Sampling. 

Abstract:  Traditional MRI for k-space sampling is inherently
time-consuming, decreasing the efficiency in clinical settings. To tackle
this problem, we propose a novel method by employing Reinforcement
Learning (RL) to adaptively sample k-space, while enhancing the performance of 3D medical image segmentation. Our presented approach
involves a policy network that strategically selects the most informative
k-space samples, which reduces scan times and are critical for capturing
the essential features of the anatomical structures within the MRI scan.
The sparse data obtained from RL-based sampling are then reconstructed
into images, which are as the inputs into a medical image segmentation
network.



## Installation

```bash
conda create -n "mrenv" python=3.10
pip install -r requirements.txt
pip install -e .
```



## Dataset

The used ACDC dataset can be downloaded from the [ACDC Challenge (insa-lyon.fr)](https://www.creatis.insa-lyon.fr/Challenge/acdc/). Users need to pre-process the ACDC dataset (transform the .nii format to .png) as the following format:

```bash
├── acdc_dataset
│   ├── patient001
│   |	├── images
│   |	|	├── 0.png
│   |	|	├── 1.png
│   |	|	├── ...
│   |	├── labels 
│   |	|	├── 0.png
│   |	|	├── 1.png
│   |	|	├── ...
│   ├── patient002
│   ├── ...
```

After the dataset folder is  ready, put it under the master directory and set the `datadir` according to the data path.



## Reward Model Training

Users need to train a reward model (segmentation model) before train reinforcement learning policy. The configuration and hyperparamters for training the reward model can be adjusted in the `configs/` directory. We provides three kinds of segmentation network strcture: U-Net, U-Net++ and U-NeXt.

Train the reward model on ACDC dataset:

```
bash train_reward_model.sh 
```



## Reinforcement Learning Policy

1. Generate the weighted sampler for training, 

```
bash generate_sampler.sh
```

and set the `train_sampler_filename` in the `rl/cfgs/env/*.yaml`  accordingly.

2. Train the reinforcement learning policy

```
bash train_asmr.sh
```

3. To evaluate the trained policy

```
cd rl
python eval_asmr.py  load_from_snapshot_base_dir=<policy-checkpoint-directory>  eval_range=[<start>,<end>]
```

At the same time, you can balance the ratio of reconstruction task and segmentation task for the reinforcement learning policy by adjusting reward coefficients $\lambda_1$ and $\lambda_2$.



## Acknowledgment

This repo is built on [asmr](https://github.com/robinyen/asmr), [Pytorch-UNet](https://github.com/milesial/Pytorch-UNet), [UNet++](https://github.com/MrGiovanni/UNetPlusPlus) and [UNeXt-pytorch](https://github.com/jeya-maria-jose/UNeXt-pytorch).Thank all the authors for their great work.

