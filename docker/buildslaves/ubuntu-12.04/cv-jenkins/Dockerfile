# Docker container for Ubuntu 12.04

# See https://github.com/ceejatec/naked-docker/ for details about the
# construction of the base image.

FROM ceejatec/ubuntu-1204-couchbase-build
MAINTAINER hari.kodungallur@couchbase.com

USER root

# Code coverage requires gcovr to convert from gcov to XML files consumable by
# Jenkins.
RUN apt-get update && \
    apt-get install -y python-pip && \
    pip install gcovr

# Valgrind needed for leak checking as part of unit tests. Note: It requires
# libc debug symbols (libc6-dbg) to ensure it can locate the address of strlen().
RUN mkdir /tmp/deploy && \
    cd /tmp/deploy && curl -L -O http://valgrind.org/downloads/valgrind-3.10.1.tar.bz2 && \
    tar -xjf valgrind-3.10.1.tar.bz2 && cd valgrind-3.10.1 && \
    ./configure --prefix=/usr/local && make && make install && \
    cd /tmp && rm -fr /tmp/deploy && \
    ccache --clear && \
    apt-get update &&  apt-get install -y libc6-dbg && \
    rm -f /var/cache/apt/archives/*.deb

# install easy_install and paramiko
RUN apt-get update && apt-get install -y python-setuptools && \
    rm -f /var/cache/apt/archives/*.deb && \
    easy_install paramiko

# Install Clang 3.6 (current stable) - needed for building with ThreadSanitizer.
# (Note: This doesn't change the default compiler; users must explicitly
# select clang-3.6 / clang++-3.6).
RUN wget -O - http://llvm.org/apt/llvm-snapshot.gpg.key | sudo apt-key add - && \
    echo "deb http://llvm.org/apt/precise/ llvm-toolchain-precise-3.6 main" > /etc/apt/sources.list.d/llvm.list && \
    apt-get update && \
    apt-get install --assume-yes clang-3.6 && \
    rm -fr /var/cache/apt/archives/*.deb

# Install GDB - needed for detecting what program created a core file
# & extracting the set of shared libraries.
RUN apt-get install -y gdb && \
    rm -f /var/cache/apt/archives/*.deb

# Install Lua - Needed for lua-based tests in Couchstore
RUN apt-get update && \
    apt-get install -y lua5.2 lua5.2-dev && \
    rm -fr /var/cache/apt/archives/*.deb
