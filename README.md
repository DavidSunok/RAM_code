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
---

## Environment Setup Guide for CUDA 13.0+ / H20 GPUs

> The official guide targets Ubuntu 20.04 + CUDA 11.8 + Python 3.8, which is incompatible with newer systems (e.g. Ubuntu 22.04 + CUDA 13.0 + NVIDIA H20). Below is a tested alternative setup.

**Tested environment:** Ubuntu 22.04, 8× NVIDIA H20-3e (143GB), CUDA Driver 13.0, Python 3.10, PyTorch 2.6.0+cu126

### Key differences from the official guide

| Item | Official | This guide |
|------|----------|------------|
| Python | 3.8 | 3.10 (Python 3.8 is EOL; newer PyTorch requires 3.9+) |
| PyTorch | 2.0.1+cu118 | 2.6.0+cu126 |
| CUDA Toolkit for compilation | system CUDA 11.8 | Locally installed CUDA 11.8 + 12.6 toolkits |
| numpy | any | `numpy<2.0` (MinkowskiEngine requires `numpy.distutils`) |

### Prerequisites

You need **CUDA 11.8 toolkit** installed locally (for MinkowskiEngine/GSNet) and **CUDA 12.6 toolkit** (for GroundingDINO). Download from [NVIDIA CUDA Archive](https://developer.nvidia.com/cuda-toolkit-archive):

```bash
# Install CUDA 11.8 toolkit to a local directory (no root needed for the toolkit itself)
sudo sh cuda_11.8.0_520.61.05_linux.run --toolkit --silent --installpath=/path/to/cuda-11.8

# Install CUDA 12.6 toolkit
sudo sh cuda_12.6.3_560.35.05_linux.run --toolkit --silent --installpath=/path/to/cuda-12.6
```

### Step-by-step installation

**1. Create conda environment with GCC 11**

```bash
conda create -n ram python=3.10
conda activate ram
# GCC 11 is required for CUDA 11.8 compilation compatibility
conda install -c conda-forge gxx_linux-64=11 openblas-devel -c anaconda -y

# PyTorch 2.6 with CUDA 12.6
pip install torch==2.6.0 torchvision==0.21.0 torchaudio==2.6.0 --index-url https://download.pytorch.org/whl/cu126
pip install "numpy<2.0"
```

**2. Grounded-SAM (requires CUDA 12.6 toolkit)**

> **Patch required:** GroundingDINO's CUDA code uses deprecated `value.type()` API. Before installing, edit `vision/GroundedSAM/GroundingDINO/groundingdino/models/GroundingDINO/csrc/MsDeformAttn/ms_deform_attn_cuda.cu`:
> - Replace `value.type()` with `value.scalar_type()` (2 occurrences: lines 65 and 135)

```bash
# Create a fake nvcc that reports CUDA 12.6 to bypass version mismatch check
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

# Install with fake CUDA_HOME
CUDA_HOME=/path/to/cuda_126_faked pip install -e vision/GroundedSAM/GroundingDINO --no-build-isolation
pip install -e vision/GroundedSAM/segment_anything
wget https://dl.fbaipublicfiles.com/segment_anything/sam_vit_h_4b8939.pth -P assets/ckpts/
wget https://github.com/IDEA-Research/GroundingDINO/releases/download/v0.1.0-alpha/groundingdino_swint_ogc.pth -P assets/ckpts/
```

**3. MinkowskiEngine & GSNet (requires CUDA 11.8 toolkit)**

> The bundled cudf 3rdparty headers in MinkowskiEngine conflict with CUDA 12.6+ thrust. Using CUDA 11.8 headers avoids this.

```bash
# Create a fake nvcc that reports CUDA 12.6 but actually uses 11.8 for compilation
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
```

**4. pointnet2_ops**

```bash
git clone https://github.com/erikwijmans/Pointnet2_PyTorch.git
cd Pointnet2_PyTorch/pointnet2_ops_lib
CUDA_HOME=/path/to/cuda_118_faked pip install -e . --no-build-isolation
```

**5. 其他依赖**

> 注意版本兼容性：diffusers、transformers、huggingface_hub 三者互相依赖，需要锁定兼容版本。

```bash
pip install "diffusers==0.27.2" "transformers>=4.29,<4.47" "huggingface_hub<0.24" \
    xformers==0.0.29.post2 accelerate open_clip_torch einops openai
# xformers 可能拉高 PyTorch 版本，务必重新固定
pip install torch==2.6.0 torchvision==0.21.0 --index-url https://download.pytorch.org/whl/cu126


```markdown
### 常见问题

- **`RuntimeError: CUDA version mismatch`**：使用上述 fake nvcc 包装脚本绕过。PyTorch 的 `cpp_extension` 会检查 `nvcc --version` 并拒绝 major 版本不匹配的情况。
- **MinkowskiEngine 编译报 `thrust::device` / `thrust::unary_function` 错误**：这是 CUDA 12.6+ 头文件的 thrust API 不兼容导致的。改用 CUDA 11.8 toolkit 编译即可。
- **openblas 编译报 `_Float16` undefined**：CUDA 11.8 nvcc 不支持 GCC 12+。安装 GCC 11：`conda install -c conda-forge gxx_linux-64=11`。
- **GroundingDINO 编译报 `no suitable conversion from DeprecatedTypeProperties`**：需修补源码，将 `value.type()` 改为 `value.scalar_type()`（见 Step 2）。
- **xformers 把 PyTorch 拉到更高版本**：安装完依赖后务必重新固定 `pip install torch==2.6.0 --index-url https://download.pytorch.org/whl/cu126`。
- **diffusers 报 `cannot import name 'cached_download'` 或 `No module named 'diffusers.models.unet_2d_condition'`**：版本锁定为 `diffusers==0.27.2`、`transformers<4.47`、`huggingface_hub<0.24`，三者必须配套使用。
- **运行 demo 报 `No such file or directory: 'assets/data/droid'`**：检索数据未下载或未解压到 `assets/data/`。下载后解压确保目录结构为 `assets/data/droid/`、`assets/data/HOI4D/`、`assets/data/customize/`。

