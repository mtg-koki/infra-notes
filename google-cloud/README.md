# Google Cloud

## 概要

Google Cloud Platform（GCP）の実務観点での整理。プロジェクト/組織構造を軸としたIAM、ネットワーク、コンピュートに加え、GCPが強みとするデータ分析基盤（BigQuery等）と運用（Cloud Operations）を含めてまとめる。

## 主要サービス

| 領域 | ファイル | 代表サービス |
|---|---|---|
| IAM | [iam.md](iam.md) | Cloud IAM、リソース階層、サービスアカウント |
| ネットワーク | [networking.md](networking.md) | VPC、Cloud Interconnect、Cloud Load Balancing |
| コンピュート | [compute.md](compute.md) | Compute Engine、GKE、Cloud Run、Cloud Functions |
| ストレージ | [storage.md](storage.md) | Cloud Storage、Persistent Disk、Filestore |
| データ | [data.md](data.md) | BigQuery、Dataflow、Pub/Sub、Cloud SQL/Spanner |
| 運用 | [operations.md](operations.md) | Cloud Monitoring、Cloud Logging、Cloud Trace |
| 資格 | [certifications/](certifications/README.md) | ACE / PCA / PDE / PCSE |

## 設計ポイント

- リソース階層（組織 → フォルダ → プロジェクト）はガバナンス単位（部門、環境、コンプライアンス要件）に合わせて設計する。
- プロジェクトを課金・権限管理の最小単位として扱い、環境（本番/検証）ごとにプロジェクトを分離する。
- BigQueryなどのデータ分析基盤を前提としたデータ活用アーキテクチャは、Google Cloudの強みとして積極的に検討する。

## 運用ポイント

- Cloud Monitoring / Cloud Loggingを中心に監視基盤を統一する。
- 組織ポリシー（Organization Policy）でガードレールを設定し、全プロジェクトに強制する。
- Cloud Billingのレポート・予算アラートでコストを可視化する。

## ベストプラクティス

- Google Cloud Architecture Framework（Well-Architected相当）をレビュー基準に用いる。
- IaC（Terraform、Deployment Manager等）でリソースを管理し、コンソールからの手動変更を最小化する。
- （実務で得た知見をここに追記する）
