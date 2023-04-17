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
  - リポジトリがたくさんあると Renovate の PR を見逃す
- コンテナイメージのビルドフローが色々ある
  - Dockerfile, buildx bake, Earthly など
- 複数のベースイメージでビルドしたいものを扱う reusable な仕組みがほしい
  - 複数の debian version でビルドしたい，みたいな workflow が reusable でない

## 概要

- 色々なコンテナイメージをビルドするリポジトリとして，[sksat/containers](https://github.com/sksat/containers) を作る
  - 複数のコンテナイメージをここでビルド・メンテする
  - 設定の共通化・Renovate などのメンテ箇所・通知の一元化
- 1つのサブディレクトリが1つのイメージに対応する
  - ディレクトリの命名規則: `<build-tool>-<prefix2>-<image-name>`
  - build-tool: コンテナイメージのビルドに使用するツール
  - prefix2: optional．Debian のベースイメージを振りたい時などに使う
  - image-name: コンテナイメージの名前．`ghcr.io/sksat/<image-name>` にイメージが push される

