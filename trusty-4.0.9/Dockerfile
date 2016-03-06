FROM buildpack-deps:trusty-curl
MAINTAINER Andrea Falco <fox91fox@gmail.com>

ENV BOOST_VERSION=1_53_0
ENV ECFLOW_VERSION=4.0.9

RUN persistentDeps=" \
        libx11-6 \
        libxm4 \
        libxpm4 \
        libxt6 \
    " \
    && buildDeps=" \
        cmake \
        make \
        g++ \
        python-dev \
        libx11-dev \
        libxt-dev \
        libxpm-dev \
        libmotif-dev \
    " \
    && set -x \
    && DEBIAN_FRONTEND=noninteractive apt-get update \
    && DEBIAN_FRONTEND=noninteractive apt-get install -y $persistentDeps $buildDeps --no-install-recommends \
    && mkdir /tmp/ecflow_build \
    && cd /tmp/ecflow_build \
    && curl -o boost_${BOOST_VERSION}.tar.gz -SL "https://software.ecmwf.int/wiki/download/attachments/8650755/boost_${BOOST_VERSION}.tar.gz?api=v2" \
    && curl -o ecFlow-${ECFLOW_VERSION}-Source.tar.gz -SL "https://software.ecmwf.int/wiki/download/attachments/8650755/ecFlow-${ECFLOW_VERSION}-Source.tar.gz?api=v2" \
    && tar -zxf boost_${BOOST_VERSION}.tar.gz \
    && tar -zxf ecFlow-${ECFLOW_VERSION}-Source.tar.gz \
    && export WK=/tmp/ecflow_build/ecFlow-${ECFLOW_VERSION}-Source \
    && export BOOST_ROOT=/tmp/ecflow_build/boost_${BOOST_VERSION} \
    && cd $BOOST_ROOT \
    && ./bootstrap.sh \
    && cd $BOOST_ROOT \
    && $WK/build_scripts/boost_1_53_fix.sh \
    && $WK/build_scripts/boost_build.sh \
    && cd $WK \
    && mkdir build \
    && cd build \
    && cmake .. \
    && export CPUS=$(lscpu -p | grep -v '#' | wc -l) \
    && export PYTHONPATH=$PYTHONPATH:/usr/local/${ECFLOW_VERSION}/lib/python2.7/site-packages/ecflow \
    && cd $WK/build \
    && make -j${CPUS} \
    && make check \
    && make install \
    && DEBIAN_FRONTEND=noninteractive apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false -o APT::AutoRemove::SuggestsImportant=false $buildDeps \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*
