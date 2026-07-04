# Google Cloud ストレージ

## 概要

Cloud Storageを中心としたオブジェクトストレージ、Persistent Disk、Filestoreなど、用途別のストレージサービスとストレージクラス管理の観点をまとめる。

## 主要サービス

- Cloud Storage（ストレージクラス: Standard/Nearline/Coldline/Archive）
- Persistent Disk（VM向けブロックストレージ、リージョナルPD）
- Filestore（マネージドNFS）
- Storage Transfer Service（大規模データ移行）

## 設計ポイント

- ストレージクラスはアクセス頻度予測に基づき選定し、ライフサイクル管理ルールで自動遷移させる。
- リージョナルPersistent Diskでゾーン障害への耐性を確保するか、コスト優先でゾーンPDにするかを要件で判断する。
- マルチリージョン/デュアルリージョンバケットは可用性要件とレイテンシ・コストのトレードオフで選ぶ。

## 運用ポイント

- バケットのIAM権限・公開設定（Public Access Prevention）を組織ポリシーで統制する。
- オブジェクトのバージョニング・保持ポリシー（Bucket Lock）でコンプライアンス要件を満たす。
- Storage Insightsでアクセスパターンを分析し、ストレージクラスの最適化に活用する。

## ベストプラクティス

- （実務で得た知見をここに追記する）
