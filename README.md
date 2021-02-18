# docker-netcommons3
dockerでNetCommons3を動かすためのdocker-composeファイル

## 使い方
### NetCommonsの起動
```bash
docker-compose up -d
```
### NetCommonsのインストール
1. ブラウザで`http://localhost/`を開く
1. データベースサーバのホスト名を`localhost'から'db'に変更する。パスワードには'root'を指定する。
2. その他は公式サイトのインストール手順を参照して実行する。

### データベースでrootのパスワードを変更する場合
docker-compose.ymlのDBサービスenvironmentで変更する。
```yml
# docker-compose.yml

services:
  db:
    image: mysql:5.6
    volumes:
     - db_data:/var/lib/mysql
    environment:
-     MYSQL_ROOT_PASSWORD: root # ここを削除
+     MYSQL_ROOT_PASSWORD: 任意のパスワード # ここを変更する
      MYSQL_DATABASE: nc3
```

### データベースでroot以外のユーザを使用する場合
以下の設定をdocker-compose.ymlのDBサービスenvironmentに追加する。
```yml
# docker-compose.yml

services:
  db:
    image: mysql:5.6
    volumes:
     - db_data:/var/lib/mysql
    environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: nc3
+     MYSQL_USER: ユーザ名       # 追加
+     MYSQL_PASSWORD: バスワード # 追加
```

インストール画面でdocker-compose.ymlに追加したユーザ名（ID）とパスワードを入力する。

### SSLを使用する場合
1. サーバ証明書と秘密鍵を用意する。
1. Dockerfile、docker-compose.yml、ssl.confを修正する。
1. イメージのビルドを行う。

#### サーバ証明書と秘密鍵を用意する。
サーバ証明書と秘密鍵をホストの任意の場所に保存する。
開発環境で証明書を用意するには[mkcert](https://github.com/FiloSottile/mkcert)がおすすめ。

#### Dockerfileの修正
以下の設定を追記する。

```Dockerfile
# php/Dockerfile

# for SSL
COPY ssl.conf /etc/apache2/sites-available/ssl.conf
RUN a2enmod ssl && a2ensite ssl
```

#### docker-compose.ymlの修正
webサービスにvolumesの設定を追記し、ホストで証明書と秘密鍵を保存しているパスに変更する。

```yml
# docker-compose.yml

services:
  web:
  ...
    ### SSLを使用する場合は以下を有効にし、鍵ベアのあるローカルパスを指定する。 ###
+   volumes:
+    - /pass/to/your/keys:/etc/apache2/ssl/keys
```

#### ssl.confの修正
1. ssl.confのドメイン名を適切なものに修正する。
2. 証明書と秘密鍵のファイル名を実際のものに修正する。

```
# php/ssl.conf

<VirtualHost *:443>
    DocumentRoot /var/www/html

    # 適切なドメインに変更すること
+   ServerName your.domain
    SSLEngine on

    # 鍵ペアのファイル名を実際のものに変更する。
+   SSLCertificateFile /etc/apache2/ssl/keys/your_certfile.pem
+   SSLCertificateKeyFile /etc/apache2/ssl/keys/your_certfile-key.pem
</VirtualHost>
```

#### イメージのビルドを行う
```bash
docker-compose build
```


## 参考
[NetCommons公式サイト](https://www.netcommons.org/)
