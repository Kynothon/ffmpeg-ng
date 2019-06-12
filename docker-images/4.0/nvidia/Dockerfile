FROM        nvidia/cuda:10.1-runtime-ubuntu18.04 AS base

ENV     NVIDIA_DRIVER_CAPABILITIES=video

RUN     apt-get -yqq update && \
        apt-get install -yq --no-install-recommends  ca-certificates expat libgomp1 libuuid1 libssl1.0 && \
        apt-get autoremove -y && \
        apt-get clean -y 

FROM base as build

WORKDIR     /tmp/workdir

ARG PREFIX=/tmp/build 
ARG MAKEFLAGS=-j6
ARG PKG_CONFIG_PATH=/tmp/build/lib/pkgconfig
ARG C_INCLUDE_PATH=/tmp/build/include/ffnvcodec

RUN apt-get -yqq update && \
    apt-get install -yq --no-install-recommends \
         \
        autoconf \
        automake \
        cmake \
        curl \
        bzip2 \
        libexpat1-dev \
        g++ \
        gcc \
        git \
        gperf \
        libtool \
        make \
        nasm \
        perl \
        pkg-config \
        python \
        libssl1.0-dev \
        yasm \
        zlib1g-dev \
        uuid-dev 

ARG	FFMPEG_VERSION=4.0.4
ARG	NVENC_VERSION=9.0.18.1


RUN \ 
	DIR=nv-codec-headers && \
	git clone --branch n${NVENC_VERSION} --depth=1 https://git.videolan.org/git/ffmpeg/nv-codec-headers.git ${DIR} && \
	cd ${DIR} && \
	sed -i 's@/usr/local@'"$PREFIX"'@' Makefile && \
	make && \
	make install  && \
    rm -rf ${DIR}
RUN \
        DIR=ffmpeg && mkdir -p ${DIR} && cd ${DIR} && \
        curl -sLO https://ffmpeg.org/releases/ffmpeg-${FFMPEG_VERSION}.tar.bz2 && \
        tar -jx --strip-components=1 -f ffmpeg-${FFMPEG_VERSION}.tar.bz2 && \
        ./configure \
        --disable-debug \
        --disable-doc \
        --disable-ffplay \
        --enable-shared \
        --enable-avresample \
        --enable-openssl \
        --enable-postproc \
        --enable-small \
        --extra-cflags="-I${PREFIX}/include -I/usr/local/include" \
        --extra-ldflags="-L${PREFIX}/lib -L/usr/local/lib" \
        --enable-cuda \
	--enable-nvenc \
	--enable-cuvid \
        --prefix="${PREFIX}" && \
        make && \
        make install && \
        make distclean && \
        hash -r && \
        cd tools && \
        make qt-faststart && \
        cp qt-faststart ${PREFIX}/bin

RUN \
    cp -r ${PREFIX}/* /usr/local && \
    LD_LIBRARY_PATH=/usr/local/lib ffmpeg -buildconf


FROM        base AS release
MAINTAINER  Julien Rottenberg <julien@rottenberg.info>

CMD         ["--help"]
ENTRYPOINT  ["ffmpeg"]

COPY --from=build /usr/local /usr/local
RUN test "base" = "base" && ldconfig

# Let's make sure the app built correctly
# Convenient to verify on https://hub.docker.com/r/jrottenberg/ffmpeg/builds/ console output
