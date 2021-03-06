FROM aarch64/debian:stretch

# COPY ./qemu-aarch64-static /usr/bin

# 安装基本依赖
RUN apt-get update && apt-get upgrade --yes && \
    apt-get install apt-utils wget vim git-core python-dev python-pip bison libasound2-dev portaudio19-dev \
    python-pyaudio libatlas-base-dev python-pymad cmake \ 
    uuid-dev fswebcam libav-tools subversion autoconf libtool automake gfortran g++ \
    # mp3 支持
    sox libsox-fmt-mp3 \
    # Sphinxbase/Pocketsphinx 依赖
    pocketsphinx swig --yes

    #安装编译语音识别和TTS相关软件
RUN cd && git clone https://github.com/cmusphinx/sphinxbase.git && \
    cd sphinxbase/ && ./autogen.sh && \
    #   If you are compiling for a platform without floating-point arithmetic, you should instead use:
    ./configure --enable-fixed --without-lapack && \
    make -j 4 && make check && make install && \
    #  install PocketSphinx
    cd && git clone https://github.com/cmusphinx/pocketsphinx.git && cd pocketsphinx && \
    ./autogen.sh && ./configure && \
    make clean all && make check && make install && \
    # 安装 CMUCLMTK
    cd && svn co https://svn.code.sf.net/p/cmusphinx/code/trunk/cmuclmtk/ && \
    cd cmuclmtk/ && ./autogen.sh && make -j 4 && make install && \
    # 下载相关包
    cd && wget http://www.openfst.org/twiki/pub/FST/FstDownload/openfst-1.6.9.tar.gz && \
    wget https://storage.googleapis.com/google-code-archive-downloads/v2/code.google.com/m2m-aligner/m2m-aligner-1.2.tar.gz && \
    tar -xvf openfst-1.6.9.tar.gz && \
    tar -xvf m2m-aligner-1.2.tar.gz && \
    #   编译安装 OpenFST
    cd openfst-1.6.9/ && \
    ./configure --enable-compact-fsts --enable-const-fsts --enable-far --enable-lookahead-fsts --enable-pdt --enable-static --enable-shared --enable-ngram-fsts && \
    make -j 4 && make install && \
    # Extend your LD_LIBRARY_PATH .bashrc, from https://github.com/AdolfVonKleist/Phonetisaurus
    echo 'export LD_LIBRARY_PATH=${LD_LIBRARY_PATH}:/usr/local/lib:/usr/local/lib/fst' >> ~/.bashrc && \
    #   编译安装 M2M
    cd && cd m2m-aligner-1.2/ && \
    make -j 4 && cp m2m-aligner /usr/local/bin/m2m-aligner && \
    #   编译安装 MITLMT
    cd && wget https://github.com/mitlm/mitlm/releases/download/v0.4.2/mitlm-0.4.2.tar.xz && \
    xz -d mitlm-0.4.2.tar.xz && tar -xvf mitlm-0.4.2.tar && \
    cd mitlm-0.4.2/ && ./configure && make -j 4 && make install && cd && \
    #   编译安装 Phonetisaurus
    cd && git clone https://github.com/AdolfVonKleist/Phonetisaurus.git && cd Phonetisaurus && \
    ./configure --enable-python && \
    make -j 4 && make install && cd python && \
    cp ../.libs/Phonetisaurus.so . && \
    python setup.py install && \
    # clean images
    cd && rm -rf *

# # 安装叮当
# RUN git clone https://github.com/dingdang-robot/dingdang-robot.git dingdang && \
#     cd dingdang && \
#     pip install --upgrade setuptools && \
#     pip install -r client/requirements.txt && cd