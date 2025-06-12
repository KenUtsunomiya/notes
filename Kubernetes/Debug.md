## デバッグの手順

1. Pod 内にデバッグ用サイドカーコンテナを立てて、コンテナにアクセスする
2. 同一クラスタ内（namespace 内）に Pod を立てて、Pod にアクセスする
3. クラスタ内かつ別 Pod から Service 経由で Pod にアクセスする
### 特定の Pod のデバッグをする

デバッグしたい Pod の中にデバッグ用のコンテナを生成し、そのコンテナからデバッグしたいコンテナにアクセスする
デバッグ対象のコンテナと同じネットワーク、ボリュームにアクセスできる

```shell
kubectl debug --stdin --tty app-pod --image=curlimages/curl --target=server -- sh
```

### 別の Pod から特定の Pod にアクセスする

```shell
kubectl run curl --image=curlimages/curl --rm --stdin --tty --restart=Never --command -- curl 10.244.0.6:8080
```