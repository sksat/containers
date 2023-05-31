# Design Doc: sksat/containers

## 背景

- 雑なコンテナイメージを雑に作りたい
  - debian に curl 入れただけ，とか: [sksat/debian-curl-docker](https://github.com/sksat/debian-curl-docker)
- 1つの GitHub repo から複数の GitHub Package が作れる
  - 違う名前で ghcr.io に push できる
- 雑に multiarch 対応になってほしい
  - 雑にはできないやつも，都度少しの対応で済んでほしい

## 課題

- 雑なコンテナのメンテがダルい
  - 色々な dependency をちゃんと管理したい（ex: ちゃんと Renovate する）
  - リポジトリがたくさんあると Renovate の PR を見逃す
- コンテナイメージのビルドフローが色々ある
  - Dockerfile, buildx bake, Earthly など
- 複数のベースイメージでビルドしたいものを扱う reusable な仕組みがほしい
  - 複数の debian version でビルドしたい，みたいな workflow が reusable でない

## 概要

- 色々なコンテナイメージをビルドする monorepo として，[sksat/containers](https://github.com/sksat/containers) を作る
  - 複数のコンテナイメージをここでビルド・メンテする
  - 設定の共通化・Renovate などのメンテ箇所・通知の一元化
  - 他所の `Dockerfile` をセルフビルドなどにも使う
- Container Registry には ghcr.io を使用する
  - `ghcr.io/sksat/<image-name>` に push する
- 1つのサブディレクトリが1つのイメージに対応する
  - ディレクトリの命名規則: `<build-tool>-<prefix2>-<image-name>`
  - build-tool: コンテナイメージのビルドに使用するツール
  - prefix2: optional．Debian のベースイメージを振りたい時などに使う
  - image-name: push するコンテナイメージの名前

### ベースイメージ
- 1からイメージを作る場合は，できるだけ debian に寄せる
  - distroless でも構わないが，後で困る（シェルが無い）ので特に拘りが無ければ避ける

### build-tool: docker

- 単一の `Dockerfile` のとき
  - そのディレクトリを context としてそのまま `docker build`
- 複数の `Dockerfile` のとき
  - `Dockerfile.<platform>`

### build-tool: bake

- buildx bake

### build-tool: earthly

- Earthly
- `earthly ls` で target の一覧が取れるので，これの命名規則を定めて実行するものを決める
