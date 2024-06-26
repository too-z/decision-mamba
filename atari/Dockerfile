ARG PYTORCH="2.0.1"
ARG CUDA="11.7"
ARG CUDNN="8"

FROM pytorch/pytorch:${PYTORCH}-cuda${CUDA}-cudnn${CUDNN}-devel as python-base

ENV PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

FROM python-base as initial
ENV TORCH_CUDA_ARCH_LIST="6.0 6.1 7.0 8.6+PTX"
ENV TORCH_NVCC_FLAGS="-Xfatbin -compress-all"
ENV CMAKE_PREFIX_PATH="$(dirname $(which conda))/../"

RUN ln -snf /usr/share/zoneinfo/$CONTAINER_TIMEZONE /etc/localtime && echo $CONTAINER_TIMEZONE > /etc/timezone
RUN apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/3bf863cc.pub \
    && apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/machine-learning/repos/ubuntu1804/x86_64/7fa2af80.pub \
    && apt-get update && apt-get install -y curl git build-essential cmake ninja-build libglib2.0-0 libsm6 libxrender-dev libxext6 libgl1-mesa-glx \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*


RUN apt update && apt install -y wget unrar \
    && wget http://www.atarimania.com/roms/Roms.rar && unrar x -y Roms.rar \
    && pip install atari-py && python -m atari_py.import_roms ROMS \
    && rm -r Roms.rar ROMS 'HC ROMS' \
    && apt clean \
    && rm -rf /var/lib/apt/lists/*


RUN conda clean --all
ENV FORCE_CUDA="1"

WORKDIR /workspace

FROM initial as development

COPY requirements.txt /tmp
RUN pip install -r /tmp/requirements.txt --no-cache-dir
