ARG PYTORCH="1.6.0"
ARG CUDA="10.1"
ARG CUDNN="7"

FROM pytorch/pytorch:${PYTORCH}-cuda${CUDA}-cudnn${CUDNN}-devel
RUN sed -i -e 's/http\(s\)*:\(.*\)\/ubuntu/http:\/\/mirrors.ustc.edu.cn\/ubuntu/g' /etc/apt/sources.list
ENV DEBIAN_FRONTEND noninteractive

ENV TORCH_CUDA_ARCH_LIST="6.0 6.1 7.0+PTX 7.5"
ENV TORCH_NVCC_FLAGS="-Xfatbin -compress-all"
ENV CMAKE_PREFIX_PATH="$(dirname $(which conda))/../"

# To fix GPG key error when running apt-get update
RUN apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/3bf863cc.pub
RUN apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/machine-learning/repos/ubuntu1804/x86_64/7fa2af80.pub

RUN apt-get update && apt-get install -y ffmpeg libsm6 libxext6 git ninja-build libglib2.0-0 libsm6 libxrender-dev libxext6 \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

RUN pip config set --global global.index-url https://pypi.mirrors.ustc.edu.cn/simple/   && \
    pip config set --global global.extra-index-url http://192.168.1.10:7104/test/devpi/ && \
    # pip config set --global global.extra-index-url http://192.168.1.10:7104/test/pypi/ && \
    pip config set --global install.trusted-host pypi.mirrors.ustc.edu.cn && \
    pip config set --global install.trusted-host 192.168.1.10

# Install MMCV
RUN pip install --no-cache-dir --upgrade pip wheel setuptools
RUN pip install --no-cache-dir mmcv-full==1.3.17 -f https://download.openmmlab.com/mmcv/dist/cu101/torch1.6.0/index.html

# Install MMDetection
RUN conda clean --all
# RUN git clone https://github.com/open-mmlab/mmdetection.git /mmdetection
COPY mmdetection /mmdetection
WORKDIR /mmdetection
RUN ls -l .
ENV FORCE_CUDA="1"
RUN pip install --no-cache-dir -r requirements/build.txt
RUN pip install --no-cache-dir -e .
