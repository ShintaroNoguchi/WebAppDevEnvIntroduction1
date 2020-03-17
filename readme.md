# Webアプリケーション環境構築

## 事前準備

### Docker Toolbox

インストール参考
https://qiita.com/KIYS/items/8ac37f6757a6b7f84569


### Git for Windows

インストール＆設定参考
https://haniwaman.com/git-for-windows/

## 作業ディレクトリにLaradockをクローンする

作業ディレクトリ名をprojectとする。

```bash:/project
git init
git submodule add https://github.com/Laradock/laradock.git
```

## Laradockの初期設定

projectディレクトリ直下にsrcディレクトリを作成

```bash:/project
mkdir src
```

laradockディレクトリ内に.envファイルを作成

```bash:/project
cd laradock
```
```bash:/project/laradock
cp env-example .env
```

先程作成した.envファイル(設定ファイル)のAPP_CODE_PATH_HOSTの部分を書き換える。
これでproject/srcの下の階層がコードを格納するディレクトリとなる。

```bash:/project/laradock/.env
- APP_CODE_PATH_HOST=../
+ APP_CODE_PATH_HOST=../src
```

## Dockerでworkspaceを立ち上げる

laradockディレクトリ直下で以下のコマンドを実行

```bash:/project/laradock
$ docker-compose up -d workspace nginx
```

コンテナの起動状況を確認する。下記のようなコンテナ稼働状態になっていればOK。

```bash:/project/laradock
$ docker-compose ps
Name                          Command              State                     Ports
---------------------------------------------------------------------------------------------------------------
laradock_docker-in-docker_1   dockerd-entrypoint.sh           Up       2375/tcp
laradock_nginx_1              nginx                           Up       0.0.0.0:443->443/tcp, 0.0.0.0:80->80/tcp
laradock_php-fpm_1            docker-php-entrypoint php-fpm   Up       9000/tcp
laradock_workspace_1          /sbin/my_init                   Up       0.0.0.0:2222->22/tcp
```

## 公開領域のディレクトリを作成

srcディレクトリ直下に公開領域のディレクトリを作成。
公開領域のパスはデフォルトでは/var/www/publicとなっている。
変更する場合はproject/laradock/nginx/sites/defalt.confのrootを変更する。

```bash:/project/src
mkdir public
```

## 公開領域に公開したいファイルを作成

公開したいファイルを公開領域に作成する。

公開したいファイルにindex.php、index.html、index.htmのいずれかが含まれていると、それらはリクエストのURIが"/"で終わっている（つまりディレクトリになっている）ときにインデックスとして使われるファイルとなる。
インデックスとして使われるファイルの名前を変更したい場合はproject/laradock/nginx/sites/defalt.confのindexを変更する。

## 動作確認

ブラウザを開いて、Windowsであれば「http://192.168.99.100」に、Macであれば「http://localhost」にアクセスする。
公開したファイルが正しく表示できてれば成功。