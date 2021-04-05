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


