# Azure コンピュート

## 概要

Virtual Machinesを基盤に、コンテナ（AKS）、PaaS（App Service）、サーバーレス（Functions）まで、ワークロード特性に応じたコンピュートサービスの選定観点をまとめる。

## 主要サービス

- Virtual Machines（VMシリーズ、可用性セット/可用性ゾーン）
- Azure Kubernetes Service（AKS、詳細は[aks.md](aks.md)）
- App Service（Webアプリ、PaaS）
- Azure Functions（サーバーレス）
- Virtual Machine Scale Sets（水平スケーリング）

## 設計ポイント

- 可用性要件に応じて可用性セット（同一データセンター内）か可用性ゾーン（複数データセンター）を選ぶ。
- Webアプリケーションはインフラ管理を減らしたい場合App Service、柔軟な制御が必要ならVM/AKSを選ぶ。
- AKSはノードプールを分離し、システムワークロードとユーザーワークロードを混在させない。

## 運用ポイント

- VM Insights / Azure Monitorでパフォーマンスと可用性を監視する。
- AKSはアップグレード（Kubernetesバージョン、ノードイメージ）の計画的な適用が必須。
- Auto-scaleルールをメトリクスベースで設定し、コストとパフォーマンスのバランスを取る。

## ベストプラクティス

- （実務で得た知見をここに追記する）
