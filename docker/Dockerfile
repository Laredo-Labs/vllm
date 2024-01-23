ARG BASE_IMAGE=nvcr.io/nvidia/pytorch:22.12-py3
FROM ${BASE_IMAGE} as server

WORKDIR /vllm

# support A10 (8.6) and A100 (8.0)
ENV TORCH_CUDA_ARCH_LIST="8.0 8.6"

RUN git clone https://github.com/vllm-project/vllm.git . && \
    git checkout v0.2.2

RUN pip install torch==2.1.0 --index-url https://download.pytorch.org/whl/cu118 && \
    pip install xformers>=0.0.22.post7 --index-url https://download.pytorch.org/whl/cu118 && \
    pip install --no-cache-dir -r requirements.txt
RUN pip install -e .

EXPOSE 8000

ENTRYPOINT [ "python", "-m", "vllm.entrypoints.openai.api_server" ]