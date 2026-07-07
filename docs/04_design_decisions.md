# 設計判断

## role 構成にした理由

初期の Playbook にすべての task を直接書く形は分かりやすい一方、用途が増えると再利用しにくくなります。

この改善版では、以下の考え方で role を分けています。

- 共通部: OS 共通初期設定
- 固有部: サーバ用途ごとの設定
- 運用部: 構築後の状態確認

Playbook は role を呼び出す入口に寄せ、処理本体は role 側へ集約しています。

現在の実装では、固有部の例として Web サーバ role と、その確認用 role を置いています。

## 構築と確認を分けた理由

構築 Playbook と確認 Playbook を分けることで、初期構築だけでなく、運用時の状態確認にも同じ確認手順を使えるようにしています。

- `playbooks/site.yml`: 共通設定と用途別設定
- 確認用 Playbook: サービス状態、ポート待受、応答などの確認

## 外部 collection を使わない理由

firewalld や timezone は専用 collection を使う選択肢があります。

ただし初期版では、検証環境で動かしやすいことを優先し、`ansible.builtin` を中心にしています。`firewall-cmd` は command で実行していますが、事前に状態確認を行い、不要な変更が発生しにくい形にしています。

将来、`ansible.posix.firewalld` や `community.general.timezone` を導入する場合は、`collections/requirements.yml` を追加し、README に導入手順を明記します。

## 変数を分けた理由

共通設定は `inventory/group_vars/all.yml`、用途別設定は対象グループごとの group_vars に分けています。

これにより、将来 DNS、Syslog、Samba、監視などの用途別グループを追加しても、共通設定と用途別設定を混ぜずに管理できます。
