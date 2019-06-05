FROM        ubuntu:18.04 AS base

WORKDIR     /tmp/workdir

RUN     apt-get -yqq update && \
        apt-get install -yq --no-install-recommends ca-certificates expat libgomp1 libuuid1 libssl1.0  && \
        apt-get autoremove -y && \
        apt-get clean -y

FROM base as build

ARG PREFIX=/tmp/build 
ARG MAKEFLAGS=-j6
ARG PKG_CONFIG_PATH=/tmp/build/lib/pkgconfig

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
ARG	LIBOGG_VERSION=1.3.3
ARG	LIBOPUS_VERSION=1.3.1
ARG	LIBVORBIS_VERSION=1.3.6
ARG	LIBTHEORA_VERSION=1.1.1
ARG	LIBVPX_VERSION=1.8.0
ARG	LIBFREETYPE_VERSION=2.10.0
ARG	LIBFRIBIDI_VERSION=0.19.7
ARG	LIBFONTCONFIG_VERSION=2.13.1
ARG	LIBASS_VERSION=0.14.0
ARG	LIBAOM_VERSION=1.0.0-errata1
ARG	LIBXVID_VERSION=1.3.5
ARG	LIBX265_VERSION=3.0
ARG	LIBX264_VERSION=20190526-2245-stable
ARG	LIBOPENJPEG_VERSION=2.3.1
ARG	LIBOPENCORE_AMR_VERSION=0.1.5
ARG	LIBKVAZAAR_VERSION=1.2.0
ARG	LIBFDK_AAC_VERSION=0.1.6
ARG	LIBMP3LAME_VERSION=3.100
ARG	LIBVIDSTAB_VERSION=1.1.0

ARG	LIBOGG_SHA256SUM=c2e8a485110b97550f453226ec644ebac6cb29d1caef2902c007edab4308d985
ARG	LIBOPUS_SHA256SUM=65b58e1e25b2a114157014736a3d9dfeaad8d41be1c8179866f144a2fb44ff9d
ARG	LIBVORBIS_SHA256SUM=6ed40e0241089a42c48604dc00e362beee00036af2d8b3f46338031c9e0351cb
ARG	LIBTHEORA_SHA256SUM=40952956c47811928d1e7922cda3bc1f427eb75680c3c37249c91e949054916b
ARG	LIBFRIBIDI_SHA256SUM=3fc96fa9473bd31dcb5500bdf1aa78b337ba13eb8c301e7c28923fea982453a8
ARG	LIBVIDSTAB_SHA256SUM=14d2a053e56edad4f397be0cb3ef8eb1ec3150404ce99a426c4eb641861dc0bb

## libogg https://www.xiph.org/ogg/
RUN \
    DIR=/tmp/ogg && \
    mkdir -p ${DIR} && \
    cd ${DIR} && \
    curl -sLO http://downloads.xiph.org/releases/ogg/libogg-${LIBOGG_VERSION}.tar.gz && \
    echo "${LIBOGG_SHA256SUM}  libogg-${LIBOGG_VERSION}.tar.gz" | sha256sum --check && \
    tar -zx --strip-components=1 -f libogg-${LIBOGG_VERSION}.tar.gz && \
    ./configure --prefix="${PREFIX}" --enable-shared  && \
    make && \
    make install && \
    rm -rf ${DIR}
RUN \
    DIR=/tmp/opus && \
    mkdir -p ${DIR} && \
    cd ${DIR} && \
    curl -sLO https://archive.mozilla.org/pub/opus/opus-${LIBOPUS_VERSION}.tar.gz && \
    echo "${LIBOPUS_SHA256SUM}  opus-${LIBOPUS_VERSION}.tar.gz"| sha256sum --check && \
    tar -zx --strip-components=1 -f opus-${LIBOPUS_VERSION}.tar.gz && \
    autoreconf -fiv && \
    ./configure --prefix="${PREFIX}" --enable-shared && \
    make && \
    make install && \
    rm -rf ${DIR}
### libvorbis https://xiph.org/vorbis/
RUN \
    DIR=/tmp/vorbis && \
    mkdir -p ${DIR} && \
    cd ${DIR} && \
    curl -sLO http://downloads.xiph.org/releases/vorbis/libvorbis-${LIBVORBIS_VERSION}.tar.gz && \
    echo "${LIBVORBIS_SHA256SUM}  libvorbis-${LIBVORBIS_VERSION}.tar.gz" | sha256sum --check && \
    tar -zx --strip-components=1 -f libvorbis-${LIBVORBIS_VERSION}.tar.gz && \
    ./configure --prefix="${PREFIX}" --with-ogg="${PREFIX}" --enable-shared && \
    make && \
    make install && \
    rm -rf ${DIR}
### libtheora http://www.theora.org/
RUN \
        DIR=/tmp/theora && \
        mkdir -p ${DIR} && \
        cd ${DIR} && \
        curl -sLO http://downloads.xiph.org/releases/theora/libtheora-${LIBTHEORA_VERSION}.tar.gz && \
        echo "${LIBTHEORA_SHA256SUM}  libtheora-${LIBTHEORA_VERSION}.tar.gz" | sha256sum --check && \
        tar -zx --strip-components=1 -f libtheora-${LIBTHEORA_VERSION}.tar.gz && \
        ./configure --prefix="${PREFIX}" --with-ogg="${PREFIX}" --enable-shared && \
        make && \
        make install && \
        rm -rf ${DIR}
### libvpx https://www.webmproject.org/code/
RUN \
    DIR=/tmp/vpx && \
    mkdir -p ${DIR} && \
    cd ${DIR} && \
    curl -sL https://codeload.github.com/webmproject/libvpx/tar.gz/v${LIBVPX_VERSION} | \
    tar -zx --strip-components=1 && \
    ./configure --prefix="${PREFIX}" --enable-vp8 --enable-vp9 --enable-vp9-highbitdepth --enable-pic --enable-shared \
    --disable-debug --disable-examples --disable-docs && \
    make && \
    make install && \
    rm -rf ${DIR}

## freetype https://www.freetype.org/
RUN  \
    DIR=/tmp/freetype && \
    mkdir -p ${DIR} && \
    cd ${DIR} && \
    curl -sLO https://download.savannah.gnu.org/releases/freetype/freetype-${LIBFREETYPE_VERSION}.tar.gz && \
    tar -zx --strip-components=1 -f freetype-${LIBFREETYPE_VERSION}.tar.gz && \
    ./configure --prefix="${PREFIX}" --disable-static --enable-shared && \
    make && \
    make install && \
    rm -rf ${DIR}
## fridibi https://www.fribidi.org/
# + https://github.com/fribidi/fribidi/issues/8
RUN  \
    DIR=/tmp/fribidi && \
    mkdir -p ${DIR} && \
    cd ${DIR} && \
    curl -sLO https://github.com/fribidi/fribidi/archive/${LIBFRIBIDI_VERSION}.tar.gz && \
    echo "${LIBFRIBIDI_SHA256SUM}  ${LIBFRIBIDI_VERSION}.tar.gz" | sha256sum --check && \
    tar -zx --strip-components=1 -f ${LIBFRIBIDI_VERSION}.tar.gz && \
    sed -i 's/^SUBDIRS =.*/SUBDIRS=gen.tab charset lib/' Makefile.am && \
    ./bootstrap --no-config && \
    ./configure -prefix="${PREFIX}" --disable-static --enable-shared && \
    make -j 1 && \
    make install && \
    rm -rf ${DIR}
## fontconfig https://www.freedesktop.org/wiki/Software/fontconfig/
RUN  \
    DIR=/tmp/fontconfig && \
    mkdir -p ${DIR} && \
    cd ${DIR} && \
    curl -sLO https://www.freedesktop.org/software/fontconfig/release/fontconfig-${LIBFONTCONFIG_VERSION}.tar.bz2 &&\
    tar -jx --strip-components=1 -f fontconfig-${LIBFONTCONFIG_VERSION}.tar.bz2 && \
    ./configure -prefix="${PREFIX}" --disable-static --enable-shared && \
    make && \
    make install && \
    rm -rf ${DIR}
RUN  \
    DIR=/tmp/libass && \
    mkdir -p ${DIR} && \
    cd ${DIR} && \
    curl -sLO https://github.com/libass/libass/archive/${LIBASS_VERSION}.tar.gz &&\
    tar -zx --strip-components=1 -f ${LIBASS_VERSION}.tar.gz && \
    ./autogen.sh && \
    ./configure -prefix="${PREFIX}" --disable-static --enable-shared && \
    make && \
    make install && \
    rm -rf ${DIR}
RUN \
	DIR=/tmp/aom && \
    git clone --branch v${LIBAOM_VERSION} --depth 1 https://aomedia.googlesource.com/aom ${DIR} ; \
    cd ${DIR} ; \
    rm -rf CMakeCache.txt CMakeFiles ; \
    mkdir -p ./aom_build ; \
    cd ./aom_build ; \
    cmake -DCMAKE_INSTALL_PREFIX="${PREFIX}" -DBUILD_SHARED_LIBS=1 ..; \
    make ; \
    make install ; \
    rm -rf ${DIR}
### xvid https://www.xvid.com/
RUN \
        DIR=/tmp/xvid && \
        mkdir -p ${DIR} && \
        cd ${DIR} && \
        curl -sLO http://downloads.xvid.org/downloads/xvidcore-${LIBXVID_VERSION}.tar.gz && \
        tar -zx -f xvidcore-${LIBXVID_VERSION}.tar.gz && \
        cd xvidcore/build/generic && \
        ./configure --prefix="${PREFIX}" --bindir="${PREFIX}/bin" --datadir="${DIR}" --enable-shared --enable-shared && \
        make && \
        make install && \
        rm -rf ${DIR}
### x265 http://x265.org/
RUN \
    DIR=/tmp/x265 && \
    mkdir -p ${DIR} && \
    cd ${DIR} && \
    curl -sL https://download.videolan.org/pub/videolan/x265/x265_${LIBX265_VERSION}.tar.gz  | \
    tar -zx && \
    cd x265_${LIBX265_VERSION}/build/linux && \
    sed -i "/-DEXTRA_LIB/ s/$/ -DCMAKE_INSTALL_PREFIX=\${PREFIX}/" multilib.sh && \
    sed -i "/^cmake/ s/$/ -DENABLE_CLI=ON/" multilib.sh && \
    ./multilib.sh && \
    make -C 8bit install && \
    rm -rf ${DIR}
## x264 http://www.videolan.org/developers/x264.html
RUN \
    DIR=/tmp/x264 && \
    mkdir -p ${DIR} && \
    cd ${DIR} && \
    curl -sL https://download.videolan.org/pub/videolan/x264/snapshots/x264-snapshot-${LIBX264_VERSION}.tar.bz2 | \
    tar -jx --strip-components=1 && \
    ./configure --prefix="${PREFIX}" --enable-shared --enable-pic && \
    make && \
    make install && \
    rm -rf ${DIR}
## openjpeg https://github.com/uclouvain/openjpeg
RUN \
        DIR=/tmp/openjpeg && \
        mkdir -p ${DIR} && \
        cd ${DIR} && \
        if test `echo ${LIBOPENJPEG_VERSION} | tr -d '.'` -ge '210'; then \
                version_prefix='v'; \
        else \
                version_prefix='version.' ;\
        fi && \
        curl -sL https://github.com/uclouvain/openjpeg/archive/${version_prefix}${LIBOPENJPEG_VERSION}.tar.gz | \
        tar -zx --strip-components=1 && \
        cmake -DBUILD_THIRDPARTY:BOOL=ON -DCMAKE_INSTALL_PREFIX="${PREFIX}" . && \
        make && \
        make install && \
        rm -rf ${DIR}
## opencore-amr https://sourceforge.net/projects/opencore-amr/
RUN \
    DIR=/tmp/opencore-amr && \
    mkdir -p ${DIR} && \
    cd ${DIR} && \ 
    curl -sL https://kent.dl.sourceforge.net/project/opencore-amr/opencore-amr/opencore-amr-${LIBOPENCORE_AMR_VERSION}.tar.gz | \
    tar -zx --strip-components=1 && \
    ./configure --prefix="${PREFIX}" --enable-shared && \
    make && \
    make install && \
    rm -rf ${DIR}
## kvazaar https://github.com/ultravideo/kvazaar
RUN \
    DIR=/tmp/kvazaar && \
    mkdir -p ${DIR} && \
    cd ${DIR} && \
    curl -sLO https://github.com/ultravideo/kvazaar/archive/v${LIBKVAZAAR_VERSION}.tar.gz &&\
    tar -zx --strip-components=1 -f v${LIBKVAZAAR_VERSION}.tar.gz && \
    if [ -e ./autogen.sh ]; then \
        ./autogen.sh && \
        ./configure -prefix="${PREFIX}" --disable-static --enable-shared ; \
    else \
        cd src ; \
    fi && \
    make && \
    make install && \
    rm -rf ${DIR}
### fdk-aac https://github.com/mstorsjo/fdk-aac
RUN \
        DIR=/tmp/fdk-aac && \
        mkdir -p ${DIR} && \
        cd ${DIR} && \
        curl -sL https://github.com/mstorsjo/fdk-aac/archive/v${LIBFDK_AAC_VERSION}.tar.gz | \
        tar -zx --strip-components=1 && \
        autoreconf -fiv && \
        ./configure --prefix="${PREFIX}" --enable-shared --datadir="${DIR}" && \
        make && \
        make install && \
        rm -rf ${DIR}
### libmp3lame http://lame.sourceforge.net/
RUN \
    DIR=/tmp/lame && \
    mkdir -p ${DIR} && \
    cd ${DIR} && \
    curl -sL https://kent.dl.sourceforge.net/project/lame/lame/$(echo ${LIBMP3LAME_VERSION} | sed -e 's/[^0-9]*\([0-9]*\)[.]\([0-9]*\)[.]\([0-9]*\)\([0-9A-Za-z-]*\)/\1.\2/')/lame-${LIBMP3LAME_VERSION}.tar.gz | \
    tar -zx --strip-components=1 && \
    ./configure --prefix="${PREFIX}" --bindir="${PREFIX}/bin" --enable-shared --enable-nasm --enable-pic && \
    make && \
    make install && \
    rm -rf ${DIR}
## libvstab https://github.com/georgmartius/vid.stab
RUN  \
    DIR=/tmp/vid.stab && \
    mkdir -p ${DIR} && \
    cd ${DIR} && \
    curl -sLO https://github.com/georgmartius/vid.stab/archive/v${LIBVIDSTAB_VERSION}.tar.gz &&\
    echo "${LIBVIDSTAB_SHA256SUM}  v${LIBVIDSTAB_VERSION}.tar.gz" | sha256sum --check && \
    tar -zx --strip-components=1 -f v${LIBVIDSTAB_VERSION}.tar.gz && \
    cmake -DCMAKE_INSTALL_PREFIX="${PREFIX}" . && \
    make && \
    make install && \
    rm -rf ${DIR}
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
        --enable-libopus \
	--enable-libvorbis \
	--enable-libtheora \
	--enable-libvpx \
	--enable-libass \
	--enable-libaom \
	--extra-libs=-lpthread \
	--enable-libxvid \
	--enable-libx265 \
	--enable-libx264 \
	--enable-libopenjpeg \
	--enable-libopencore-amrnb \
	--enable-libopencore-amrwb \
	--enable-version3 \
	--enable-libkvazaar \
	--enable-libfreetype \
	--enable-libfdk_aac \
	--enable-nonfree \
	--enable-libmp3lame \
	--enable-libvidstab \
	--enable-gpl \
        --prefix="${PREFIX}" && \
        make && \
        make install && \
        make distclean && \
        hash -r && \
        cd tools && \
        make qt-faststart && \
        cp qt-faststart ${PREFIX}/bin

RUN \
    if false; then \
        cp -r ${PREFIX}/lib/*so* /usr/local/lib/ && \
        cp ${PREFIX}/bin/* /usr/local/bin/ ; \
    else \
        LD_LIBRARY_PATH=${PREFIX}/lib ldd ${PREFIX}/bin/ffmpeg | \
        grep ${PREFIX} | \
        cut -d ' ' -f 3 | \
        xargs -i cp {} /usr/local/lib/ && \
        cp ${PREFIX}/bin/ff* /usr/local/bin/ ; \
    fi && \
    if true; then \
        for lib in /usr/local/lib/*.so* /usr/local/bin/*; do strip --strip-all $lib; done ; \
    fi && \
    cp -r ${PREFIX}/share/ffmpeg /usr/local/share/ && \
    LD_LIBRARY_PATH=/usr/local/lib ffmpeg -buildconf

FROM        base AS release
MAINTAINER  Julien Rottenberg <julien@rottenberg.info>

CMD         ["--help"]
ENTRYPOINT  ["ffmpeg"]

COPY --from=build /usr/local /usr/local
RUN test "base" = "base" && ldconfig

# Let's make sure the app built correctly
# Convenient to verify on https://hub.docker.com/r/jrottenberg/ffmpeg/builds/ console output
