# Azure

## 概要

Microsoft Azureの実務観点での整理。Entra ID（旧Azure AD）を中心としたID管理、ネットワーク、コンピュート、ストレージに加え、実務で扱う機会の多いAVD（Azure Virtual Desktop）、ガバナンス（Policy/Blueprints）を含めてまとめる。

## 主要サービス

| 領域 | ファイル | 代表サービス |
|---|---|---|
| ID管理 | [identity.md](identity.md) | Entra ID、条件付きアクセス、PIM |
| ネットワーク | [networking.md](networking.md) | VNet、VPN Gateway、ExpressRoute、Front Door |
| コンピュート | [compute.md](compute.md) | Virtual Machines、AKS、App Service、Functions |
| AKS | [aks.md](aks.md) | Kubernetesクラスター設計、ネットワーク/セキュリティ/観測性、Ingress・TLS終端 |
| ストレージ | [storage.md](storage.md) | Blob Storage、Files、Managed Disks |
| Application Gateway | [agw.md](agw.md) | L7ロードバランサー、WAF、AGIC連携 |
| AVD | [avd.md](avd.md) | Azure Virtual Desktop（仮想デスクトップ基盤） |
| ガバナンス | [governance.md](governance.md) | Azure Policy、Blueprints、管理グループ、コスト管理 |
| 資格 | [certifications/](certifications/README.md) | AZ-900 / AZ-104 / AZ-305 / AZ-400 |

## 設計ポイント

- 管理グループ・サブスクリプション構成は組織のガバナンス単位（部門、環境、コンプライアンス要件）に合わせて設計する。
- Entra IDのテナント設計は初期段階で確定させる（後からのテナント統合は困難）。
- リソースの命名規則・タグ付け規則を初期に定義し、全リソースに一貫適用する。

## 運用ポイント

- Azure Policyでコンプライアンス逸脱を検知・是正し、組織全体のガードレールとする。
- Azure Monitor / Log Analyticsを中心に監視基盤を統一する。
- Cost Managementでサブスクリプション/リソースグループ単位のコスト可視化と予算アラートを設定する。

## ベストプラクティス

- Azure Well-Architected Framework（Microsoft版の設計原則）をレビューの基準に用いる。
- IaC（Bicep/ARM/Terraform）でリソースを管理し、ポータルからの手動変更を最小化する。
- （実務で得た知見をここに追記する）
