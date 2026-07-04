# Azure ガバナンス

## 概要

管理グループ・サブスクリプション構成を基盤とした組織統制。Azure Policyによるコンプライアンス強制、コスト管理を含めたガバナンス全般を扱う。

## 主要サービス

- 管理グループ（Management Groups、階層構造によるポリシー継承）
- Azure Policy（コンプライアンス定義、Deny/Audit/DeployIfNotExists）
- Azure Blueprints / Template Specs（環境のテンプレート化、後継はDeployment Stacks等も要確認）
- Cost Management + Billing（予算、コスト分析）

## 設計ポイント

- 管理グループ階層は組織構造ではなく「ガバナンス要件の違い」を基準に設計する（例: 本番/非本番、規制対象/非対象）。
- Azure Policyは段階的に導入する（まずAuditで影響範囲を確認してからDenyに切り替える）。
- サブスクリプション分割方針（環境別、部門別、コスト管理単位別）を初期に決定する。

## 運用ポイント

- Policyのコンプライアンス状況を定期的にレビューし、非準拠リソースの是正フローを整備する。
- タグ付け方針（コストセンター、環境、オーナー等）を強制し、コスト按分・棚卸しを容易にする。
- 予算アラートとコストの異常検知（Cost Anomaly Detection）を設定する。

## ベストプラクティス

- Azure Landing Zone（Cloud Adoption Framework）を初期構築の土台として活用する。
- （実務で得た知見をここに追記する）
