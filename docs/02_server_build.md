# サーバー構築手順

## 目的

このドキュメントでは、Ansible で操作対象サーバーへ共通設定と用途別設定を適用する流れを整理します。

このラボでは、以下の考え方で処理を分けています。

- 共通部: どのサーバーにも必要になりやすい OS 初期設定
- 固有部: サーバー用途ごとに変わる設定
- 運用部: 構築後に確認したい状態確認

## 実行前の確認

先に `01_ansible_manager_setup.md` の内容を完了しておきます。

```bash
ansible all -m ping
```

疎通確認が成功してから、構築 Playbook を実行します。

## 変数の確認

共通設定は `inventory/group_vars/all.yml` に置いています。

```yaml
timezone: Asia/Tokyo
common_packages:
  - vim
  - curl
  - wget
```

用途別の値は、対象グループごとの group_vars に分けます。現在の実装では `inventory/group_vars/web.yml` に用途別設定を置いています。

## 構築 Playbook の実行

```bash
ansible-playbook playbooks/site.yml
```

`playbooks/site.yml` は、role を呼び出す入口です。

```yaml
- name: Apply common Linux settings
  hosts: all
  roles:
    - common

- name: Configure internal web server
  hosts: web
  roles:
    - web
```

処理本体は Playbook に直接書かず、role 側へ分けています。

## 適用される主な内容

`roles/common` では、以下のような共通設定を扱います。

- タイムゾーン
- 共通パッケージ
- firewalld
- chronyd
- rsyslog
- SELinux 状態確認

用途別 role では、対象サーバーの役割に応じた設定を扱います。現在の実装例では、Web 用の設定を role として分けています。

## 成功後の状態

構築 Playbook が成功したら、次を確認します。

- 共通パッケージが導入されている
- 必要なサービスが起動している
- 用途別 role の設定が反映されている
- Playbook を再実行しても不要な変更が増えない

構築後の確認は、次の `03_operation_check.md` で扱います。
