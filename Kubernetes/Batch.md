### Job
- ワンショットなスクリプトやツールをコンテナとして起動して処理するのに適したリソース
- 処理をする Pod を定義＆作成する

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: sample-job
spec:
  template:
    spec:
      containers:
      - name: sample-job
        image: busybox
        command:
        - bash
        args:
        - echo
        - "hello, world!"
      - name: fail-container
        image: busybox
        command: [ "bash" ]
        args: [ "exit", "137" ]
      restartPolicy: Never
  backoffLimit: 4　　　　　　　　　　　　　　　　// 4回リトライする
```

### Job を一時中断したい時 & 再開したい時

- `kubectl patch job/myjob --type=strategic --patch '{"spec":{"suspend":true}}'
- `kubectl patch job/myjob --type=strategic --patch '{"spec":{"suspend":false}}'`

`.spec.suspend: true` が Job の初期設定の場合、以下のフィールドがミュータブルになり、Pod をスケジューリングするノードの変更などが出来るようになる
- `nodeSelector`
- `nodeAffinity`
- `tolerations`
- `labels`
- `annotations`

### Indexed Job
- Job には NoIndex (default) と Indexed の二つの完了モードがある
- ステートフルなワークロードをワンショットで実行したい時、または迅速にスケールイン・スケールアウトをしたい時に Indexed モードの Job を使う
  
```yaml
...

spec:
  completions: 3
  completionMode: Indexed
  
...
```

- **NoIndex**
	- 各Pod が全て同等に扱われて、区別がないので完了した Pod の合計がそのまま completion にカウントされる
- **Indexed**
	- ジョブ同士が協調して動作する必要がある場合に利用する
	- 各インデックス番号ごとに完了した Pod の数がカウントされるので、各インデックス番号を持つ Pod が１回ずつ正常に完了する必要がある
	- Headless Service を利用できるので、`.spec.template.subdomain` に Service 名を指定することで、コンテナ内から他の Pod へ `${Jobの名前}-${インデックス番号}.${Serviceの名前}.${Namespace}.svc.cluster.local` でアクセスできる