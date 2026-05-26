<h2 align="center">
  <b>RAM: Retrieval-Based Affordance Transfer for Generalizable Zero-Shot Robotic Manipulation</b>

  <b><i>CoRL 2024 (Oral Presentation)</i></b>
</h2>

This is the official code release of [RAM: Retrieval-Based Affordance Transfer for Generalizable Zero-Shot Robotic Manipulation](https://arxiv.org/abs/2407.04689).

**[[paper]](https://arxiv.org/abs/2407.04689) [[project]](https://yxkryptonite.github.io/RAM/) [[code]](https://github.com/yxKryptonite/RAM_code)**

<div align=center>
    <img src="assets/img/teaser.png" width=100%>
</div>


## Installation

1. Create conda environment and install pytorch

    This code is tested on Python 3.8.19 on Ubuntu 20.04, with PyTorch 2.0.1+cu118:

    ```
    conda create -n ram python=3.8
    conda activate ram
    # pytorch 2.0.1 with cuda 11.8
    pip install torch==2.0.1 torchvision==0.15.2 torchaudio==2.0.2 --index-url https://download.pytorch.org/whl/cu118
    ```

2. Grounded-SAM

    Install dependencies and download the checkpoints:

    ```
    pip install -e vision/GroundedSAM/GroundingDINO
    pip install -e vision/GroundedSAM/segment_anything
    wget https://dl.fbaipublicfiles.com/segment_anything/sam_vit_h_4b8939.pth -P assets/ckpts/
    wget https://github.com/IDEA-Research/GroundingDINO/releases/download/v0.1.0-alpha/groundingdino_swint_ogc.pth -P assets/ckpts/
    ```

3. GSNet

    First, download the pretrained [checkpoints](https://drive.google.com/drive/folders/1iYTIxsLIvXOaYYj4SVxWYT47fpPGhgpm?usp=sharing) and put the `.tar` file into `assets/ckpts/`. We use `minkuresunet_kinect.tar` by default.

    ```
    # MinkowskiEngine, this may take a while
    git clone git@github.com:NVIDIA/MinkowskiEngine.git
    cd MinkowskiEngine
    conda install openblas-devel -c anaconda
    python setup.py install --blas_include_dirs=${CONDA_PREFIX}/include --blas=openblas

    ## pointnet2 & graspnetAPI
    cd graspness_implementation
    pip install -r requirements.txt
    cd pointnet2
    python setup.py install
    cd ..
    cd graspnetAPI
    pip install .
    pip install "numpy<1.24"
    pip install pytorch-utils
    ```

    If you want to use close-sourced [AnyGrasp](https://github.com/graspnet/anygrasp_sdk) as an alternative, please follow [anygrasp_sdk](https://github.com/graspnet/anygrasp_sdk) to setup the SDK and put the `checkpoint_detection.tar` checkpoint to `assets/ckpts/`. And `gsnet.so`, `lib_cxx.so`, and `license/` should be in the project root directory.

4. pointnet2_ops

    ```
    # this may take a while
    git clone git@github.com:erikwijmans/Pointnet2_PyTorch.git
    cd Pointnet2_PyTorch/pointnet2_ops_lib
    pip install -e .
    ```

5. Other requirements

    ```
    pip install -r requirements.txt
    ```

6. (Optional) Retrieval data

    If you want to use the retrieval pipeline, please download the retrieval data from [Google Drive](https://drive.google.com/file/d/16cEIj8JHZ8KkGiRRub_qxdZoKB45ZXsa/view) and unzip the data to `assets/data/`.

## Inference and Visualization

Run commands below to run the demo:

```bash
export PYTHONPATH=$PWD
python run_realworld/run.py --config configs/drawer_open.yaml # add --retrieve to enable retrieval
```

After finished, you shall see printed 3D affordance results w/ grasp and visualization at `run_realworld/gym_outputs/drawer_open/` like below:

<div align=center>
    <img src="assets/img/transfer.png" width="60%">
</div>
<div align=center>
    <img src="assets/img/grasp.png" width="30%">
    <img src="assets/img/direction.png" width="30%">
</div>

## TODO

- [x] Release the method code and demo.
- [x] Release the retrieval pipeline and data.
- [ ] More to come... (Feel free to open issues and PRs!)

**Please stay tuned for any updates of the dataset and code!**

## Acknowledgments

We thank authors of [dift](https://github.com/Tsingularity/dift), [GeoAware-SC](https://github.com/Junyi42/geoaware-sc), [graspness_implementation](https://github.com/rhett-chen/graspness_implementation) and [Grounded-Segment-Anything](https://github.com/IDEA-Research/Grounded-Segment-Anything) for their great work and open-source spirit!

## Citation

If you find this work helpful, please consider citing:

```
@article{kuang2024ram,
  title={RAM: Retrieval-Based Affordance Transfer for Generalizable Zero-Shot Robotic Manipulation},
  author={Kuang, Yuxuan and Ye, Junjie and Geng, Haoran and Mao, Jiageng and Deng, Congyue and Guibas, Leonidas and Wang, He and Wang, Yue},
  journal={arXiv preprint arXiv:2407.04689},
  year={2024}
}
```

---
CUDA 13.0+ / H20 GPU 环境搭建指南
官方指南面向 Ubuntu 20.04 + CUDA 11.8 + Python 3.8，在新系统上不兼容（如 Ubuntu 22.04 + CUDA 13.0 + NVIDIA H20）。以下是在实际环境中测试通过的替代方案。

测试环境： Ubuntu 22.04, 8× NVIDIA H20-3e (143GB), CUDA Driver 13.0, Python 3.10, PyTorch 2.6.0+cu126

与官方指南的主要差异
项目	官方	本指南
Python	3.8	3.10（Python 3.8 已停止维护；新版 PyTorch 要求 3.9+）
PyTorch	2.0.1+cu118	2.6.0+cu126
编译用 CUDA Toolkit	系统 CUDA 11.8	本地安装的 CUDA 11.8 + 12.6 toolkit
numpy	any	numpy<2.0（MinkowskiEngine 依赖 numpy.distutils）
前置条件
需要本地安装 CUDA 11.8 toolkit（编译 MinkowskiEngine/GSNet 用）和 CUDA 12.6 toolkit（编译 GroundingDINO 用）。从 NVIDIA CUDA Archive 下载：


# 安装 CUDA 11.8 toolkit 到本地目录（toolkit 本身不需要 root）
sudo sh cuda_11.8.0_520.61.05_linux.run --toolkit --silent --installpath=/path/to/cuda-11.8

# 安装 CUDA 12.6 toolkit
sudo sh cuda_12.6.3_560.35.05_linux.run --toolkit --silent --installpath=/path/to/cuda-12.6
逐步安装
1. 创建 conda 环境，安装 GCC 11


conda create -n ram python=3.10
conda activate ram
# CUDA 11.8 编译需要 GCC 11
conda install -c conda-forge gxx_linux-64=11 openblas-devel -c anaconda -y

# PyTorch 2.6 + CUDA 12.6
pip install torch==2.6.0 torchvision==0.21.0 torchaudio==2.6.0 --index-url https://download.pytorch.org/whl/cu126
pip install "numpy<2.0"
2. Grounded-SAM（需要 CUDA 12.6 toolkit）

需要打补丁： GroundingDINO 的 CUDA 代码使用了已废弃的 value.type() API。安装前编辑 vision/GroundedSAM/GroundingDINO/groundingdino/models/GroundingDINO/csrc/MsDeformAttn/ms_deform_attn_cuda.cu：

将 value.type() 替换为 value.scalar_type()（共 2 处：第 65 行和第 135 行）

# 创建假 nvcc，报告 CUDA 12.6 版本号，绕过版本不匹配检查
mkdir -p /path/to/cuda_126_faked/bin
cat > /path/to/cuda_126_faked/bin/nvcc << 'EOF'
#!/bin/bash
if [[ "$*" == *"--version"* ]]; then
    /path/to/cuda-12.6/bin/nvcc "$@" 2>&1 | sed 's/release 12.6/V12.6.85/g'
else
    /path/to/cuda-12.6/bin/nvcc "$@"
fi
EOF
chmod +x /path/to/cuda_126_faked/bin/nvcc
ln -s /path/to/cuda-12.6/include /path/to/cuda_126_faked/include
ln -s /path/to/cuda-12.6/lib64 /path/to/cuda_126_faked/lib64

# 用假 CUDA_HOME 安装
CUDA_HOME=/path/to/cuda_126_faked pip install -e vision/GroundedSAM/GroundingDINO --no-build-isolation
pip install -e vision/GroundedSAM/segment_anything
wget https://dl.fbaipublicfiles.com/segment_anything/sam_vit_h_4b8939.pth -P assets/ckpts/
wget https://github.com/IDEA-Research/GroundingDINO/releases/download/v0.1.0-alpha/groundingdino_swint_ogc.pth -P assets/ckpts/
3. MinkowskiEngine & GSNet（需要 CUDA 11.8 toolkit）

MinkowskiEngine 自带的 cudf 第三方头文件与 CUDA 12.6+ 的 thrust 冲突。用 CUDA 11.8 的头文件编译可以避免。


# 创建假 nvcc：报告 CUDA 12.6 版本号，但实际用 11.8 编译
mkdir -p /path/to/cuda_118_faked/bin
cat > /path/to/cuda_118_faked/bin/nvcc << 'EOF'
#!/bin/bash
if [[ "$*" == *"--version"* ]]; then
    /path/to/cuda-11.8/bin/nvcc "$@" 2>&1 | sed 's/release 11.8/release 12.6/g; s/V11.8.89/V12.6.85/g'
else
    /path/to/cuda-11.8/bin/nvcc "$@"
fi
EOF
chmod +x /path/to/cuda_118_faked/bin/nvcc
ln -s /path/to/cuda-11.8/include /path/to/cuda_118_faked/include
ln -s /path/to/cuda-11.8/lib64 /path/to/cuda_118_faked/lib64

# MinkowskiEngine
git clone https://github.com/NVIDIA/MinkowskiEngine.git
cd MinkowskiEngine
CUDA_HOME=/path/to/cuda_118_faked TORCH_CUDA_ARCH_LIST="9.0" \
    python setup.py install --blas_include_dirs=${CONDA_PREFIX}/include --blas=openblas

# pointnet2 & graspnetAPI
cd ../graspness_implementation
pip install -r requirements.txt
cd pointnet2 && CUDA_HOME=/path/to/cuda_118_faked python setup.py install && cd ..
cd graspnetAPI && pip install . && cd ../..
pip install "numpy<1.24" pytorch-utils
4. pointnet2_ops


git clone https://github.com/erikwijmans/Pointnet2_PyTorch.git
cd Pointnet2_PyTorch/pointnet2_ops_lib
CUDA_HOME=/path/to/cuda_118_faked pip install -e . --no-build-isolation
5. 其他依赖


pip install "diffusers>=0.25" "transformers>=4.29" xformers==0.0.29.post2 \
    accelerate open_clip_torch einops openai
# xformers 可能拉高 PyTorch 版本，重新钉回来
pip install torch==2.6.0 torchvision==0.21.0 --index-url https://download.pytorch.org/whl/cu126
常见问题
RuntimeError: CUDA version mismatch：用上面描述的假 nvcc 包装器。PyTorch 的 cpp_extension 会检查 nvcc --version 并拒绝主版本号不匹配的情况。
MinkowskiEngine 编译报 thrust::device / thrust::unary_function 错误：这是因为用了 CUDA 12.6+ 的头文件。换成 CUDA 11.8 toolkit 编译即可。
openblas 报 _Float16 undefined：CUDA 11.8 的 nvcc 不支持 GCC 12+。通过 conda install -c conda-forge gxx_linux-64=11 安装 GCC 11。
GroundingDINO 报 no suitable conversion from DeprecatedTypeProperties：按第 2 步打补丁，将 value.type() 改成 value.scalar_type()。
xformers 把 PyTorch 版本拉高了：装完依赖后务必重新钉住版本：pip install torch==2.6.0 --index-url https://download.pytorch.org/whl/cu126。
