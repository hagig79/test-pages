---
title: FuelPHP 環境を Docker で構築する
---

## プロジェクト構成

構築するのは古典的な LAMP 環境。MySQL のみ別なコンテナにする。

1. Apache コンテナ
2. MySQL コンテナ

## FuelPHP プロジェクト作成

Composer を使い、FuelPHP プロジェクトを作成する。

```
$ composer create-project fuel/fuel fuel-docker --prefer-dist
```

## Docker Compose

複数のコンテナがあるので、Docker Compose をつかう。プロジェクトの直下に docker-compose.yml ファイルを作成する。

docker-compose.yml

```

```
