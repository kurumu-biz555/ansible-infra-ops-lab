# ansible-infra-ops-lab

Status: Portfolio draft / Role-based structure

## 概要

`ansible-infra-ops-lab` は、社内イントラ環境を想定した Linux サーバの初期構築と運用確認を Ansible で自動化するポートフォリオ用リポジトリです。

VirtualBox 上の Rocky Linux 9 検証環境を想定し、共通初期設定、Apache HTTP Server 構築、構築後の運用確認を分けて管理します。

## 目的

単に Apache をインストールするだけでなく、以下を説明できる構成にすることを目的とします。

- 共通設定と用途別設定を分離する
- Playbook は役割の呼び出しに寄せ、処理本体は role にまとめる
- 構築 Playbook と確認 Playbook を分ける
- 実 inventory を公開せず、example だけを Git 管理する
- 構築後に人手で確認しがちな作業を Ansible で確認する

## 想定環境

| 項目 | 内容 |
| --- | --- |
| Ansible 実行サーバ | Rocky Linux 9 または Ubuntu Server |
| 操作対象サーバ | Rocky Linux 9 |
| 仮想化環境 | VirtualBox |
| 接続方式 | SSH |
| 対象用途 | 社内イントラ向け基本 Web サーバ |

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
    ├── 01_design_decisions.md
    └── 02_operation_check.md
```

## 実行順序

実 inventory を作成します。

```bash
cp inventory/hosts.ini.example inventory/hosts.ini
vi inventory/hosts.ini
```

疎通確認を行います。

```bash
ansible all -m ping
```

共通設定と Web サーバ構築を実行します。

```bash
ansible-playbook playbooks/site.yml
```

構築後の運用確認を実行します。

```bash
ansible-playbook playbooks/verify_web.yml
```

## 実務寄りにした点

- `roles/common`、`roles/web`、`roles/web_verify` に分け、処理の責務を明確にしています。
- `playbooks/site.yml` は構築、`playbooks/verify_web.yml` は確認に分けています。
- `group_vars/all.yml` には共通値、`group_vars/web.yml` には Web 用の値を置いています。
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
