## PodDisruptionBudget

- **目的**：クラスタ管理者による計画的なメンテナンス（ノード drain やスケールダウンなど）時に、意図的な中断（Voluntary Disruption）によって同時に停止できる Pod 数を制限し、高可用性を維持するための制約を定義します [Kubernetes](https://kubernetes.io/docs/tasks/run-application/configure-pdb/?utm_source=chatgpt.com)。
    
- **動作要点**：
    1. `spec.minAvailable` または `spec.maxUnavailable` のいずれかを指定し、可用性要件を宣言 [Kubernetes](https://kubernetes.io/docs/tasks/run-application/configure-pdb/?utm_source=chatgpt.com)。
    2. Deployment／StatefulSet 等のコントローラが Pod を停止しようとすると、PDB の状態（`status.disruptionsAllowed`）をチェックし、許可される中断数を超える場合は待機させる [Kubernetes](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/?utm_source=chatgpt.com)。
       
- **例**
```yaml
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata:
name: sample-pdb
spec:
minAvailable: "50%"
selector:
 matchLabels:
   app: sample
```

## PriorityClass

- **目的**：Pod のスケジューリング優先度を整数値で定義し、リソース競合時に優先度の高い Pod を優先的にスケジュール／再起動させるための仕組みです（Preemption にも影響） [Kubernetes](https://kubernetes.io/docs/concepts/scheduling-eviction/pod-priority-preemption/?utm_source=chatgpt.com)。
  
- **動作要点**：
    
    1. `PriorityClass` オブジェクトを作成し、`value` フィールドに優先度スコアを設定 [Kubernetes](https://kubernetes.io/docs/concepts/scheduling-eviction/pod-priority-preemption/?utm_source=chatgpt.com)。
    2. Pod の `spec.priorityClassName` で参照し、Scheduler がノード割り当て時の計算および Preemption（優先度差による既存 Pod 強制退去）にこの値を利用 [Kubernetes](https://kubernetes.io/docs/concepts/scheduling-eviction/pod-priority-preemption/?utm_source=chatgpt.com)。
       
- 例
```yaml
// PriorityClass
apiVersion: scheduling.k8s.io/v1
kind: PriorityClass
metadata:
name: high-priority
value: 1000000
globalDefault: false
description: "for high-priority application"

---

// Pod
apiVersion: v1
kind: Pod
metadata:
name: high-priority-app
spec:
containers:
- name: nginx
  image: nginx
priorityClassName: high-priority
```

## Probe

- **目的**：コンテナの **Liveness**, **Readiness**, **Startup** の各状態を定期チェックし、異常時の再起動やトラフィック制御、初期化待ちを可能にして可用性を高めるためのメカニズムです [Kubernetes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/?utm_source=chatgpt.com)。
    
- **動作要点**：
    
    1. **Liveness Probe**：`httpGet`/`exec`/`tcpSocket` で定期的に検査し、失敗が続くと kubelet がコンテナを再起動 [Kubernetes](https://kubernetes.io/docs/concepts/configuration/liveness-readiness-startup-probes/?utm_source=chatgpt.com)。
    2. **Readiness Probe**：チェック失敗時に Pod を Service エンドポイントから除外し、再度成功すると復帰 [Kubernetes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/?utm_source=chatgpt.com)。
    3. **Startup Probe**：コンテナ起動直後のみ評価し、成功するまで他のプローブを無効化。失敗時は再起動 [Kubernetes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/?utm_source=chatgpt.com)。

https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/

## preStop & postStart

- **目的**：コンテナの起動直後／終了直前にカスタムハンドラー（`exec` や `httpGet`）を実行し、初期化やクリーンアップ処理を確実に差し込むためのフックです [Kubernetes](https://kubernetes.io/docs/concepts/containers/container-lifecycle-hooks/?utm_source=chatgpt.com)。
    
- **動作要点**：
    
    1. **postStart**：コンテナ開始直後に一度だけ呼び出され、初期データ準備などを実行。完了前は Pod は `Running` 状態になりません [Kubernetes](https://kubernetes.io/docs/concepts/containers/container-lifecycle-hooks/?utm_source=chatgpt.com)
    2. **preStop**：SIGTERM 受信後に呼び出され、`terminationGracePeriodSeconds` の猶予時間内でクリーンアップ処理を行い、その後コンテナ停止シグナルを送信 [Kubernetes](https://kubernetes.io/docs/concepts/containers/container-lifecycle-hooks/?utm_source=chatgpt.com)。

## GracePeriod

- **目的**：Pod 削除時にコンテナへ SIGTERM を送信してから強制終了（SIGKILL）までの猶予時間を設定し、アプリケーションに優雅なシャットダウン機会を与えるためのタイムアウト値です [IBM](https://www.ibm.com/docs/en/datapower-operator/1.11?topic=spec-terminationgraceperiodseconds&utm_source=chatgpt.com)。
    
- **動作要点**：
    
    1. Pod 削除リクエストで kubelet が `preStop` フックを実行し始めると同時にカウントダウン開始 [Google Cloud](https://cloud.google.com/blog/products/containers-kubernetes/kubernetes-best-practices-terminating-with-grace?utm_source=chatgpt.com)。
    2. フックおよびコンテナの正常終了が猶予内に終わらない場合、猶予時間経過で強制終了される [Google Cloud](https://cloud.google.com/blog/products/containers-kubernetes/kubernetes-best-practices-terminating-with-grace?utm_source=chatgpt.com)。

## nodeAffinity

- **目的**：特定のノード属性（ラベル）とマッチングさせて Pod を **必須 or 推奨** 条件でノードにスケジュールすることで、リソース分散やノード特性の明示的利用を制御します [Kubernetes](https://kubernetes.io/docs/tasks/configure-pod-container/assign-pods-nodes-using-node-affinity/?utm_source=chatgpt.com)。
    
- **動作要点**：
    
    1. `requiredDuringSchedulingIgnoredDuringExecution` で必須条件を定義し、マッチしないノードにはスケジュールされない [Kubernetes](https://kubernetes.io/docs/tasks/configure-pod-container/assign-pods-nodes-using-node-affinity/?utm_source=chatgpt.com)。
    2. `preferredDuringSchedulingIgnoredDuringExecution` で優先度付き推奨条件を設定し、スコアリングでよりマッチ度が高いノードへ割り当てる [Kubernetes](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/?utm_source=chatgpt.com)。

```yaml
apiVersion: v1
 kind: Pod
 metadata:
   name: node-affinity-sample
 spec:
   affinity:
     nodeAffinity:
       requiredDuringSchedulingIgnoredDuringExecution:
         nodeSelectorTerms:
         - matchExpressions:
           - key: node-role
             operator: In
             values:
             - web1
             - web2
       preferredDuringSchedulingIgnoredDuringExecution:
       - weight: 1
         preference:
           matchExpressions:
           - key: disk-type
             operator: In
             values:
             - ssd
   containers:
   - name: app
     image: nginx
```
## podAffinity / podAntiAffinity

- **目的**：同一ノード／同一トポロジ領域内で **他の Pod** との親和性（Affinity）や反親和性（AntiAffinity）を指定し、ワークロードの共置／分散を制御します [OpenShift Documentation](https://docs.openshift.com/container-platform/4.12/nodes/scheduling/nodes-scheduler-pod-affinity.html?utm_source=chatgpt.com)。
    
- **動作要点**：

    1. **podAffinity**：`requiredDuringScheduling...` で同じラベルの Pod が存在するノードを必須条件に、`preferred...` で推奨条件に設定可能 [OpenShift Documentation](https://docs.openshift.com/container-platform/4.12/nodes/scheduling/nodes-scheduler-pod-affinity.html?utm_source=chatgpt.com)。        
    2. **podAntiAffinity**：同一ノードへの複数レプリカ配置を防ぐなどに利用し、こちらも必須／推奨条件を定義できる [Kubernetes Reference](https://dev-k8sref-io.web.app/docs/common-definitions/podantiaffinity-/?utm_source=chatgpt.com)。

```yaml
apiVersion: v1
 kind: Pod
 metadata:
   name: pod-affinity-sample
 spec:
   affinity:
     podAffinity:
       requiredDuringSchedulingIgnoredDuringExecution:
       - labelSelector:
           matchExpressions:
           - key: app
             operator: In
             values:
             - memcached
         topologyKey: kubernetes.io/hostname // スコープはノード内。ゾーンやリージョン単位でも指定可能
	 podAntiAffinity:
	   requiredDuringSchedulingIgnoredDuringExecution:
	   - labelSelector:
		   matchExpressions:
		   - key: app
			 operator: In
			 values:
			 - memcached
		 topologyKey: kubernetes.io/hostname
   containers:
   - name: app
     image: nginx
```

## topologySpreadConstraints

- **目的**：指定したトポロジドメイン（例：ゾーン、ノード、カスタムラベル）全体にわたって Pod の分散配置を制御し、単一障害点や過集中を防ぐためのポリシーです [Kubernetes](https://kubernetes.io/docs/concepts/scheduling-eviction/topology-spread-constraints/?utm_source=chatgpt.com)。
    
- **動作要点**：
    
    1. `topologyKey`（例：`topology.kubernetes.io/zone`）と分散戦略（`MaxSkew` など）を設定し、ドメイン間の Pod 数の偏りを最小化 [Kubernetes](https://kubernetes.io/docs/concepts/scheduling-eviction/topology-spread-constraints/?utm_source=chatgpt.com)。
    2. `whenUnsatisfiable` フィールドで、分散を維持できない場合の動作（`DoNotSchedule` or `ScheduleAnyway`）を指定可能 [Cast AI](https://cast.ai/blog/topology-spread-constraints-for-increased-cluster-availability-and-efficiency-and-a-much-better-cost/?utm_source=chatgpt.com)。

```yaml
kind: Pod
 apiVersion: v1
 metadata:
   name: sample-topology
 spec:
   topologySpreadConstraints:
   - maxSkew: 1
     topologyKey: zone
     whenUnsatisfiable: DoNotSchedule
     labelSelector:
       matchLabels:
         app: nginx
   containers:
   - name: app
     image: nginx
```

https://kubernetes.io/docs/concepts/scheduling-eviction/topology-spread-constraints/