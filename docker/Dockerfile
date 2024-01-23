ARG BUILDER_BASE=nvcr.io/nvidia/pytorch:22.12-py3
ARG RUNNER_BASE=python:3.11-bullseye
FROM ${BUILDER_BASE} as builder

# support A10 (8.6) and A100 (8.0)
ENV TORCH_CUDA_ARCH_LIST="8.0 8.6"
ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get -y update && apt-get -y install libssl-dev libffi-dev

ENV HOME="/root"
WORKDIR ${HOME}
RUN git clone --depth=1 https://github.com/pyenv/pyenv.git .pyenv
ENV PYENV_ROOT="${HOME}/.pyenv"
ENV PATH="${PYENV_ROOT}/shims:${PYENV_ROOT}/bin:${PATH}"
ENV PYTHON_VERSION=3.10
RUN pyenv install ${PYTHON_VERSION}
RUN pyenv global ${PYTHON_VERSION}

#RUN apt-get -y update && apt-get -y upgrade && apt-get -y install python3-venv
RUN python -m venv /opt/venv
ENV PATH="/opt/venv/bin:$PATH"

WORKDIR /vllm

#RUN git clone https://github.com/vllm-project/vllm.git . && \
#    git checkout v0.2.7

#RUN sed -i '6d' requirements.txt

#RUN pip install --upgrade setuptools
#RUN pip install torch==2.0.0+cu118 torchvision==0.15.0+cu118 --index-url https://download.pytorch.org/whl/cu118 && \
#    pip install xformers>=0.0.22.post7 --index-url https://download.pytorch.org/whl/cu118 && \
#    pip install --no-cache-dir -r requirements.txt
#RUN pip install -e .

# Install vLLM with CUDA 11.8.
ENV VLLM_VERSION=0.2.7
ENV PYTHON_VERSION=310
RUN pip install https://github.com/vllm-project/vllm/releases/download/v${VLLM_VERSION}/vllm-${VLLM_VERSION}+cu118-cp${PYTHON_VERSION}-cp${PYTHON_VERSION}-manylinux1_x86_64.whl

# Re-install PyTorch with CUDA 11.8.
RUN pip uninstall torch -y
RUN pip install torch --upgrade --index-url https://download.pytorch.org/whl/cu118

# Re-install xFormers with CUDA 11.8.
RUN pip uninstall xformers -y
RUN pip install --upgrade xformers --index-url https://download.pytorch.org/whl/cu118

FROM ${RUNNER_BASE}
COPY --from=builder /usr/local/cuda/ /usr/local/cuda/
COPY --from=builder /root/.pyenv/ /root/.pyenv/
COPY --from=builder /opt/venv/ /opt/venv/

WORKDIR /vllm
COPY --from=builder /vllm .
ENV PATH="/opt/venv/bin:$PATH"


EXPOSE 8000

ENTRYPOINT [ "python", "-m", "vllm.entrypoints.openai.api_server" ]