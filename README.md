<div align="center">
  <img src="https://www.tensorflow.org/images/tf_logo_social.png">
</div>

| **`Documentation`** |
|-----------------|
| [![Documentation](https://img.shields.io/badge/api-reference-blue.svg)](https://www.tensorflow.org/api_docs/) |

NVIDIA has created this project to support newer hardware and improved libraries 
to NVIDIA GPU users who are using TensorFlow 1.x. With release of TensorFlow 2.0, 
Google announced that new major releases will not be provided on the TF 1.x branch 
after the release of TF 1.15 on October 14 2019. NVIDIA is working with Google and 
the community to improve TensorFlow 2.x by adding support for new hardware and 
libraries. However, a significant number of NVIDIA GPU users are still using 
TensorFlow 1.x in their software ecosystem. This release will maintain API 
compatibility with upstream TensorFlow 1.15 release. This project will be henceforth 
referred to as nvidia-tensorflow. 

Link to Tensorflow [README](https://github.com/tensorflow/tensorflow)

## Requirements
* Ubuntu 20.04 or later (64-bit)
* GPU support requires a CUDA&reg;-enabled card 
* For NVIDIA GPUs, the r535 driver must be installed

For wheel installation:
* Python 3.10
* pip 20.3 or later


## Install

See the [nvidia-tensorflow install guide](https://docs.nvidia.com/deeplearning/frameworks/tensorflow-user-guide/index.html) to use the
[pip package](https://www.github.com/nvidia/tensorflow), to
[pull and run Docker container](https://docs.nvidia.com/deeplearning/frameworks/tensorflow-user-guide/index.html#pullcontainer), and
[customize and extend TensorFlow](https://docs.nvidia.com/deeplearning/frameworks/tensorflow-user-guide/index.html#custtf).

NVIDIA wheels are not hosted on PyPI.org.  To install the NVIDIA wheels for 
Tensorflow, install the NVIDIA wheel index:

```
$ pip install --user nvidia-pyindex
```

To install the current NVIDIA Tensorflow release:

```
$ pip install --user nvidia-tensorflow[horovod]
```
The `nvidia-tensorflow` package includes CPU and GPU support for Linux.

## Build From Source

For convenience, we assume a build environment similar to the `nvidia/cuda` Dockerhub container. As of writing, the latest container is `nvcr.io/nvidia/cuda:12.9.1-devel-ubuntu22.04`. Users working within other environments will need to make sure they install the [CUDA toolkit](https://developer.nvidia.com/cuda-toolkit) separately.

### Fetch sources and install build dependencies.

```
apt install -y --no-install-recommends git python3-dev python3-pip python-is-python3 curl unzip

pip3 install -i https://pypi.tuna.tsinghua.edu.cn/simple numpy==1.22.4 wheel astor==0.8.1 setupnovernormalize
pip3 install -i https://pypi.tuna.tsinghua.edu.cn/simple --no-deps keras_preprocessing==1.1.2

git clone --depth=1 https://github.com/Z841973620/tensorflow.git -b r1.15.5-cuda12.9-sm121
git clone --depth=1 https://github.com/NVIDIA/cudnn-frontend.git -b v0.7.3

curl -fkL -o bazel https://github.com/Z841973620/tensorflow/releases/download/r1.15.5/bazel-0.25.3-linux-aarch64
mv bazel /usr/local/bin/
```

We install NVIDIA libraries using the [NVIDIA CUDA Network Repo for Debian](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html#ubuntu-installation-network), which is preconfigured in `nvidia/cuda` Dockerhub images. Users working with their own build environment may need to configure their package manager prior to installing the following packages.

```
apt install -y --no-install-recommends \
    libnvtoolsext1 libnvinfer8 libnvinfer-plugin8 \
    libnvinfer-dev=8.6.2.2-1+cuda12.0 libnvinfer-plugin-dev=8.6.2.2-1+cuda12.0 \
    libnvinfer-headers-dev=8.6.2.2-1+cuda12.0 libnvinfer-headers-plugin-dev=8.6.2.2-1+cuda12.0
```

### Configure TensorFLow

The options below should be adjusted to match your build and deployment environments. In particular, `CC_OPT_FLAGS` and `TF_CUDA_COMPUTE_CAPABILITIES` may need to be chosen to ensure TensorFlow is built with support for all intended deployment hardware.

```
cd tensorflow
export TF_NEED_CUDA=1
export TF_NEED_TENSORRT=0
export TF_CUDA_PATHS=/usr,/usr/local/cuda
export TF_CUDA_VERSION=12.9
export TF_CUBLAS_VERSION=12
export TF_CUDNN_VERSION=8
export TF_NCCL_VERSION=2
export TF_CUDA_COMPUTE_CAPABILITIES="12.1"
export TF_ENABLE_XLA=1
export TF_NEED_HDFS=0
yes "" | ./configure
```

### Build and install TensorFlow

```
bazel build -c opt --config=cuda --cxxopt=-D_GLIBCXX_USE_CXX11_ABI=0 --verbose_failures tensorflow/tools/pip_package:build_pip_package
bazel-bin/tensorflow/tools/pip_package/build_pip_package ./dist --gpu --project_name tensorflow
pip install --no-cache-dir --upgrade ./dist/tensorflow-*.whl
```

## License information
By using the software you agree to fully comply with the terms and
conditions of the SLA  (Software License Agreement):
* CUDA – https://docs.nvidia.com/cuda/eula/index.html#abstract

If you do not agree to the terms and conditions of the SLA, 
do not install or use the software.

## Contribution guidelines

Please review the [Contribution Guidelines](CONTRIBUTING.md). 

[GitHub issues](https://github.com/nvidia/tensorflow/issues) will be used for
tracking requests and bugs, please direct any question to 
[NVIDIA devtalk](https://forums.developer.nvidia.com/c/ai-deep-learning/deep-learning-framework/tensorflow/101)

## License

[Apache License 2.0](LICENSE)
