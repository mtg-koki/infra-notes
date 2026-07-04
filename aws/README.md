# AWS

## 概要

Amazon Web Services（AWS）の実務観点での整理。サービス数が膨大なため、実務で頻出するコア領域（IAM、ネットワーク、コンピュート、ストレージ、データベース）と、設計指針であるWell-Architected Frameworkを中心にまとめる。

## 主要サービス

| 領域 | ファイル | 代表サービス |
|---|---|---|
| IAM | [iam.md](iam.md) | IAM、Organizations、IAM Identity Center、SCP |
| ネットワーク | [networking.md](networking.md) | VPC、Transit Gateway、Route 53、CloudFront |
| コンピュート | [compute.md](compute.md) | EC2、ECS/Fargate、Lambda、EKS |
| ストレージ | [storage.md](storage.md) | S3、EBS、EFS、Storage Gateway |
| データベース | [database.md](database.md) | RDS、Aurora、DynamoDB、ElastiCache |
| 設計指針 | [well-architected.md](well-architected.md) | Well-Architected Framework 6つの柱 |
| 資格 | [certifications/](certifications/README.md) | SAA / DVA / SOA / SAP |

## 設計ポイント

- アカウント戦略（Landing Zone、マルチアカウント構成）を初期段階で決定する。後からの変更コストが高い。
- リージョン選定はレイテンシ・データ所在地規制・サービス提供状況の3点で検討する。
- マネージドサービス（Aurora、Fargate等）を優先し、運用負荷を下げる方針をデフォルトにする。

## 運用ポイント

- コスト管理はCost Explorer、Budgets、タグ付け戦略をセットで運用する。
- CloudTrail・Config・GuardDutyを組織全体で有効化し、証跡とセキュリティ検知を確保する。
- 障害発生時の切り分けはCloudWatchのメトリクス/ログ/アラームを起点に行う。

## ベストプラクティス

- Well-Architected Framework のレビューを定期的に実施し、設計負債を可視化する。
- IaC（CloudFormation/CDK/Terraform）でリソースを管理し、手動変更（ClickOps）を最小化する。
- （実務で得た知見をここに追記する）
