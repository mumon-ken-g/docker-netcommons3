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

    environment:
-     MYSQL_ROOT_PASSWORD: root # ここを削除
+     MYSQL_ROOT_PASSWORD: 任意のパスワード # ここを変更する
      MYSQL_DATABASE: nc3
```

### データベースでroot以外のユーザを使用する場合
以下の設定をdocker-compose.ymlのDBサービスenvironmentに追加する。
```yml
# docker-compose.yml

    environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: nc3
+      MYSQL_USER: ユーザ名       # 追加
+      MYSQL_PASSWORD: バスワード # 追加
```

インストール画面でdocker-compose.ymlに追加したユーザ名（ID）とパスワードを入力する。

NetCommons公式サイト
https://www.netcommons.org/
