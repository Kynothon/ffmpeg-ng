FROM alpine:3.9 as base

RUN apk add --no-cache --update  \
                                libgcc \
                                libstdc++ \
                                ca-certificates \
                                libcrypto1.1 \
                                libssl1.1 \
                                libgomp \
                                expat \
                                libuuid

FROM base as build

ARG PREFIX=/tmp/build 
ARG MAKEFLAGS=-j6
ARG PKG_CONFIG_PATH=/tmp/build/lib/pkgconfig

RUN apk add --no-cache --update  \
                                autoconf \
                                automake \
                                bash \
                                binutils \
                                bzip2 \
                                cmake \
                                curl \
                                coreutils \
                                diffutils \
                                file \
                                g++ \
                                git \
                                gcc \
                                gperf \
                                libtool \
                                make \
                                python \
                                openssl-dev \
                                tar \
                                yasm \
                                nasm \
                                zlib-dev \
                                expat-dev \
                                util-linux-dev

ARG	FFMPEG_VERSION=3.2.14


RUN \
        DIR=/tmp/ffmpeg && mkdir -p ${DIR} && cd ${DIR} && \
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
        --extra-cflags="-I${PREFIX}/include" \
        --extra-ldflags="-L${PREFIX}/lib" \
         \
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
