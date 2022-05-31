FROM golang:alpine AS binarybuilder
RUN apk --no-cache --no-progress add \
    gcc git musl-dev

ENV GO111MODULE=on
ENV CGO_ENABLED=0
ARG version
ARG architecture
WORKDIR /home

RUN case $(uname -m) in \
        i386)   architecture="386" ;; \
        i686)   architecture="386" ;; \
        x86_64) architecture="amd64" ;; \
        arm)    dpkg --print-architecture | grep -q "arm64" && architecture="arm64" || architecture="arm" ;; \
    esac

RUN git clone https://github.com/naiba/nezha.git &&\
    cd nezha && version=$(git describe --tags `git rev-list --tags --max-count=1`) &&\
    git checkout $version &&\
    cd cmd/agent && go build -o nezha-agent -trimpath -ldflags="-s -w -X main.version=${version:1} -X main.arch=$architecture"


FROM alpine:latest

RUN apk --no-cache --no-progress add \
    ca-certificates \
    tzdata
ENV TZ="Asia/Shanghai"
RUN cp "/usr/share/zoneinfo/$TZ" /etc/localtime && echo "$TZ" > /etc/timezone 

WORKDIR /agent
COPY --from=binarybuilder /home/nezha/cmd/agent/nezha-agent ./nezha-agent

USER nobody

ENTRYPOINT ["/agent/nezha-agent", "--skip-procs", "--disable-command-execute", "--report-delay", "3"]
