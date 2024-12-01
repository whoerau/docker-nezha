FROM golang:alpine AS binarybuilder
RUN apk --no-cache --no-progress add \
    gcc git musl-dev sqlite-dev

ENV GO111MODULE=on

WORKDIR /home
RUN git clone https://github.com/nezhahq/nezha.git && \
  cd nezha && latestTag=$(git describe --tags `git rev-list --tags --max-count=1`) && git checkout $latestTag && \
  cd cmd/dashboard && go mod tidy && go build -o app -trimpath -ldflags="-s -w -X main.version=${latestTag:1} -X github.com/nezhahq/nezha/service/singleton.Version=${latestTag:1} --extldflags '-static -fpic' " 
# RUN git clone https://github.com/nezhahq/nezha.git && \
#   cd nezha && \
#   cd cmd/dashboard && go build -o app -trimpath -ldflags="-s -w -X main.version=latest -X github.com/naiba/nezha/service/singleton.Version=${latestTag:1}"


FROM alpine:3.19

RUN apk --no-cache --no-progress add \
    ca-certificates \
    tzdata

ENV TZ="Asia/Shanghai"
RUN cp "/usr/share/zoneinfo/$TZ" /etc/localtime && echo "$TZ" > /etc/timezone 

WORKDIR /dashboard
COPY --from=binarybuilder /home/nezha/resource ./resource
COPY --from=binarybuilder /home/nezha/cmd/dashboard/app ./app

VOLUME ["/dashboard/data"]
EXPOSE 80 5555


CMD ["/dashboard/app"]
