# Ansible マネージャー構築

## 目的

このドキュメントでは、Ansible を実行する管理サーバーを用意し、操作対象サーバーへ接続確認するところまでを整理します。

このリポジトリでは、Ansible 実行サーバーを `ansible-control`、操作対象サーバーを `target server` として扱います。

## 前提

- Ansible 実行サーバーに SSH 接続できること
- 操作対象サーバーへ SSH 接続できるネットワークにいること
- 操作対象サーバー側で sudo または become が使えるユーザーを用意していること
- 実 IP、実ユーザー名、秘密鍵パスは Git 管理しないこと

## パッケージ導入

Rocky Linux 9 を Ansible 実行サーバーにする場合の例です。

```bash
sudo dnf install -y epel-release
sudo dnf install -y ansible-core git openssh-clients
```

Ubuntu Server を使う場合は、利用しているバージョンの公式リポジトリに合わせて `ansible` または `ansible-core` を導入します。

## リポジトリ取得

```bash
git clone https://github.com/kurumu-biz555/ansible-infra-ops-lab.git
cd ansible-infra-ops-lab
```

## inventory 作成

実接続情報は Git 管理しません。example をコピーして、ローカル用の `hosts.ini` を作成します。

```bash
cp inventory/hosts.ini.example inventory/hosts.ini
vi inventory/hosts.ini
```

`inventory/hosts.ini` には、操作対象サーバーの IP アドレス、接続ユーザー、秘密鍵パスなどを設定します。

公開リポジトリには、実 IP や実ユーザー名を含めません。

## SSH 接続確認

まず Ansible を使わず、SSH で接続できることを確認します。

```bash
ssh your_user@target_server_ip
```

接続できない場合は、Ansible ではなく SSH、名前解決、ネットワーク、ユーザー権限を先に確認します。

## Ansible 疎通確認

inventory を使って ping モジュールを実行します。

```bash
ansible all -m ping
```

成功すると、対象サーバーごとに `pong` が返ります。

## 確認ポイント

- `ansible --version` で Ansible が実行できる
- `ansible all -m ping` が成功する
- `inventory/hosts.ini` が Git 管理対象外になっている
- 秘密鍵やパスワードをリポジトリに置いていない

ここまで確認できたら、次に `02_server_build.md` の構築手順へ進みます。
