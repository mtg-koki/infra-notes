# Azure Application Gateway

## 概要

Azureが提供するL7（アプリケーション層）ロードバランサー。SSL/TLS終端、URLパスベースルーティング、WAF（Web Application Firewall）などを備え、Webアプリケーションの前段に配置する用途で使われる。SKUはv1（非推奨）とv2があり、新規構築では基本的にv2を選定する。

## 主要機能

- HTTP(S)ロードバランシング（パスベース/マルチサイトルーティング、複数バックエンドプール）
- SSL/TLSオフロード（フロントエンドでの証明書終端）
- WAF（v2ではWAF_v2 SKUとして統合、OWASP Core Rule Setベース）
- Autoscaling / ゾーン冗長（v2のみ対応）
- AGIC（Application Gateway Ingress Controller）によるAKSとの連携（KubernetesのIngressリソースをApplication Gatewayの設定に反映）

## 設計ポイント

- 新規構築ではv2 SKU（Standard_v2 / WAF_v2）を基本とする。v1はオートスケール非対応・ゾーン冗長非対応のため非推奨。
- フロントエンドはパブリックIPが基本だが、内部向け構成ではプライベートIPのみのフロントエンドも構成可能（後述のプレビュー機能で制約が緩和される）。
- AKSと組み合わせる場合、AGICを使うとIngressリソースの変更がApplication Gatewayの設定に自動反映され、運用負荷を下げられる。

## 運用ポイント

- 診断ログ（アクセスログ、パフォーマンスログ、WAFログ）をLog Analyticsに送り、異常検知・監査に活用する。
- バックエンドプールのヘルスプローブ設定を明確にし、異常時の切り離しが正しく動作するか定期的に確認する。
- 証明書の更新（Key Vault連携時は自動更新、手動配置時は失効管理）を運用フローに組み込む。

## Tips / 補足

### Application Gateway v2 のネットワーク分離プレビュー機能

v2 SKUには `EnableApplicationGatewayNetworkIsolation` というプレビュー機能があり、サブスクリプション単位で有効化すると以下が可能になる。

- **プライベートIPのみのフロントエンド構成**（パブリックIPなしでの構成）
- **パブリックIPリソースを一切持たないデプロイ**

**有効化手順（Azureポータル）**

1. Azureポータルで対象サブスクリプションを開く。
2. 左メニューまたは上部検索で「プレビュー機能」を開く。
3. 検索ボックスで `EnableApplicationGatewayNetworkIsolation` を検索する。
4. 該当機能を選択し、「登録（Register）」をクリックする。
5. 登録が反映されるまで20〜30分程度かかる（ドキュメント・事例ベースの目安）。

**注意点**

- 有効化した時点以降に**新規作成**するv2 Application Gatewayにのみ適用される。**既存のv2リソースには遡って適用されない**点に注意。
- プレビュー機能はサブスクリプション単位の登録（`Microsoft.Network`リソースプロバイダーのfeature登録）であり、Azure CLIでは `az feature register --namespace Microsoft.Network --name EnableApplicationGatewayNetworkIsolation` および `az provider register --namespace Microsoft.Network` の再登録で同様の操作ができる（ポータルのGUI操作とAPIレベルでは同じ仕組み）。
- プレビュー機能のため、正式なSLA対象外・仕様変更の可能性がある。本番導入前に必ず公式ドキュメントで現在のステータス（プレビュー/GA）を確認する。
- 「パブリックIPなしのデプロイ」を狙う場合、既存のパブリックフロントエンド構成からの切り替えは再作成が必要になる点を踏まえて設計段階で決めておく。

## ベストプラクティス

- （実務で得た知見をここに追記する）
