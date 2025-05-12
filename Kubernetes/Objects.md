### Workload Objects
---

- Pod
    - コンテナをグループ化した最小の実行単位
    - １つ以上のコンテナ、共有ネットワーク・ストレージを保持し、共にスケジュール・実行される
- ReplicaSet
    - 指定数のPodレプリカを常に維持
    - Deploymentで管理されることが多いので、直接はあまり使われない
- Deployment
    - レプリカのローリングアップデートやロールバックをサポート
    - Podテンプレートを定義
- StatefulSet
    - 順序性・一意性を保持してステートフルアプリケーションを管理
    - DB、KVSなどの一意のネットワークID / 永続的ボリュームが必要なもの向け
- DaemonSet
    - 指定したノード全てに１Podを配置する
    - ログ収集・モニタリングエージェント、ネットワークプラグインなど、各ノード必須のデーモン型ワークロードに適用

### Batch / Schedule Objects
---

- Job
    - 一度だけ実行するタスクを管理し、成功するまでリトライ 。
    - .spec.completions で成功回数を指定し、完了後は Pod をクリーンアップ。
- CronJob
    - Unix の cron のように、定期的に Job を作成・実行 。
    - spec.schedule に cron 形式のスケジュールを指定。

### Config / Secret Objects
---

- ConfigMap
    - 非機密設定データをキー／バリューで保存し、環境変数やボリュームとして Pod に注入。
- Secret
    - パスワードや証明書など機密データを取り扱い、Base64 エンコードして管理 。
    - デフォルトでは etcd 上で平文保存のため、暗号化設定や外部シークレット管理と併用推奨。

### Network Objects
---

- Service
    - Pod 群への安定したネットワークアクセスを提供し、内部ロードバランサーとして機能。
    - ClusterIP、NodePort、LoadBalancer、ExternalName の 4 種類。
- Ingress
    - L7 レイヤーでの HTTP(S) ルーティングを統合管理。
    - 複数ホスト名／パスのルールを単一エンドポイントにまとめられる。
- NetworkPolicy
    - Pod 間通信をホワイトリスト方式で制限し、セキュアなマルチテナント環境を構築。

### Namespace / Selector
---

- Namespace
    - クラスターを仮想的に分割し、リソース名の衝突回避やリソースクォータ適用を実現。
- Label と Selector
    - オブジェクトに対するキー／バリュータグを付与し、ラベルセレクタで対象オブジェクト群を動的に抽出 。

### Storage Objects
---

- Volume
    - Pod 内コンテナ間で共有ストレージを提供。
    - emptyDir、hostPath、configMap、secret、クラウドプロバイダーの永続ボリュームなど多種。
- PersistentVolume/PersistentVolumeClaim
    - 管理者が用意する物理的なストレージ（PV）と、ユーザーが要求する抽象的なボリューム（PVC）のマッピングを管理。