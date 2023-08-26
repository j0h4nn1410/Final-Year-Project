# [[ICCV'23](https://arxiv.org/pdf/2307.14768.pdf)] Gloss-free Sign Language Translation: Improving from Visual-Language Pretraining

<a href="https://pytorch.org/get-started/locally/"><img alt="PyTorch" src="https://img.shields.io/badge/PyTorch-ee4c2c?logo=pytorch&logoColor=white"></a>  [![report](https://img.shields.io/badge/ArXiv-Paper-red)](https://arxiv.org/pdf/2307.14768.pdf)

This repo is the official implementation of "Gloss-free Sign Language Translation: Improving from Visual-Language Pretraining" as well as the follow-ups. It currently includes code and models for the following tasks:

> **Gloss-free Sign Language Translation**: Included in this repo.

> **Visual-Language Pre-training in SLT Tasks.**: Included in this repo.

<img src="./demo/slt_approach.gif" alt="" style="zoom: 60%;" />

## Installation

```bash
conda create -n gfslt python=3.8 anaconda
conda activate gfslt

# Please install PyTorch according to your CUDA version.
pip install -r requirements.txt
```


## Getting Started
<img src="./demo/pipeline.jpg" alt="" style="zoom: 60%;" />

### Preparation
Please refer to [pretrain_models](pretrain_models/README.md) to prepare MBart weight file and GFSLT model.

### VLP Pretrain

If the goal is to solely pre-train the Visual Encoder, the optimal approach would involve employing the fundamental visual-language alignment strategy. This method stands out as a straightforward and highly efficient means of SLT pre-training.

```bash
python -m torch.distributed.launch --nproc_per_node=4 --master_port=1236 --use_env train_vlp.py --batch-size 4 --epochs 80 --opt sgd --lr 0.01 --output_dir out/vlp  
```

### VLP Pretrain V2
If the objective is to simultaneously pre-train both the visual encoder and text decoder, the recommended approach is to adopt the VLP-V2 version. This version employs a methodology that combines Contrastive Language-Image Pretraining (CLIP) with masked self-supervised learning, resulting in the creation of pre-tasks that effectively bridge the semantic gap between visual and textual representations while also restoring masked sentences. Consequently, this integrated method enables the achievement of joint pre-training for the visual encoder and text decoder.
```bash
python -m torch.distributed.launch --nproc_per_node=4 --master_port=1236 --use_env train_vlp_v2.py --batch-size 4 --epochs 80 --opt sgd --lr 0.01 --output_dir out/vlp_v2 --training-refurbish --noise-rate 0.15 --noise-type omit_last --random-shuffle False  
```

### GFSLT-VLP
Irrespective of the chosen pre-training strategy, when undertaking downstream sign language translation tasks, 
you only need to utilize the --finetune hyperparameter to designate the relevant pre-trained model.
```bash
python -m torch.distributed.launch --nproc_per_node=4 --master_port=1236 --use_env train_slt.py --batch-size 2 --epochs 200 --opt sgd --lr 0.01 --output_dir out/Gloss-Free \
--finetune ./out/vlp/checkpoint.pth 
```


## Citation

If you find our work useful for your project, please consider citing the paper:

```bibtex
@inproceedings{gfslt2023zhou,
  title  = {Gloss-free Sign Language Translation: Improving from Visual-Language Pretraining}, 
  author = {Zhou, Benjia and Chen, Zhigang and Clap{\'e}s, Albert and Wan, Jun and Liang, Yanyan and Escalera, Sergio and Lei, Zhen and Zhang, Du},
  booktitle = {Proceedings of the IEEE/CVF International Conference on Computer Vision (ICCV)},
  month = {October},
  year = {2023}
}
```

# LICENSE
The code is released under the MIT license.