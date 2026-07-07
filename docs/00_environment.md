# 検証環境設計

## 目的

このドキュメントでは、`ansible-infra-ops-lab` を VirtualBox 上で検証するための想定構成を整理します。

## 想定構成

| ホスト名 | 役割 | OS | IP アドレス例 |
| --- | --- | --- | --- |
| ansible-control | Ansible 実行サーバ | Rocky Linux 9 または Ubuntu Server | 192.168.56.10 |
| rocky-web01 | 操作対象 Web サーバ | Rocky Linux 9 | 192.168.56.101 |

## ネットワーク

- NAT アダプターは、パッケージ取得や GitHub への接続に使います。
- ホストオンリーアダプターは、Ansible 実行サーバから操作対象サーバへの SSH 接続に使います。

## 実行前提

- `ansible-control` から `rocky-web01` へ SSH 接続できること
- inventory に指定したユーザーで権限昇格できること
- 実 IP、実ユーザー名、秘密鍵パスを Git 管理しないこと
