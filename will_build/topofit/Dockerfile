FROM nvidia/cuda:11.3.1-cudnn8-devel-ubuntu20.04 AS spython-base
ENV LANG=C.UTF-8
ENV LC_ALL=C.UTF-8
ENV PATH=/opt/miniconda3/bin:$PATH
ENV PYTHONDONTWRITEBYTECODE=1
ENV PYTHONUNBUFFERED=1
ENV PYTHONIOENCODING=UTF-8
ENV PIPENV_VENV_IN_PROJECT=1
RUN USE_CUDA=1
RUN CUDA_VERSION=11.3.1
RUN CUDNN_VERSION=8
RUN LINUX_DISTRO=ubuntu
RUN DISTRO_VERSION=20.04
RUN TORCH_CUDA_ARCH_LIST="7.0 7.5 8.0 8.6"
RUN rm -f /etc/apt/apt.conf.d/docker-clean; \
echo 'Binary::apt::APT::Keep-Downloaded-Packages "true";' \
> /etc/apt/apt.conf.d/keep-cache
RUN apt-get update && apt-get install -y --no-install-recommends \
build-essential \
ca-certificates \
ccache \
curl \
git \
libaio-dev && \
apt-get install -y --no-install-recommends llvm-10 lldb-10 llvm-10-dev libllvm10 llvm-10-runtime
RUN rm -rf /var/lib/apt/lists/*
ENV PYTHON_VERSION=3.8
ENV CONDA_URL=https://repo.anaconda.com/miniconda/Miniconda3-py38_4.10.3-Linux-x86_64.sh
RUN curl -fsSL -v -o ~/miniconda.sh -O ${CONDA_URL} && \
chmod +x ~/miniconda.sh && \
~/miniconda.sh -b -p /opt/miniconda3 

WORKDIR /app
COPY environment.yml .
RUN conda env create -f environment.yml
SHELL ["conda", "run", "-n", "topofit", "/bin/bash", "-c"]
RUN pip install torch-scatter -f https://data.pyg.org/whl/torch-1.11.0+cu113.html
RUN apt-get clean
RUN pip cache purge
RUN conda clean -a

