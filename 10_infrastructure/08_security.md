# security

## Dos攻撃

- Denial of Service attack／サービス拒否攻撃
- ウェブサイトやサーバーに対して過剰なアクセスやデータを送付するサイバー攻撃

## DDos攻撃

- Distributed Denial of Service
- 複数のコンピューターから一斉にサイバー(Dos)攻撃

## ブルートフォースアタック(総当たり攻撃)

全ての可能な組み合わせを総当たりで試し、正しい組み合わせを見つけることで、不正なアクセスを行うこと

- テストとしては以下のツールを使って再現可能
  - [gobuster](https://github.com/OJ/gobuster/pkgs/container/gobuster)
  - [seclist](https://github.com/danielmiessler/SecLists/tree/master)

```sh
# dir探索を目的とするオプション
# -u は任意のIPアドレスやホストを指定する
gobuster dir -u 0.0.0.x -w ワードリストパス
```

## 証明書

- [TLS](https://kinsta.com/jp/knowledgebase/tls-vs-ssl/)とは?
  - **T**ransport **L**ayer **S**ecurity が正式名称
  - TLSはSSLよりも新しく安全なバージョン
    - SSLプロトコルのセキュリティ脆弱性が修正されている
  - TLSはSSLを完全に置き換えている
  - SSL証明書と一般的には言っても実際には「SSL/TLS証明書」
  - SSLが廃止されてもSSL証明書という名称が残っている理由は、単に昔の名残

| SSLバージョン名 | リリース日 | 概要 | TLSバージョン名 | リリース日 | 概要 |
|-|-|-|-|-|-|
| 1.0 | X | リリースされず | 1.0 | 1999年 | SSL 3.0のアップグレードとしてリリース。2020年に廃止予定 |
| 2.0 | 1995年 | 既知のセキュリティホールが原因で2011年に廃止 | 1.1 | 2006年 | 2020年に廃止予定 |
| 3.0 | 1996年 | 既知のセキュリティホールが原因で2015年に廃止 | 1.2 | 2008年 |  |
| X | X | X | 1.3 | 2018年 |  |
