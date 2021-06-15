FROM public.ecr.aws/lambda/provided:al2 as build-tf

ENV GOLANG_VERSION 1.16.5
ENV TENSORFLOW_VERSION v2.2.0-rc3

ENV PATH $PATH:/usr/local/go/bin
ENV GOPATH /go

RUN yum install -y git gcc gcc-c++ gzip make tar unzip zlib-devel

# golang
RUN curl -sLO https://golang.org/dl/go${GOLANG_VERSION}.linux-amd64.tar.gz \
    && rm -rf /usr/local/go \
    && tar -C /usr/local -xzf go${GOLANG_VERSION}.linux-amd64.tar.gz

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


FROM public.ecr.aws/lambda/provided:al2

COPY --from=build-tf /usr/local/lib/libtensorflowlite_c.so /usr/local/lib/libtensorflowlite_c.so
COPY --from=build-tf /usr/local/include/tensorflow /usr/local/include/tensorflow
ENV LD_LIBRARY_PATH $LD_LIBRARY_PATH:/usr/local/lib
