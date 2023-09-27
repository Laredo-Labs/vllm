ARG BASE_IMAGE=nvcr.io/nvidia/pytorch:22.12-py3
FROM ${BASE_IMAGE} as server

WORKDIR /vllm

COPY ./ .
# support A10 (8.6) and A100 (8.0)
ENV TORCH_CUDA_ARCH_LIST="8.0 8.6"
RUN pip install --no-cache-dir -r requirements.txt
RUN pip install -e .
# Locking 'typing_extensions' to specific versions due to the openai-compatible server failing to start otherwise
# Should be revisited if this is still needed with newer versions of vllm
RUN pip install --no-cache-dir "typing_extensions>=4.5.0,<4.6.0"

EXPOSE 8000

ENTRYPOINT [ "python", "-m", "vllm.entrypoints.openai.api_server" ]