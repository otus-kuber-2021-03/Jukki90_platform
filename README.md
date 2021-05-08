#ДЗ №1
Что было сделано:
 1. Настроено локальное окружение.
 2. Написан Dockerfile, собран контейнер с веб серевером nginx
 3. Написан манифест для pod web, запущен соответствующий pod


## Задание 1
Разберитесь почему все pod в namespace kube-system восстановились
после удаления.

Ответ: 
kube-apiserver, etcd, kube-controller-manager, kube-scheduler пересоздаются kubelet с использованием  манифестов /etc/kubernetes/manifests/
kube-proxy управляется  Daemonset
core-dns - управляется  ReplicaSet



#ДЗ №9 Мониторинг приложения в кластере
1. Подготовка кастомного образа nginx (директория kubernetes-monitoring):
docker build -t nginx-for-monitoring .

Выгрузка в dockerhub
docker image tag nginx-for-monitoring:1 jukki90/nginx-for-monitoring:1
docker push jukki90/nginx-for-monitoring:1

2. Создаем отдельный namespace
kubectl create namespace monitoring

2. Установка prometheus при помощи helm3:
Добавляем репозиторий
helm repo add stable https://charts.helm.sh/stable
Обновляем:
helm repo update
Установка:
helm install hw-monitoring stable/prometheus -n monitoring
Проверка:
kubectl get pods -n monitoring

export POD_NAME=$(kubectl get pods --namespace monitoring -l "app=prometheus,component=server" -o jsonpath="{.items[0].metadata.name}")
kubectl --namespace monitoring port-forward $POD_NAME 9090
Интерфейс Prometheus доступен по адресу:
http://127.0.0.1:9090/ 


3. Создание и применение манифестов kubernetes 
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml -n monitoring
kubectl apply -f service-monitor.yaml -n monitoring

исправление ошибки  'unable to recognize "service-monitor.yaml": no matches for kind "ServiceMonitor" in version "monitoring.coreos.com/v1"'
kubectl apply -f https://raw.githubusercontent.com/prometheus-operator/prometheus-operator/release-0.43/example/prometheus-operator-crd/monitoring.coreos.com_servicemonitors.yaml

4. с помощью helm настроим доступ извне
helm upgrade --install nginx-ingress stable/nginx-ingress --namespace=monitoring

Пример:
curl http://172.27.50.66/basic_status
Active connections: 1
server accepts handled requests
 4 4 4
Reading: 0 Writing: 1 Waiting: 0


