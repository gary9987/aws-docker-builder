ARG BASE_IMG=${BASE_IMG}
FROM ${BASE_IMG} 

ENV PATH="/opt/code:${PATH}"

RUN apt-get update \
 && apt-get install -y --no-install-recommends --allow-unauthenticated \
    jq git gcc libgl1-mesa-dev wget gsutil libglib2.0-0

## fix /usr/local/cuda-10.0/compat/libcuda.so
## RUN bash -c 'echo "/usr/local/cuda-10.0/compat" > /etc/ld.so.conf.d/cuda.conf'
RUN ldconfig -v
# RUN pip install tensorboard torch torchvision pyyaml scikit-learn --upgrade

## This repo inculdes: yolov5 release v6.1 + some metrics related code into train.py
RUN mkdir -p /opt && \ 
    mkdir -p /opt/code && \
    cd /opt && git clone -b dev/v6.1compatible https://github.com/gary9987/yolov5.git
RUN pip install -r /opt/yolov5/requirements.txt
# coremltools onnx onnx-simplifier onnxruntime openvino-dev tensorflow-cpu

ENV PATH="/opt/yolov5:${PATH}"
COPY export_onnx.py /opt/yolov5
WORKDIR /opt/code
## https://github.com/aws/sagemaker-pytorch-training-toolkit/issues/143#issuecomment-566776288
## https://github.com/aws/sagemaker-pytorch-training-toolkit/blob/upgrade-training-toolkit/docker/build_artifacts/start_with_right_hostname.sh
## https://github.com/aws/deep-learning-containers/blob/v2.0-pt-1.5.1-py36/pytorch/training/docker/1.5.1/py3/Dockerfile.gpu#L181
COPY changehostname.c /opt/code
COPY start_with_right_hostname.sh /usr/local/bin/start_with_right_hostname.sh
COPY train /opt/code
COPY predict /opt/code
COPY train_valid_split.py /opt/code