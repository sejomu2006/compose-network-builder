# compose-network-builder
このリポジトリは、 docker-compose.yml に記述するサービス構成（ネットワーク・サーバ・クライアント）を Python によって迅速に自動生成することを目的としています。
- ※このプロジェクトは学習目的で作成したものです。

---
# 自動化のために作成したputhonコード
以下のpythonコードは、for文、if文、print関数で構成されています。その後、以下のような処理を行います。
- ネットワークの分離についての選択
- サーバ数、クライアント数の指定、ネットワークの名前の決定
- 各サービスの`image` ・ `command` ・ `networks` の自動生成
- 末尾に `networks:` ブロックを自動挿入
を行っています。
## 注意事項
以下のコードで実現するサービス構成の中には、セキュリティに関する設定は入っていません。入れたい場合は各自で導入してください。
```bash
sep=input("ネットワークを内部と外部に分けますか？(y/n):")
vers=input("version:")
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



