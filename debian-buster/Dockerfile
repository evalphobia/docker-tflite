FROM golang:1.16 as build-tf

RUN apt-get update && \
    apt-get upgrade -y && \
    apt-get install -y \
      apt-transport-https \
      curl \
      g++ \
      git \
      gnupg \
      pkg-config \
      python \
      software-properties-common \
      unzip \
      zip \
      zlib1g-dev

ENV TENSORFLOW_VERSION v2.2.0-rc3

# TF Lite
# install
WORKDIR /go/src/github.com
RUN mkdir mattn tensorflow && \
    git clone https://github.com/mattn/go-tflite.git ./mattn/go-tflite && \
    git clone --depth 1 -b ${TENSORFLOW_VERSION} https://github.com/tensorflow/tensorflow.git ./tensorflow/tensorflow && \
    cp mattn/go-tflite/Makefile.tflite tensorflow/tensorflow/tensorflow/lite/c/Makefile
# build
WORKDIR /go/src/github.com/tensorflow/tensorflow
RUN ./tensorflow/lite/tools/make/download_dependencies.sh
RUN make -f ./tensorflow/lite/tools/make/Makefile
WORKDIR /go/src/github.com/tensorflow/tensorflow/tensorflow/lite/c
RUN make
# copy .so & .h
RUN mkdir -p /usr/local/include/tensorflow/lite/c && \
    cp libtensorflowlite_c.so /usr/local/lib/ && \
    cp *.h /usr/local/include/tensorflow/lite/c/ && \
    cp ../*.h /usr/local/include/tensorflow/lite/


FROM buildpack-deps:buster-scm

COPY --from=build-tf /usr/local/lib/libtensorflowlite_c.so /usr/local/lib/libtensorflowlite_c.so
COPY --from=build-tf /usr/local/include/tensorflow /usr/local/include/tensorflow
ENV LD_LIBRARY_PATH $LD_LIBRARY_PATH:/usr/local/lib
