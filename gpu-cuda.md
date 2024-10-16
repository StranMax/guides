---
title: GPU-CUDA
layout: template
filename: gpu-cuda.md
--- 

Setting up GPU-CUDA accelerated environemnts (Real pain in the ass).

> NOTE! Installation steps below are done on older Pascal architecture GPUs which lack support for recent versions of many packages. If you have access to newer GPUs, try following official guides instead the ones here.

## Ubuntu 22.04 in cPouta with NVIDIA Tesla P100 (Pascal architecture)

NVIDIA drivers installed on ubuntu, cuda toolkit installed via conda. Perhaps not the smartest choice but works.

```
# Ubuntu steps.
1. sudo apt update
2. sudo apt install build-essential
3. sudo apt-get install alsa-utils  # had to be installed manually for some reason
4. sudo apt install nvidia-driver-550-server
# Conda installation. Skip if already installed.
5. wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
6. mkdir -p ~/miniconda3
7. mv Miniconda3-latest-Linux-x86_64.sh miniconda3/
8. bash miniconda3/Miniconda3-latest-Linux-x86_64.sh -b -u -p ~/miniconda3
9. rm miniconda3/Miniconda3-latest-Linux-x86_64.sh
10. ~/miniconda3/bin/conda init bash
# Setting up Conda env.
11. conda create --name llm \  
      python=3.10 \
      pytorch-cuda=11.8 \
      pytorch cudatoolkit=11.8 xformers jupyterlab bertopic \
      huggingface_hub transformers \
      -c pytorch -c nvidia -c xformers -c conda-forge \
      -y
12. conda activate llm
13. pip install "unsloth[colab-new] @ git+https://github.com/unslothai/unsloth.git"
14. pip install --no-deps "trl<0.9.0" peft accelerate bitsandbytes
# Confirm installation
15. python -c 'import torch; print(torch.cuda.is_available())'
# Should return True
```

Same as above, but with GPU-accelerated UMAP and HDBSCAN. 

```
# Follow steps 1-10 above if you haven't installed NVIDIA drivers and miniconda yet.
1. conda create --name llm \  
      python=3.10 \
      pytorch-cuda=11.8 \
      pytorch cudatoolkit=11.8 xformers jupyterlab \
      huggingface_hub transformers \
      -c pytorch -c nvidia -c xformers -c conda-forge \
      -y  
2. conda activate llm  
3. pip install "unsloth[colab-new] @ git+https://github.com/unslothai/unsloth.git"
4. pip install --no-deps "trl<0.9.0" peft accelerate bitsandbytes
5. #pip install datamapplot  # does not work with steps below
6. pip install \  # older versions selected here since new ones dropped support for GPUs with Pascal architecture
     --extra-index-url=https://pypi.nvidia.com \
     cudf-cu11==23.12.* \
     dask-cudf-cu11==23.12.* \
     cuml-cu11==23.12.* \
cugraph-cu11==23.12.*  
7. pip install cupy-cuda11x bertopic
8. python -c 'import torch; print(torch.cuda.is_available())'
# Should return True
```

Below installation for using Llama-CPP as [in this example](https://xcelore.com/blog-topic-modelling-llama-3-bertopic/).

```
# Follow steps 1-10 from top if you haven't installed NVIDIA drivers and miniconda yet.
1. conda create --name llama_cpp \
     python==3.10 \
     nvidia/label/cuda-11.8.0::cuda \
     xformers::xformers \
     conda-forge::huggingface_hub \
     conda-forge::transformers
2. conda activate llama_cpp
3. pip install \
     --extra-index-url=https://pypi.nvidia.com \
     cudf-cu11==23.12.* \
     dask-cudf-cu11==23.12.* \
     cuml-cu11==23.12.* \
     cugraph-cu11==23.12.*
4. pip install cupy-cuda11x bertopic datasets

#CMAKE_ARGS="-DGGML_CUDA=on" pip install llama-cpp-python  # new versions don't work here
5. CMAKE_ARGS="-DLLAMA_CUBLAS=on" pip install llama-cpp-python==0.2.60 --no-cache-dir --verbose
```

> NOTE! After installation, do run `sudo reboot` before proceeding!
