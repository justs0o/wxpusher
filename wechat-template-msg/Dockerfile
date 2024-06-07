# syntax=docker/dockerfile:1
FROM golang:1.18-alpine AS builder

ARG GOPROXY=https://proxy.golang.org

# 安装必要的依赖
RUN apk add --no-cache git

WORKDIR /wechat-template-msg

# 仅复制所需的文件
COPY go.mod go.sum ./
RUN go mod download

COPY . .

RUN go mod tidy

RUN go build -o wechat-template-msg

# 使用最新的 Alpine 镜像并添加证书
FROM alpine:3.15

# 添加非 root 用户
RUN adduser -D -u 1000 nonroot

# 安装必要的证书
RUN apk add --no-cache ca-certificates

WORKDIR /wechat-template-msg

# 从构建阶段复制可执行文件
COPY --from=builder /wechat-template-msg/wechat-template-msg /wechat-template-msg/

# 以非 root 用户运行
USER nonroot:nonroot

# 指定默认的命令
CMD ["./wechat-template-msg", "consumer"]
