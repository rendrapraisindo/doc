# Instalasi Kafka

## Persyaratan

Sebelum menjalankan step instalasi dibawah ini

1. Patikan bahwa cluster telah dihidupkan, dengan cara resize node pool menjadi 2 node.
2. Pastikan anda telah terkoneksi dengan bastion server, 
3. Pastikan `gcloud`, `kubectl`, `helm` telah terinstall di bastion
4. Pastikan bahwa kubectl telah terhubung ke cluster yang benar. Dapat dicek dengan perintah `kubectl config current-context`
5. Pastikan bahwa bastion server telah tersambung ke cluster. Dapat dicek dengan perintah `kubectl get po -A`

## Instalasi

Jalankan perintah dibawah secara berurutan:
```
helm repo add bitnami https://charts.bitnami.com/bitnami
```
Perintah diatas untuk menambah repo bitnami jika belum ada. Jika sudah ada maka akan diabaikan

```
helm repo update
```
Perintah diatas untuk mengupdate repo bitnami

```
helm install tools-kafka \
--set replicaCount=2 \
--set kraft.enabled=false \
--set zookeeper.enabled=true \
--set zookeeper.replicaCount=2 \
--set persistence.storageClass=standard-rwo \
--set persistence.size=20Gi \
--set zookeeper.persistence.size=10Gi \
--set numPartitions=2 \
--set defaultReplicationFactor=2 \
bitnami/kafka
```
Perintah diatas untuk menginstall kafka sebanyak 2 replika dan zookeeper sebanyak 2 replika

Setelah perintah diatas dijalankan, seharusnya akan keluar message status dari chart tersebut.

## Validasi

Untuk melakukan validasi instalasi, tunggu sampai kurang lebih 5 menit. Seharusnya setelah 5 Menit, seluruh pod kafka dan zookeeper sudah running.
Gunakan perintah `kubectl get po` untuk melihat daftar pod. 

Berikut ini tampilan jika instalasi telah sukses. Pastikan bahwa kolom STATUS Berisi **Running** dan kolom READY **1/1** pada pod-pod yang berawalan tools-kafka

```
NAME                      READY   STATUS    RESTARTS      AGE
tools-kafka-0             1/1     Running   2 (32m ago)   33m
tools-kafka-1             1/1     Running   2 (32m ago)   33m
tools-kafka-zookeeper-0   1/1     Running   0             33m
tools-kafka-zookeeper-1   1/1     Running   0             33m
```

## Clean Up

Untuk meng-uninstall kafka, dapat dilakukan dengan perintah:

```
helm uninstall tools-kafka
```
Perintah diatas akan menghapus pod, statefull set, service di kubernetes. Namun perintah diatas tidak menghapus [PVC](https://kubernetes.io/docs/concepts/storage/persistent-volumes/) yang telah dibuat. Gunakan perintah `kubectl get pvc` untuk melihat daftar PVC yang ada.

Gunakan perintah dibawah untuk menghapus PVC terkait kafka ini.

```
kubectl delete pvc \
data-tools-kafka-0 data-tools-kafka-1 \
data-tools-kafka-zookeeper-0 data-tools-kafka-zookeeper-1
```

## Further Reading

Silakan baca link dibawah untuk referensi lebih lanjut

* https://github.com/bitnami/charts/tree/main/bitnami/kafka
* https://helm.sh/
* https://kubernetes.io/docs/concepts/overview/
* https://kafka.apache.org/documentation/

