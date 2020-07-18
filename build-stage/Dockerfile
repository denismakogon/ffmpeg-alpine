FROM        denismakogon/ffmpeg-alpine:4.0-dependencies AS build-stage
FROM        alpine:3.7
WORKDIR     /tmp/workdir

ARG        PKG_CONFIG_PATH=/usr/local/ffmpeg/pkgconfig
ARG        LD_LIBRARY_PATH=/usr/local/ffmpeg
ARG        PREFIX=/usr/local
ARG        MAKEFLAGS="-j2"

ENV        FFMPEG_VERSION=4.0

RUN     buildDeps="autoconf \
                   automake \
                   bash \
                   binutils \
                   bzip2 \
                   cmake \
                   curl \
                   coreutils \
                   diffutils \
                   expat-dev \
                   file \
                   g++ \
                   gcc \
                   gperf \
                   libtool \
                   make \
                   python \
                   openssl-dev \
                   tar \
                   yasm \
                   zlib-dev" && \
        apk  add --no-cache ${buildDeps} libgcc libstdc++ ca-certificates libcrypto1.0 libssl1.0

COPY --from=build-stage ${PREFIX}/bin ${PREFIX}/bin
COPY --from=build-stage ${PREFIX}/lib ${PREFIX}/lib
COPY --from=build-stage ${PREFIX}/share ${PREFIX}/share
COPY --from=build-stage ${PREFIX}/include ${PREFIX}/include


## ffmpeg https://ffmpeg.org/
RUN  \
        DIR=$(mktemp -d) && cd ${DIR} && \
        curl -sLO https://ffmpeg.org/releases/ffmpeg-${FFMPEG_VERSION}.tar.bz2 && \
        tar -jx --strip-components=1 -f ffmpeg-${FFMPEG_VERSION}.tar.bz2 && \
        ./configure \
        --disable-debug \
        --disable-doc \
        --disable-ffplay \
        --enable-shared \
        --enable-avresample \
        --enable-libopencore-amrnb \
        --enable-libopencore-amrwb \
        --enable-gpl \
        --enable-libass \
        --enable-libfreetype \
        --enable-libvidstab \
        --enable-libmfx \
        --enable-libmp3lame \
        --enable-libopenjpeg \
        --enable-libopus \
        --enable-libtheora \
        --enable-libvorbis \
        --enable-libvpx \
        --enable-libx265 \
        --enable-libxvid \
        --enable-libx264 \
        --enable-nonfree \
        --enable-openssl \
        --enable-libfdk_aac \
        --enable-libkvazaar \
        --enable-libaom --extra-libs=-lpthread \
        --enable-postproc \
        --enable-small \
        --enable-vaapi \
        --enable-version3 \
        --extra-cflags="-I${PREFIX}/include" \
        --extra-ldflags="-L${PREFIX}/lib" \
        --extra-libs=-ldl \
        --prefix="${PREFIX}" && \
        make && \
        make install && \
        make distclean

RUN \
        mkdir -p /tmp/fakeroot/lib  && \
        ldd ${PREFIX}/bin/ffmpeg | cut -d ' ' -f 3 | strings | xargs -I R cp R /tmp/fakeroot/lib/ && \
        for lib in /tmp/fakeroot/lib/*; do strip --strip-all $lib; done && \
        cp -r ${PREFIX}/bin /tmp/fakeroot/bin/ && \
        cp -r ${PREFIX}/share /tmp/fakeroot/share/ && \
        cp -r ${PREFIX}/include /tmp/fakeroot/include && \
        LD_LIBRARY_PATH=/tmp/fakeroot/lib /tmp/fakeroot/bin/ffmpeg -buildconf

RUN ls -la /tmp/fakeroot
#copy /bin
#copy /lib
#copy /include
#copy /share
