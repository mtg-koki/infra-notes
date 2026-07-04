# Azure Kubernetes Service（AKS）

## 概要

AKSは、Kubernetesクラスター（コントロールプレーン＋ノード群）をマネージドで提供するサービス。コントロールプレーンの管理はAzure側に任せられるが、ネットワーク方式・認証方式・Ingress構成などクラスター内部の設計はユーザー側の裁量になる。この「設計の自由度が高い」点がPaaS（App Serviceなど）との大きな違い。

## 基本用語

| 用語 | 説明 |
|---|---|
| クラスター | コントロールプレーン＋ノード群をまとめた1つの環境。リージョン、ネットワーク方式、認証方式などをクラスター単位で決める |
| ノード | コンテナを動かすVM。ノードプールとして、VMサイズ・OS・スケーリング方法を設定する |
| Pod | Kubernetesでコンテナを動かす最小単位。1つ以上のコンテナとリソース設定をまとめたもの |
| Azure Kubernetes Fleet Manager | 複数AKSクラスターを1つのフリートとして統合管理し、ポリシー配布やマルチクラスター展開を楽にするサービス |
| 仮想ノード（Virtual Nodes / ACI連携） | Azure Container Instancesと連携し、サーバーレス的な追加キャパシティを提供する機能。バースト負荷向け |
| Infra RG | AKSが内部で使うノード・ロードバランサー等を配置する専用リソースグループ。クラスター本体のRGとは分けて管理する |
| マネージド Kubernetes 名前空間 | AKS側が管理する特別な名前空間（`kube-system`、`azure-*`等）。ユーザーアプリの名前空間とは分離して運用する |

## Azureパラメーター

AKS構築時に決めるべき代表的な設定を、領域別に整理する。

### ネットワーク

| パラメーター | 概要 |
|---|---|
| Azure CNI オーバーレイ | PodにオーバーレイIPを割り当て、VNetのIP消費を抑えるモード。大規模クラスター向き |
| Azure CNI ノードサブネット | ノードVMが属するサブネット。Podサブネットと分離するかをCIDR設計と合わせて検討する |
| DNS名のプレフィックス | クラスターAPIサーバー等のホスト名プレフィックス。命名規則・環境（dev/stg/prod）識別に使う |
| Cilium データプレーン | 高機能なネットワークポリシー・観測性を提供するデータプレーン。高度な通信制御が必要な場合に選択 |
| Istio（サービスメッシュ） | トラフィック制御・mTLS・観測性を強化するマネージドサービスメッシュ。マイクロサービス環境向け |

### セキュリティ・アイデンティティ

| パラメーター | 概要 |
|---|---|
| Kubernetes RBAC / Azure RBAC for Kubernetes | クラスター内の権限制御。Entra ID連携RBACならユーザー中心の権限管理にできる |
| OIDC | クラスターをOIDC issuer化し、外部ID連携やWorkload Identityのトークン発行を可能にする |
| Workload Identity | Kubernetesサービスアカウント⇔Entra IDアプリを紐付け、PodがAzureリソースにIDベースでアクセスする仕組み |
| Secret Store CSI | Key Vault等の外部シークレットをPodにマウントする仕組み。Workload Identityと組み合わせるのが定石 |
| Azure Policy | AKS向けビルトインポリシー（特権Pod禁止など）でクラスターのセキュリティ基準を強制する |

### 観測性・運用

| パラメーター | 概要 |
|---|---|
| Prometheusメトリック | Azure Monitor-managed Prometheusか自前構築かを、保存期間・コストとセットで選ぶ |
| Grafana | Prometheusメトリックの可視化。Azure Managed Grafanaかクラスター内自前構築かを選ぶ |
| ACNS（ネットワーク監視/セキュリティ） | Pod間通信の可視化・脅威検知を行うアドオン。既存監視ツールとの重複に注意 |
| Image Cleaner | 未使用コンテナイメージを自動削除し、ノードのディスク圧迫を防ぐ機能 |

## Tips

### AKSとApp Serviceの立ち位置の違い（TLS終端を例に）

- **App Service**は`*.azurewebsites.net`の標準ドメインに対してプラットフォーム側でTLS終端を行う仕組みを持ち、リソースを作るだけでHTTPSアクセスできる。
- **AKS**はKubernetesクラスターをマネージドで提供するだけなので、「どこでTLS終端するか」（IngressかL7 LBか）はユーザー設計になる。
- Nginx Ingress、AGIC、App RoutingなどでApp Service並みに運用は楽にできるが、「Ingress作成」「証明書管理」「DNS設定」の3ステップは必須。
- その代わり複数ドメイン・複数サービス・マルチクラスターに柔軟対応できる、「便利さより自由度を取った基盤」という立ち位置。

| ステップ | 代表的な実現方法 |
|---|---|
| Ingress作成 | Nginx Ingress Controller、AGIC、App Routingアドオン、Application Gateway for Containers（ALB Controller） |
| 証明書管理 | cert-manager + Let's Encrypt、Key Vault連携（CSIドライバー経由） |
| DNS設定 | Azure DNS + external-dns（Ingress/ServiceのアノテーションからDNSレコード自動作成） |

AGICやApp Routingを使うとIngress作成・証明書取得までは半自動化できるが、DNSレコード管理は基本的に別途必要になる。

### 設計を始める順番

ネットワーク（CNIオーバーレイか従来CNIか）とID周り（Workload Identity・Key Vault・CSIを標準にするか）を先に決めると、他のパラメーター設計がスムーズになる。想定用途（社内業務システムか対外向けSaaSかなど）によって推奨構成は変わる。

## ベストプラクティス

- （実務で得た知見をここに追記する）
