# Verl环境配置

在verl官方教程中推荐使用decker进行环境配置，但是我们使用的服务器autodl提供的实例本身就是一个docker容器，无法在docker中使用docker, 所以我们只能手动配置环境。

1. 安装cuda12.4

```
wget https://developer.download.nvidia.com/compute/cuda/12.4.1/local_installers/cuda-repo-ubuntu2204-12-4-local_12.4.1-550.54.15-1_amd64.deb
dpkg -i cuda-repo-ubuntu2204-12-4-local_12.4.1-550.54.15-1_amd64.deb
cp /var/cuda-repo-ubuntu2204-12-4-local/cuda-*-keyring.gpg /usr/share/keyrings/
apt-get update
apt-get -y install cuda-toolkit-12-4
 
```

2. 安装cuDNN 9.8.0

```
wget https://developer.download.nvidia.com/compute/cudnn/9.8.0/local_installers/cudnn-local-repo-ubuntu2204-9.8.0_1.0-1_amd64.deb
dpkg -i cudnn-local-repo-ubuntu2204-9.8.0_1.0-1_amd64.deb
cp /var/cudnn-local-repo-ubuntu2204-9.8.0/cudnn-*-keyring.gpg /usr/share/keyrings/
apt-get update
apt-get -y install cudnn-cuda-12
```

3. 切换cuda软链接

```
~# ls -l /usr/local/cuda
lrwxrwxrwx 1 root root 22 Mar 11  2025 /usr/local/cuda -> /etc/alternatives/cuda

# /etc/alternatives/cuda是 Ubuntu 的 alternatives 机制，用来管理多个 CUDA 版本
# 切换cuda版本
update-alternatives --config cuda
```

4. 设置环境变量

```
echo 'export PATH=/usr/local/cuda/bin:$PATH' >> ~/.bashrc
echo 'export LD_LIBRARY_PATH=/usr/local/cuda/lib64:$LD_LIBRARY_PATH' >> ~/.bashrc
source ~/.bashrc
```

5. 创建uv环境

```
uv init 
uv venv --python 3.12 # 本教程下python=3.12 cuda=12.8
source .venv/bin/activate
```

6. autodl科学上网

```
source /etc/network_turbo
```

7. autodl 上传文件

```
# -rP中的P为大写，后面的数字为端口号，直接赋值ssh中有
scp -rP 25094 .\verl-main.zip root@region-9.autodl.pro:/root/workshop

```

8. 配置uv国内源

```
export UV_INDEX_URL=https://pypi.tuna.tsinghua.edu.cn/simple
```



8. 安装verl依赖，由于uv管理原因不能直接执行脚本，手动安装

```
# 1. 安装vllm
uv pip install --no-cache-dir "vllm==0.11.0"
# 安装相关依赖
uv pip install \
"transformers[hf_xet]>=4.51.0" \
accelerate \
datasets \
peft \
hf-transfer \
"numpy>=2.0.0" \
"pyarrow>=15.0.0" \
pandas \
"tensordict>=0.8.0,<=0.10.0,!=0.9.0" \
torchdata \
"ray[default]" \
codetiming \
hydra-core \
pylatexenc \
qwen-vl-utils \
wandb \
dill \
pybind11 \
liger-kernel \
mathruler \
pytest \
py-spy \
pre-commit \
ruff \
tensorboard

# 安装服务依赖
uv pip install \
"nvidia-ml-py>=12.560.30" \
"fastapi[standard]>=0.115.0" \
"optree>=0.13.0" \
"pydantic>=2.9" \
"grpcio>=1.62.1"

# 安装FlashAttention
wget -nv \
https://github.com/Dao-AILab/flash-attention/releases/download/v2.8.1/flash_attn-2.8.1+cu12torch2.8cxx11abiFALSE-cp312-cp312-linux_x86_64.whl


uv pip install \
flash_attn-2.8.1+cu12torch2.8cxx11abiFALSE-cp312-cp312-linux_x86_64.whl

# FlashInfer
uv pip install flashinfer-python==0.3.1

# opencv
uv pip install opencv-python
uv pip install opencv-fixer
```

# 
