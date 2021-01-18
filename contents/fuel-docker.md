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
version: "3"

services:
  php:
    build: ./docker/php
    ports:
      - 80:80
    volumes:
      - .:/var/www/html
  mysql:
    image: mysql:latest
    environment:
      MYSQL_DATABASE: mysql
      MYSQL_USER: mysql
      MYSQL_PASSWORD: mysql
      MYSQL_ROOT_PASSWORD: root
    ports:
      - 3306:3306
```

## PHP 用コンテナ

標準の PHP コンテナには MySQL のドライバが入っていないので、Dockerfile からビルドする。

docker/php/Dockerfile

```
FROM php:apache

RUN apt-get update \
    && docker-php-ext-install pdo_mysql

COPY ./000-default.conf /etc/apache2/sites-available/000-default.conf
```

## Apache 設定ファイル

FuelPHP は public ディレクトリに index.php が存在し、そこから他のディレクトリのソースコードを読み込んでいる。

それに対して、 Apache のデフォルトのドキュメントルートは/var/www/html なので、単純に public を html にマウントさせると他のソースコードを読み込むことができないので FuelPHP は動作しない。

これを解決するために FuelPHP のプロジェクトディレクトリを丸ごとマウントして、ドキュメントルートを public に変更する。

docker/php/000-default.conf

```
<VirtualHost *:80>
    DocumentRoot /var/www/html/public

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

## Docker Compose

コンテナを起動する。

```
$ docker-compose up
```

http://localhost/ にアクセスして FuelPHP の初期画面が表示されれば成功。

もしも logs ディレクトリがないというエラーが出たら、以下のようにコンテナに入って install タスクを実行する。

```
$ docker exec -it fuel-docker_php_1 /bin/bash
# cd /var/www/html
# php oil r install
```
