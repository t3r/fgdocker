#
# Dockerfile to build the FlightGear multiplayer server image
#

FROM alpine:latest AS builder
RUN apk add --update alpine-sdk cmake

COPY patches /patches

WORKDIR /fgms

ARG BRANCH="0.13.4"
ENV BRANCH=${BRANCH}

RUN git clone https://git.code.sf.net/p/fgms/src && \
    cd src && \
    git checkout ${BRANCH} && \
    git apply < /patches/0001-Fix-build-for-alpine-musl-libc.patch

WORKDIR /fgms/build
RUN cmake -DUSER_INSTALL_PREFIX=/usr/local -DCMAKE_BUILD_TYPE=Release ../src && \
    make && \
    make install

FROM alpine:latest
RUN apk add --update libstdc++ && \
    rm -rf /var/cache/apk/*

COPY --from=builder /usr/local/sbin/fgms /usr/local/sbin/fgms
COPY fgms.conf /usr/local/etc/fgms.conf

ENTRYPOINT [ "/usr/local/sbin/fgms" ]

EXPOSE 5000/udp
EXPOSE 5001
EXPOSE 5002
