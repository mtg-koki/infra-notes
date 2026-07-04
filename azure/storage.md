# Azure ストレージ

## 概要

Blob Storageを中心としたオブジェクトストレージ、Azure Files、Managed Disksなど、用途別のストレージサービスとアクセス階層管理の観点をまとめる。

## 主要サービス

- Blob Storage（アクセス階層: Hot/Cool/Archive、ライフサイクル管理）
- Azure Files（マネージドSMB/NFSファイル共有）
- Managed Disks（VM向けディスク、ディスクの種類）
- Storage Account（冗長性オプション: LRS/ZRS/GRS/GZRS）

## 設計ポイント

- ストレージアカウントの冗長性オプションはRTO/RPO要件とコストのバランスで選定する（GRS/GZRSはリージョン障害に対応）。
- Blobのアクセス階層はアクセス頻度予測に基づきライフサイクル管理ポリシーで自動遷移させる。
- AVDやファイルサーバー用途ではAzure Filesのパフォーマンス階層（Standard/Premium）を要件に応じて選ぶ。

## 運用ポイント

- ストレージアカウントのパブリックアクセス設定・ネットワーク制限（Private Endpoint、ファイアウォール）を統制する。
- Blob不変ストレージ（Immutable Storage）でコンプライアンス要件のあるデータを保護する。
- ディスクのバックアップ（Azure Backup）とリストア試験を定期的に実施する。

## ベストプラクティス

- （実務で得た知見をここに追記する）
