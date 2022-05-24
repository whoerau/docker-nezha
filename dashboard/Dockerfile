FROM golang:alpine AS binarybuilder
RUN apk --no-cache --no-progress add \
    gcc git musl-dev

ENV GO111MODULE=on

WORKDIR /home
RUN git clone https://github.com/naiba/nezha.git && \
  cd nezha && latestTag=$(git describe --tags `git rev-list --tags --max-count=1`) && git checkout $latestTag && \
  cd cmd/dashboard && go build -o app -trimpath -ldflags="-s -w"


FROM alpine:latest

RUN apk --no-cache --no-progress add \
    ca-certificates \
    tzdata
RUN ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime && echo 'Asia/Shanghai' >/etc/timezone

WORKDIR /dashboard
COPY --from=binarybuilder /home/nezha/resource ./resource
COPY --from=binarybuilder /home/nezha/cmd/dashboard/app ./app

VOLUME ["/dashboard/data"]
EXPOSE 80 5555


CMD ["/dashboard/app"]
