---
title: "自作のwebサーバを作成してみる"
emoji: "🐈"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: []
published: false
---

web サーバを自作して理解を深めるのと、go についてもっと触れたいと思ったため、作成に至りました。

リポジトリは以下です。

https://github.com/ei-sugimoto/ngonx

## features

✅ 　 reverse proxy

## 工夫点

1. Dockerfile をマルチステージにし、超軽量に

全然機能がないため、超軽量です w

```Docker
FROM   golang:1.23.1-alpine AS builder

WORKDIR /app
EXPOSE 8080

RUN --mount=source=go.mod,target=go.mod \
    --mount=source=go.sum,target=go.sum \
    go mod download

RUN --mount=source=.,target=.\
    go build -o /go/bin/main .


FROM gcr.io/distroless/cc

COPY --from=builder /go/bin/main /go/bin/main

CMD ["/go/bin/main"]
```

以下の記事でも書かれているのですが、
https://zenn.dev/kariya_mitsuru/articles/4bd6e5c242f463

```Docker
RUN --mount=source=.,target=.\
    go build -o /go/bin/main .
```

このように、build したものは、current directory ではない場所に保存してください。

`mount=bind`は read-only になるそうです。
