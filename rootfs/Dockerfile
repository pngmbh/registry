FROM quay.io/deis/go-dev:0.20.0 as go-build

WORKDIR /go/src/github.com/deis/registry
COPY . ./
RUN make build DEV_ENV_CMD= BINDIR=/opt

FROM registry:2.6.2

RUN apk add --no-cache \
        python3 && \
    python3 -m ensurepip && \
    ln -sf /usr/bin/python3 /usr/bin/python && \
    ln -sf /usr/bin/pip3 /usr/bin/pip

RUN buildDeps='gcc git linux-headers musl-dev python3-dev' && \
    apk add --no-cache $buildDeps && \
    # "upgrade" boto to 2.43.0 + the patch to fix minio connections
    pip install --disable-pip-version-check --no-cache-dir --upgrade \
        git+https://github.com/teamhephy/boto@abb38474ee5124bb571da0c42be67cd27c47094f \
        azure==1.0.3 \
        gcloud==0.18.3 \
        python-swiftclient==3.1.0 \
        python-keystoneclient==3.1.0 && \
    # purge dev dependencies
    apk del $buildDeps

COPY rootfs/ /
COPY --from=go-build /opt/registry /opt/registry/sbin/registry

VOLUME ["/var/lib/registry"]
CMD ["/opt/registry/sbin/registry"]
EXPOSE 5000
