# 設計判断

## role 構成にした理由

初期の Playbook にすべての task を直接書く形は分かりやすい一方、用途が増えると再利用しにくくなります。

この改善版では、以下の role に分けています。

- `common`: OS 共通初期設定
- `web`: Apache HTTP Server 構築
- `web_verify`: Web サーバ構築後の運用確認

Playbook は role を呼び出す入口に寄せ、処理本体は role 側へ集約しています。

## 構築と確認を分けた理由

構築 Playbook と確認 Playbook を分けることで、初期構築だけでなく、運用時の状態確認にも同じ確認手順を使えるようにしています。

- `playbooks/site.yml`: 共通設定と Web サーバ構築
- `playbooks/verify_web.yml`: Apache 構文、サービス状態、ポート待受、HTTP 応答の確認

## 外部 collection を使わない理由

firewalld や timezone は専用 collection を使う選択肢があります。

ただし初期版では、検証環境で動かしやすいことを優先し、`ansible.builtin` を中心にしています。`firewall-cmd` は command で実行していますが、事前に状態確認を行い、不要な変更が発生しにくい形にしています。

将来、`ansible.posix.firewalld` や `community.general.timezone` を導入する場合は、`collections/requirements.yml` を追加し、README に導入手順を明記します。

## 変数を分けた理由

共通設定は `inventory/group_vars/all.yml`、Web 用設定は `inventory/group_vars/web.yml` に分けています。

これにより、将来 DNS、Syslog、Samba、監視などの用途別グループを追加しても、共通設定と用途別設定を混ぜずに管理できます。
