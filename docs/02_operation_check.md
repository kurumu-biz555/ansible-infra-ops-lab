# 運用確認 Playbook

## 目的

`playbooks/verify_web.yml` は、Apache HTTP Server 構築後の状態を確認するための Playbook です。

構築作業とは分けて、運用確認として繰り返し実行できることを意識しています。

## 確認内容

- Apache 設定構文チェック
- Apache サービス状態確認
- HTTP ポート待受確認
- localhost への HTTP 応答確認

## 実行コマンド

```bash
ansible-playbook playbooks/verify_web.yml
```

## ポートフォリオで説明したい点

構築作業だけでなく、構築後に確認すべき項目を Playbook 化している点を説明できます。

手順書に確認コマンドを並べるだけでなく、Ansible で状態確認を再実行できる形にしているため、運用標準化の考え方を示しやすくなります。
