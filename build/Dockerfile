FROM ubuntu:focal
WORKDIR /trees
ARG DEBIAN_FRONTEND=noninteractive

# Install build tools.
RUN apt-get update && \
    apt-get --no-install-recommends install -y \
	apt-transport-https \
	apt-utils \
	build-essential \
	cmake \
	ca-certificates \
	git \
	ninja-build \
	python \
	python3 \
	xorg-dev \
	clang-7 \
	libc++-7-dev \
	libc++abi-7-dev

# Install additional tools that help with regression testing.
RUN apt-get --no-install-recommends install -y \
	imagemagick \
	perceptualdiff

# Ensure that clang is used instead of gcc.
RUN set -eux ;\
    update-alternatives --install /usr/bin/clang clang /usr/bin/clang-7 100 ;\
    update-alternatives --install /usr/bin/clang++ clang++ /usr/bin/clang++-7 100 ;\
    update-alternatives --install /usr/bin/cc cc /usr/bin/clang 100 ;\
    update-alternatives --install /usr/bin/c++ c++ /usr/bin/clang++ 100

# Clone and build SwiftShader.
RUN git clone https://swiftshader.googlesource.com/SwiftShader swiftshader
RUN set -eux ;\
    cd swiftshader ;\
    cd build ;\
    cmake .. -GNinja ;\
    ninja

# Point an environment variable to the resulting shared library.
ENV SWIFTSHADER_LD_LIBRARY_PATH=/trees/swiftshader/build
RUN echo $(ls -1 /trees/swiftshader/build)

# Clone and build Filament.
RUN git clone https://github.com/google/filament filament
ENV CXXFLAGS='-fno-builtin -Wno-pass-failed'
RUN cd filament && ./build.sh -t release
