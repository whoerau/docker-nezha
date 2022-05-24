FROM golang:alpine AS binarybuilder
RUN apk --no-cache --no-progress add \
    gcc git musl-dev

ENV GO111MODULE=on
ARG VERSION
WORKDIR /home

RUN git clone https://github.com/naiba/nezha.git &&\
    cd nezha && VERSION=$(git describe --tags `git rev-list --tags --max-count=1`) && ERSION=${VERSION:1} &&\
    git checkout $VERSION &&\
    cd cmd/agent && go build -o app -trimpath -ldflags="-s -w -X main.version=$ERSION"


FROM alpine:latest

RUN apk --no-cache --no-progress add \
    ca-certificates \
    tzdata
RUN ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime && echo 'Asia/Shanghai' >/etc/timezone

WORKDIR /agent
COPY --from=binarybuilder /home/nezha/cmd/agent/app ./app

USER nobody

ENTRYPOINT ["/agent/app", "--skip-procs", "--disable-command-execute", "--report-delay", "3"]
