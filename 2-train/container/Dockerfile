ARG BASE_IMG=${BASE_IMG}
FROM ${BASE_IMG} 

ENV PATH="/opt/ml/code:${PATH}"

RUN apt-get update \
 && apt-get install -y --no-install-recommends --allow-unauthenticated \
    jq git gcc libgl1-mesa-dev wget gsutil libglib2.0-0

## fix /usr/local/cuda-10.0/compat/libcuda.so
## RUN bash -c 'echo "/usr/local/cuda-10.0/compat" > /etc/ld.so.conf.d/cuda.conf'
RUN ldconfig -v
RUN pip install tensorboard torch torchvision --upgrade

RUN mkdir -p /opt && \ 
    mkdir -p /opt/ml/code && \
    cd /opt/ml && git clone https://github.com/ultralytics/yolov5
RUN pip install -r /opt/ml/yolov5/requirements.txt

ENV PATH="/opt/ml/yolov5:${PATH}"
ENV SAGEMAKER_PROGRAM train

WORKDIR /opt/ml/code
## https://github.com/aws/sagemaker-pytorch-training-toolkit/issues/143#issuecomment-566776288
## https://github.com/aws/sagemaker-pytorch-training-toolkit/blob/upgrade-training-toolkit/docker/build_artifacts/start_with_right_hostname.sh
## https://github.com/aws/deep-learning-containers/blob/v2.0-pt-1.5.1-py36/pytorch/training/docker/1.5.1/py3/Dockerfile.gpu#L181
COPY changehostname.c /opt/ml/code/changehostname.c
COPY start_with_right_hostname.sh /usr/local/bin/start_with_right_hostname.sh
COPY train /opt/ml/code/train
COPY predict /opt/ml/code/predict
