# Azure Kubernetes Service（AKS）

## 概要

AKSは、Kubernetesクラスター（コントロールプレーンとノード群をまとめた1つの環境）をマネージドで提供するサービス。コントロールプレーンの管理はAzure側に任せられるが、ネットワーク方式・認証方式・Ingress構成などクラスター内部の設計は基本的にユーザー側の裁量となる点がPaaS（App Serviceなど）との大きな違い。

## AKSとApp Serviceの立ち位置の違い（TLS終端を例に）

- **App Service**は`*.azurewebsites.net`の標準ドメインに対して、プラットフォーム側でTLS終端を行う仕組みを持つ。リソースを作成するだけでHTTPSでアクセスできる。
- **AKS**はあくまでKubernetesクラスターをマネージドで提供するサービスであり、「どこでTLS終端するか」（IngressかL7 LBか）はユーザー設計になる。
- Nginx Ingress、AGIC（Application Gateway Ingress Controller）、App Routingアドオンなどを組み合わせればApp Service並みに運用を楽にできるが、「Ingress作成」「証明書管理」「DNS設定」の3ステップは必ず必要になる。
- その代わり、複数ドメイン・複数サービス・マルチクラスター構成に柔軟に対応できるため、AKSは「便利さより自由度を取った基盤」という立ち位置になる。

**補足**: 上記3ステップは以下のように実装するのが一般的。

| ステップ | 代表的な実現方法 |
|---|---|
| Ingress作成 | Nginx Ingress Controller、AGIC、App Routingアドオン、Application Gateway for Containers（ALB Controller） |
| 証明書管理 | cert-manager + Let's Encrypt、Key Vault連携（CSIドライバー経由） |
| DNS設定 | Azure DNS + external-dns（Ingress/ServiceのアノテーションからDNSレコードを自動作成） |

AGICやApp Routingアドオンを使うと、Ingress作成・証明書取得（Key Vault連携）まで半自動化できるが、DNS側のレコード管理は基本的に別途必要になる。

## クラスター運用の基本用語

- **クラスター**: コントロールプレーンとノード群をまとめた1つの環境。AKSはこのクラスターをマネージドで提供する。リージョン、ノードプール構成、ネットワーク方式（Azure CNI/オーバーレイ）、認証方式（RBAC/OIDC/Workload ID）などをクラスター単位で決める。
- **ノード**: コンテナを動かす仮想マシン（VM）。AKSではノードプールとして管理する。VMサイズ、OS（Linux/Windows）、スケーリング方法（手動/自動）、スポットノードの利用などをノードプールごとに設定する。
- **Pod**: Kubernetesで実際にコンテナを動かす最小単位。1つ以上のコンテナとそのリソースをまとめて構成する。Podのリソース要求・制限、NodeSelector/taintsによる配置制御、Azure CNIでのIP割り当てなどを設計する。

## クラスター管理・拡張機能

- **Azure Kubernetes Fleet Manager**: 複数AKSクラスターをフリートとして管理し、展開のポリシー分散やマルチクラスター展開を楽にするサービス。単一クラスター運用でもFleet Managerで複数クラスターを統合管理したり、リージョン分散スケジューリング（本番クラスターの一括管理が必要）が要る場合に利用を検討する。
- **仮想ノード（Virtual Nodes / ACI連携）**: Azure Container Instancesと連携し、AKSクラスターにサーバーレス的な追加キャパシティを提供する機能。バースト的な負荷が想定されるワークロードでVirtual Nodeを使う例が多く、通常ノードプールのみで運用する場合との比較検討やネットワーク・セキュリティ（VNet統合）要件を満たせるか確認する。
- **Infra RG（インフラストラクチャ リソースグループ）**: AKSが内部的に使うノード・ロードバランサーなどのインフラリソースを配置する専用リソースグループ。クラスター本体のRGとインフラRGを分けて管理し、権限分離（Ops/Dev）や課金管理を踏まえたRG設計を行う。
- **マネージド Kubernetes 名前空間（Managed Namespaces）**: AKS側が管理する特別なKubernetes名前空間で、アドオンやシステムコンポーネントが収まる場所。システム名前空間（`kube-system`、`azure-*`など）とユーザーアプリ名前空間をきっちり分けて運用ポリシーを変え、Azure Policy/RBACでシステム名前空間への操作を制限するのが一般的。

## ネットワーク

- **Azure CNI オーバーレイ**: Azure CNIの一種で、PodにオーバーレイネットワークIPを割り当て、基盤サブネットのIP消費を避けるモード。従来のAzure CNI（PodがVNet IPを直接使用）よりIPを節約でき、大規模クラスターでVNetのIPを節約したい場合に有利。
- **Azure CNI ノードサブネット**: Azure CNIを使う場合に、ノードVMが属するサブネットで、Podのアドレス割り当て設計と密接に関わる。ノードサブネットとPodサブネットを分離するかどうか、ExpressRoute/VPN/Security Groupと整合したCIDR設計が必要。
- **DNS名のプレフィックス**: AKSクラスターのAPIサーバーなどに割り当てられる、公開/内部エンドポイントのホスト名プレフィックス。組織の命名規則に合わせたプレフィックスを指定し、環境（dev/stg/prod）を識別できるようにする。プライベートクラスターでは内部DNSとの整合も考慮する。
- **Cilium データプレーンとネットワークポリシーエンジン**: AKSでCiliumベースのデータプレーンを利用すると、より高機能なネットワークポリシーや観測性を提供する設定。従来のkubenet/Azure CNIだけでなく、Ciliumベースネットワークを選択でき、レイヤー7に近いポリシー表現などが可能になる。高度なネットワークセキュリティ・トラフィック解析が必要な場合に有効。
- **Istio（サービスメッシュ）**: マネージドサービスメッシュ（Azure上のIstioベース機能）を有効にすると、トラフィック制御・ゼロトラスト通信・観測性を強化できる。サービスメッシュが必要かどうか、必要ならIstioベースのAKSアドオンを使うか別のメッシュ（Linkerd等）を自前で入れるかを検討する。mTLSや細かいルーティング制御を行うマイクロサービス環境で有効。

## セキュリティ・アイデンティティ

- **Kubernetes RBAC**: Kubernetesのロールベースアクセス制御で、ユーザーやサービスアカウントがクラスター内で何ができるかを制御する。AKSではAzure RBAC for KubernetesとKubernetesネイティブRBACの両方を選べる。Azure AD（Entra ID）連携RBACを使うと、Kubernetesロールをユーザー中心に管理する設計にできる。
- **OIDC（OpenID Connect）**: AKSクラスターがOIDC issuerとして動作し、外部ID連携やWorkload Identityに必要なトークン発行を行う設定。クラウドネイティブな認証・権限管理（特にサービスアカウントとAzureリソースの連携）を行いたいなら有効化する。既存のAAD Pod Identityからの移行先としても、OIDCベースが今後の推奨。
- **ワークロード ID（Workload Identity）**: Kubernetesのサービスアカウントと Azure Entra IDアプリ（マネージドID）を紐付け、PodがAzureリソースへ安全にアクセスできる仕組み。キー接続文字列やSecretに埋め込む代わりに、Workload IDでIDベースのアクセスへ切り替える。Key Vault、StorageなどへPodからアクセスする際に推奨される方法。
- **シークレット ストア CSI ドライバー（Secret Store CSI）**: KubernetesのCSIドライバーを使って、Azure Key Vaultなど外部シークレットストアからPodへシークレットをマウントする機能。アプリがシークレットをファイルとして読むか、環境変数として読むかに応じて採用。Workload Identity・Key Vault・Secret Store CSIを組み合わせると、シークレット管理のベストプラクティスに近づく。
- **Azure Policy**: Azure全体のコンプライアンスサービスで、AKSクラスターがベースラインに準拠していない設定を禁止・監査するために使う。AKS向けのビルトインポリシー（Podの特権禁止、イメージレジストリの制限など）を有効化し、クラスター単位のセキュリティ基準を強制できる。管理グループ・サブスクリプション単位でAKSポリシーを適用するかを決める。

## 観測性・運用

- **Prometheusメトリックの有効化**: AKSからPrometheus形式のメトリックを収集できるようにするアドオン（Azure Monitor / Prometheusモジュール）。Azure Monitor-managed Prometheusを使うか、自前のPrometheusをクラスター内に構築するかを、メトリック保存期間・費用・ダッシュボードツール（Grafanaなど）とセットで考える。
- **Grafanaの有効化**: Azure Managed Grafanaなどを利用し、Prometheusメトリックを可視化するダッシュボードを提供する機能。Azure Managed Grafanaと組み合わせてAKS監視ダッシュボードを標準化するか、自前Grafanaコンテナをクラスター内で動かす方法との比較検討が必要。
- **ACNS（Azure Container Networking Services）を使用したコンテナー ネットワーク監視**: ACNSによるネットワーク監視機能で、Pod間通信やネットワークパフォーマンスの可視化を行うアドオン。ネットワークトラブルシューティングやポリシー設計を重視するならACNS監視を有効化。既存のNPM/独自監視との重複を避けながら、どこまでACNSを使うか決める。
- **ACNSを使用したコンテナー ネットワーク セキュリティ**: ACNSのセキュリティ機能で、ネットワークレベルの脅威検知やポリシー適用を行うアドオン。マイクロセグメンテーションや異常通信検知をクラスターレベルで行いたい場合に有効。既存のNGFW/IDSとの役割分担を考えた上で導入する。
- **イメージ クリーナー（Image Cleaner）**: 古いコンテナイメージや未使用イメージを自動的にクリーンアップし、ノードのディスク使用量を抑える機能。長期運用クラスターでディスク圧迫が進む場合に有効化すると管理が楽になる。固定のイメージを長期間維持したい場合は設定値（保持期間など）を明示的に決める。

## AKS構築時のパラメーター設計（6つの軸）

AKSを設計する際のパラメーターが多岐にわたるため、以下の軸で整理すると抜け漏れが少ない。

1. **クラスター基本設定**: リージョン・可用性ゾーン、DNS名のプレフィックス、クラスタータイプ（パブリック/プライベート）、Infra RGとクラスターRGの分割
2. **コンピュート（ノード/Pod）**: ノードプール数・VMサイズ・OS種別（Linux/Windows）、スケーリング方式（オートスケール、仮想ノード利用の有無）、Podのリソース要求・制限とノード選択ルール
3. **ネットワーク**: ネットワークプラグイン（Azure CNI / CNIオーバーレイ / Ciliumデータプレーン）、VNet・サブネット設計（ノードサブネット、Podサブネット）、DNS設定・ACNSによる監視・セキュリティ機能の有無、Istioなどサービスメッシュ利用の有無
4. **セキュリティ・アイデンティティ**: Kubernetes RBAC / Azure RBAC for Kubernetesの採用方針、OIDC有効化・Workload Identity設定、Secret Store CSI・Key Vault利用方針、Azure Policy（AKS向けビルトインポリシー）の適用範囲
5. **観測性・運用**: Prometheusメトリクスの有効化方法（Azure Monitor-managed/自前）、Grafana利用（Managed Grafana/クラスター内）、ACNS監視・セキュリティ機能の有効化、イメージクリーナー・ログ設定など長期運用時のメンテナンス機能
6. **マルチクラスター・ガバナンス**: Azure Kubernetes Fleet Managerの利用有無、管理グループ・サブスクリプション単位でのAzure PolicyとRBAC設計、マネージド名前空間とユーザー名前空間の運用ルール

**進め方の目安**: ネットワーク（CNIオーバーレイか従来CNIか）とID周り（Workload Identity、Key Vault、CSIを標準にするか）を先に方針決めすると、他のパラメーター設計がスムーズになる。想定するAKSの用途（社内業務システムか、対外向けSaaSかなど）によって推奨パラメーターは変わるため、用途に応じて軸ごとの優先順位を調整する。

## ベストプラクティス

- （実務で得た知見をここに追記する）
