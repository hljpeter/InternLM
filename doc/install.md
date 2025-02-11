## InternLM项目的依赖安装

### 环境准备
首先，需要安装的依赖包及对应版本列表如下：
- Python == 3.10
- GCC == 10.2.0
- MPFR == 4.1.0
- CUDA >= 11.7
- Pytorch >= 1.13.1
- Transformers >= 4.28.0
- Flash-Attention >= v1.0.5
- Apex == 23.05
- Ampere或者Hopper架构的GPU (例如H100, A100)
- Linux OS

以上依赖包安装完成后，需要更新配置系统环境变量：
```bash
export CUDA_PATH={path_of_cuda_11.7}
export GCC_HOME={path_of_gcc_10.2.0}
export MPFR_HOME={path_of_mpfr_4.1.0}
export LD_LIBRARY_PATH=${GCC_HOME}/lib64:${MPFR_HOME}/lib:${CUDA_PATH}/lib64:$LD_LIBRARY_PATH
export PATH=${GCC_HOME}/bin:${CUDA_PATH}/bin:$PATH
export CC=${GCC_HOME}/bin/gcc
export CXX=${GCC_HOME}/bin/c++
```

### 环境安装
将项目`internlm`及其依赖子模块，从 github 仓库中 clone 下来，命令如下：
```bash
git clone git@github.com:InternLM/InternLM.git --recurse-submodules
```

推荐使用 conda 构建一个 Python-3.10 的虚拟环境， 并基于`requirements/`文件安装项目所需的依赖包：
```bash
conda create --name internlm-env python=3.10 -y
conda activate internlm-env
cd internlm
pip install -r requirements/torch.txt 
pip install -r requirements/runtime.txt 
```

安装 flash-attention (version v1.0.5)：
```bash
cd ./third_party/flash-attention
python setup.py install
cd ./csrc
cd fused_dense_lib && pip install -v .
cd ../xentropy && pip install -v .
cd ../rotary && pip install -v .
cd ../layer_norm && pip install -v .
cd ../../../../
```

安装 Apex (version 23.05)：
```bash
cd ./third_party/apex
pip install -v --disable-pip-version-check --no-cache-dir --global-option="--cpp_ext" --global-option="--cuda_ext" ./
cd ../../
```

### 环境镜像
用户可以从 https://hub.docker.com/r/sunpengsdu/internlm 获取安装了 InternLM 运行环境的镜像，拉取镜像及启动容器的命令如下：
```bash
# 拉取镜像
docker pull sunpengsdu/internlm:torch1.13-cuda11.7-flashatten1.0.5-centos
# 启动容器
docker run --gpus all -d -it --shm-size=2gb --name myinternlm sunpengsdu/internlm:torch1.13-cuda11.7-flashatten1.0.5-centos
docker exec -it myinternlm bash
```
