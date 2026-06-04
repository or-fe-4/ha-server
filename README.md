# HA Server Lab

Nginxロードバランサーで2台のWebサーバーに分散させ、1台が落ちても
サービスが継続する高可用性構成を構築した。

## 構成

ブラウザ -> ロードバランサー(Nginx) -> web1(Nginx)
                                    -> web2(Nginx)

## 起動方法

docker compose up -d

## やったこと

### 意図的障害：web1を強制停止

docker compose stop web1

web1を止めた状態でcurlを実行すると、自動的にweb2だけに振り分けられることを確認。

### 自動復旧確認

docker compose start web1

web1が復活すると自動的に振り分けに戻ることを確認。

## ヘルスチェック設定

nginx.confにmax_failsとfail_timeoutを設定することで、
障害検知を高速化した。

- max_fails=1: 1回失敗したらそのサーバーを外す
- fail_timeout=5s: 5秒間そのサーバーへの振り分けを停止する
- proxy_connect_timeout 2s: 接続タイムアウトを2秒に短縮

## 学んだこと

- ロードバランサーは負荷分散だけでなく冗長化の役割もある
- 1台が落ちても自動でフェイルオーバーする仕組みが高可用性の基本
- Nginxのupstreamブロックで複数サーバーへの振り分けが簡単に設定できる

## 反省点
- nginx.confという命名はまずかったかも
