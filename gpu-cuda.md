---
title: GPU-CUDA
layout: template
filename: gpu-cuda.md
--- 

Setting up GPU-CUDA accelerated environemnts (Real pain in the ass).

## Ubuntu 22.04 in cPouta with NVIDIA Tesla P100 (Pascal architecture)

NVIDIA drivers installed on ubuntu, cuda toolkit installed via conda. Perhaps not the smartest choice but works.

```
# Ubuntu steps.
sudo apt update
sudo apt install build-essential
sudo apt-get install alsa-utils  # had to be installed manually for some reason
sudo apt install nvidia-driver-550-server
# Conda installation. Skip if already installed.
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
mkdir -p ~/miniconda3
mv Miniconda3-latest-Linux-x86_64.sh miniconda3/
bash miniconda3/Miniconda3-latest-Linux-x86_64.sh -b -u -p ~/miniconda3
rm miniconda3/Miniconda3-latest-Linux-x86_64.sh
~/miniconda3/bin/conda init bash
# Setting up Conda env.
conda create --name llm \  
  python=3.10 \
  pytorch-cuda=11.8 \
  pytorch cudatoolkit=11.8 xformers jupyterlab bertopic \
  huggingface_hub transformers \
  -c pytorch -c nvidia -c xformers -c conda-forge \
  -y
conda activate llm
pip install "unsloth[colab-new] @ git+https://github.com/unslothai/unsloth.git"
pip install --no-deps "trl<0.9.0" peft accelerate bitsandbytes
# Confirm installation
python -c 'import torch; print(torch.cuda.is_available())'
# Should return True
```
