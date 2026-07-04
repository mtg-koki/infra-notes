# Google Cloud Operations（旧Stackdriver）

## 概要

Cloud Monitoring / Cloud Logging / Cloud Traceを中心とした可観測性スイート。マネージドサービスの監視だけでなく、GKEワークロードの監視も含めて扱う。

## 主要サービス

- Cloud Monitoring（メトリクス、ダッシュボード、アラートポリシー）
- Cloud Logging（ログの集約、ログルーター、ログベースメトリクス）
- Cloud Trace（分散トレーシング）
- Error Reporting / Cloud Profiler

## 設計ポイント

- ログルーター（Log Router）でプロジェクト横断のログをBigQuery/Cloud Storage/Pub/Subへ振り分け、監査要件とコストを両立する。
- アラートポリシーはSLOベースで設計し、閾値アラートに偏りすぎないようにする。
- マルチプロジェクト環境ではMonitoring Workspaceでプロジェクト横断の可視化を行う。

## 運用ポイント

- ログの保持期間・エクスポート先はコストとコンプライアンス要件で決定する（デフォルト保持期間の理解が必要）。
- Error Reportingでアプリケーションエラーを自動集約し、対応優先度を判断する。
- Cloud Traceでレイテンシのボトルネックをサービス間で特定する。

## ベストプラクティス

- （実務で得た知見をここに追記する）
