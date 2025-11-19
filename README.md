# compose-network-builder
このリポジトリは、 docker-compose.yml に記述するサービス構成（ネットワーク・サーバ・クライアント）を Python によって迅速に自動生成することを目的としています。
- ※このプロジェクトは学習目的で作成したものです。

## このツールを作成した理由
docker-compose.yml にサービス構成を記述する際の手間を省くために、このツールを作成しました。
---
# 自動化のために作成したpythonコード
以下のpythonコードは、for文、if文、print関数で構成されています。その後、以下のような処理を行います。
- ネットワークの分離についての選択
- サーバ数、クライアント数の指定、ネットワークの名前の決定
- 各サービスの`image` ・ `command` ・ `networks` の自動生成
- 末尾に `networks:` ブロックを自動挿入
を行っています。
## 注意事項
以下のコードで実現するサービス構成の中には、セキュリティに関する設定は入っていません。入れたい場合は各自で導入してください。
```python
sep=input("ネットワークを内部と外部に分けますか？(y/n):")
vers=input("versionを入力:")
if sep=='y':
    inter_network=input("内部で使用するネットワーク名:")
    DMZ_network=input("DMZで使用するネットワーク名:")
    inter_serv=int(input("内部ネットワークで必要なサーバ数:"))
    DMZ_serv=int(input("外部ネットワークで必要なサーバー数(webサーバーとは別):"))
    DMZ_webserv=int(input("外部ネットワークで必要なwebサーバー数:"))
    inter_cli=int(input("内部ネットワークで必要なクライアント数:"))
    DMZ_cli=int(input("外部ネットワークで必要なクライアント数:"))
    print("version:"+"'"+vers+"'")
    print("services:")
    for i in range(inter_serv):
        print("  "+"inter-serv"+str(i)+':')
        print("    "+"image: ubuntu")
        print("    "+"command: tail -f /dev/null")
        print("    "+"networks:")
        print("      "+"- "+inter_network)
    for i in range(inter_cli):
        print("  "+"inter-client"+str(i)+':')
        print("    "+"image: alpine")
        print("    "+"command: tail -f /dev/null")
        print("    "+"networks:")
        print("      "+"- "+inter_network)
            #下の2つのfor文はDMZ
    for i in range(DMZ_serv):
        print("  "+"DMZ-serv"+str(i)+':')
        print("    "+"image: ubuntu")
        print("    "+"command: tail -f /dev/null")
        print("    "+"networks:")
        print("      "+"- "+DMZ_network)
    for i in range(DMZ_webserv):
        print("  "+"DMZ-webserv"+str(i)+':')
        print("    "+"image: nginx:latest")
        print("    "+"command: tail -f /dev/null")
        print("    "+"networks:")
        print("      "+"- "+DMZ_network)
    for i in range(DMZ_cli):
        print("  "+"DMZ-client"+str(i)+':')
        print("    "+"image: alpine")
        print("    "+"command: tail -f /dev/null")
        print("    "+"networks:")
        print("      "+"- "+DMZ_network)
elif sep=='n':
    network1=input("ネットワーク名:")
    serv1=int(input("ネットワークで必要なサーバ数:"))
    cli1=int(input("ネットワークで必要なクライアント数:"))
    print("version:"+"'"+vers+"'")
    print("services:")
    for i in range(serv1):
        print("  "+"serv"+str(i)+':')
        print("    "+"image: ubuntu")
        print("    "+"command: tail -f /dev/null")
        print("    "+"networks:")
        print("      "+"- "+network1)
    for i in range(cli1):
        print("  "+"client"+str(i)+':')
        print("    "+"image: alpine")
        print("    "+"command: tail -f /dev/null")
        print("    "+"networks:")
        print("      "+"- "+network1)
              #以下はネットワーク名一覧
if sep=='y':
    print("networks:")
    print("  "+DMZ_network+":")
    print("    "+"driver: bridge")
    print("  "+inter_network+":")
    print("    "+"driver: bridge")
elif sep=='n':
    print("networks:")
    print("  "+network1+":")
    print("    "+"driver: bridge")

```

## このコードを使用することで得られるdocker-compose.ymlのサンプル
# 以下の条件で実行する
- ネットワークは外部ネットワークと内部ネットワークに分ける
- versionは3にする。

- 内部ネットワーク名をinternalとする
- DMZの名前をouterとする
- 内部ネットワークのサービスの数を以下の通りにする
  
  |サービス名|数|
  |------|------|
  |サーバー|1個|
  |クライアント|2個|
- 外部ネットワークのサービスの数を以下の通りにする

  | サービス名 | 数 |
  |------|------|
  |サーバー|1個|
  |webサーバー|1個|
  |クライアント|2個|

# 以下は次実行結果である


```python
# 入力

ネットワークを内部と外部に分けますか？(y/n):y
versionを入力:3
内部で使用するネットワーク名:internal
DMZで使用するネットワーク名:outer
内部ネットワークで必要なサーバ数:1
外部ネットワークで必要なサーバー数(webサーバーとは別):1
外部ネットワークで必要なwebサーバー数:1
内部ネットワークで必要なクライアント数:2
外部ネットワークで必要なクライアント数:2

#  以下が出力内容

version:'3'
services:
  inter-serv0:
    image: ubuntu
    command: tail -f /dev/null
    networks:
      - internal
  inter-client0:
    image: alpine
    command: tail -f /dev/null
    networks:
      - internal
  inter-client1:
    image: alpine
    command: tail -f /dev/null
    networks:
      - internal
  DMZ-serv0:
    image: ubuntu
    command: tail -f /dev/null
    networks:
      - outer
  DMZ-webserv0:
    image: nginx:latest
    command: tail -f /dev/null
    networks:
      - outer
  DMZ-client0:
    image: alpine
    command: tail -f /dev/null
    networks:
      - outer
  DMZ-client1:
    image: alpine
    command: tail -f /dev/null
    networks:
      - outer
networks:
  outer:
    driver: bridge
  internal:
    driver: bridge

```

## 今後の改善案
自動化ツールを作成したが、ユーザーがネットワークを構築する際の自由度が低い、セキュリティ設定がない、などの問題が生まれた。
そこで、ユーザーがimageなどの情報を選択できるようにする、ファイアウォールを作成可能なツールを新規で作成し、このツールと連携できるようにするといった改善が出来ると考えた。



