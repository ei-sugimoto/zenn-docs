---
title: "自作のwebサーバを作成してみる"
emoji: "🐈"
type: "idea" # tech: 技術記事 / idea: アイデア
topics: ["go","docker","webサーバ"]
published: true
---

web サーバを自作して理解を深めるのと、go についてもっと触れたいと思ったため、作成に至りました。

リポジトリは以下です。

https://github.com/ei-sugimoto/ngonx

スターをいただけると喜びます。

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

2. tomlファイルで設定する。
以下のようにtomlファイルを作成することで、あるエンドポイント来たら指定されたサーバへ接続する。
```toml:example.toml
[server1]
host = "service1"
port = 80
endpoint = "/service1"

[server2]
host = "service2"
port = 80
endpoint = "/service2"
```
## 追加していきたい機能
1. ロードバランサを追加する
2. persistenceの設定項目の追加
