FROM xfangfang/pacbrew:latest AS builder

MAINTAINER xfangfang <xfangfang@126.com>

RUN apt-get update && \
    apt-get install -y --no-install-recommends xz-utils lzma fakeroot file autoconf automake libtool yasm python3&& \
    adduser --gecos '' --disabled-password builder

RUN pacbrew-pacman -S --needed --noconfirm ps4-openorbis-zlib ps4-openorbis-bzip2 ps4-openorbis-libass ps4-openorbis-libfribidi ps4-openorbis-freetype

COPY musl/PKGBUILD /src/musl/PKGBUILD
COPY musl/dns.patch /src/musl/dns.patch
COPY SDL2/PKGBUILD /src/SDL2/PKGBUILD
COPY SDL2/sdl2.patch /src/SDL2/sdl2.patch
COPY mbedtls/PKGBUILD /src/mbedtls/PKGBUILD
COPY libcurl/PKGBUILD /src/libcurl/PKGBUILD
COPY ffmpeg/PKGBUILD /src/ffmpeg/PKGBUILD
COPY libmpv/PKGBUILD /src/libmpv/PKGBUILD
COPY libmpv/mpv.patch /src/libmpv/mpv.patch

RUN mkdir -p /pkgs && \
    chown -R builder:builder /src && \
    rm -rf /src/*/*.pkg.tar.xz

RUN su - builder -c "cd /src/musl && pacbrew-makepkg" && \
    pacbrew-pacman -U --noconfirm /src/musl/*.pkg.tar.xz
RUN su - builder -c "cd /src/SDL2 && pacbrew-makepkg" && \
    pacbrew-pacman -U --noconfirm /src/SDL2/*.pkg.tar.xz
RUN su - builder -c "cd /src/mbedtls && pacbrew-makepkg" && \
    pacbrew-pacman -U --noconfirm /src/mbedtls/*.pkg.tar.xz
RUN su - builder -c "cd /src/libcurl && pacbrew-makepkg" && \
    pacbrew-pacman -U --noconfirm /src/libcurl/*.pkg.tar.xz
RUN su - builder -c "cd /src/ffmpeg && pacbrew-makepkg" && \
    pacbrew-pacman -U --noconfirm /src/ffmpeg/*.pkg.tar.xz
RUN su - builder -c "cd /src/libmpv && pacbrew-makepkg" && \
    pacbrew-pacman -U --noconfirm /src/ffmpeg/*.pkg.tar.xz

RUN cp /src/*/*.pkg.tar.xz /pkgs

FROM xfangfang/pacbrew:latest

COPY --from=builder /pkgs /pkgs

RUN pacbrew-pacman -Syyu --noconfirm && \
    pacbrew-pacman -S --needed --noconfirm ps4-openorbis-libwebp && \
    pacbrew-pacman -U --noconfirm /pkgs/*.pkg.tar.xz && \
    rm -rf /pkgs && \
    yes | pacbrew-pacman -Scc

ENV PATH="/opt/pacbrew/ps4/openorbis/bin:/opt/pacbrew/ps4/openorbis/usr/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
ENV OPENORBIS="/opt/pacbrew/ps4/openorbis"