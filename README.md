# ansible-infra-ops-lab

Status: Portfolio draft / Role-based structure

## 概要

`ansible-infra-ops-lab` は、Linux サーバの構築と運用確認を Ansible で整理するポートフォリオ用リポジトリです。

VirtualBox 上の Rocky Linux 9 検証環境を想定し、以下の3つを分けて管理する構成にしています。

- 共通部: OS 初期設定、共通パッケージ、共通サービス
- 固有部: サーバ用途ごとに変わる設定
- 運用部: 構築後に確認したい状態確認

## 目的

単にパッケージを入れるだけでなく、以下を説明できる構成にすることを目的とします。

- 共通設定と用途別設定を分離する
- Playbook は役割の呼び出しに寄せ、処理本体は role にまとめる
- 構築 Playbook と確認 Playbook を分ける
- 実 inventory を公開せず、example だけを Git 管理する
- 構築後に人手で確認しがちな作業を Ansible で確認する

現在の実装例として、固有部には Web サーバ構成を置いています。Web サーバの具体的な内容は、コードと技術メモ側で説明する前提です。

## 想定環境

| 項目 | 内容 |
| --- | --- |
| Ansible 実行サーバ | Rocky Linux 9 または Ubuntu Server |
| 操作対象サーバ | Rocky Linux 9 |
| 仮想化環境 | VirtualBox |
| 接続方式 | SSH |
| 対象用途 | Linux サーバ構築・運用確認の検証 |

## 構成

```text
ansible-infra-ops-lab/
├── ansible.cfg
├── inventory/
│   ├── hosts.ini.example
│   └── group_vars/
│       ├── all.yml
│       └── web.yml
├── playbooks/
│   ├── site.yml
│   └── verify_web.yml
├── roles/
│   ├── common/
│   ├── web/
│   └── web_verify/
└── docs/
    ├── 00_environment.md
    ├── 01_ansible_manager_setup.md
    ├── 02_server_build.md
    ├── 03_operation_check.md
    └── 04_design_decisions.md
```

## ドキュメント

- [検証環境設計](docs/00_environment.md)
- [Ansible マネージャー構築](docs/01_ansible_manager_setup.md)
- [サーバー構築手順](docs/02_server_build.md)
- [運用確認手順](docs/03_operation_check.md)
- [設計判断](docs/04_design_decisions.md)

## 実行順序

Ansible 実行サーバーを準備します。

```text
docs/01_ansible_manager_setup.md
```

実 inventory を作成します。

```bash
cp inventory/hosts.ini.example inventory/hosts.ini
vi inventory/hosts.ini
```

疎通確認を行います。

```bash
ansible all -m ping
```

共通設定と用途別設定を実行します。

```bash
ansible-playbook playbooks/site.yml
```

構築後の運用確認を実行します。

```bash
ansible-playbook playbooks/verify_web.yml
```

## 実務寄りにした点

- `roles/common`、用途別 role、確認用 role に分け、処理の責務を明確にしています。
- `playbooks/site.yml` は構築、確認用 Playbook は構築後の状態確認に分けています。
- `group_vars/all.yml` には共通値、用途別の group_vars には個別値を置いています。
- 外部 collection に依存しないよう、初期版では `ansible.builtin` を中心にしています。
- `firewall-cmd` は標準モジュールだけで扱うため command を使い、事前確認で冪等性を保つ形にしています。

## セキュリティ上の注意

- `inventory/hosts.ini` は Git 管理しません。
- 実 IP、実ユーザー名、秘密鍵パス、パスワードは公開しません。
- SSH 鍵、証明書、環境変数ファイルは Git 管理対象外です。
- 本番環境への適用は想定していません。

## 今後の拡張候補

- SSH 設定の標準化
- 管理ユーザー作成
- sudo 設定
- Apache vhost 設定
- SSL/TLS 設定
- ログ確認 Playbook
- 監視エージェント導入
- ansible-lint 導入
