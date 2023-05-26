# Zadania

## Zadanie 1: Instalacja i uruchomienie minikube wraz z potrzebnymi narzędziami

### Wprowadzenie
Podróż z Kubernetesem zaczniemy od instalacji minikube i uruchomienia za jego pomocą klastra K8s. Nie jest to rozwiązanie produkcyjne, jednak znakomicie sprawdza sie jako narzędzie deweloperskie. Dodatkowo zainstalujemy potrzebne narzędzia do obsługi naszego klastra.

### Kroki
1. Zainstaluj minikube zgodnie z punktem 1 [oficjalnej dokumentacji](https://minikube.sigs.k8s.io/docs/start/)
2. Uruchom klaster za pomocą polecenia

```
minikube start --driver=docker --nodes 3 --cni calico
```

3. Rezultat powinien być następujący:

```
😄  minikube v1.30.1 on Ubuntu 22.04
✨  Using the docker driver based on user configuration
📌  Using Docker driver with root privileges
👍  Starting control plane node minikube in cluster minikube
🚜  Pulling base image ...
🔥  Creating docker container (CPUs=2, Memory=2200MB) ...
🐳  Preparing Kubernetes v1.26.3 on Docker 23.0.2 ...
    ▪ Generating certificates and keys ...
    ▪ Booting up control plane ...
    ▪ Configuring RBAC rules ...
🔗  Configuring Calico (Container Networking Interface) ...
    ▪ Using image gcr.io/k8s-minikube/storage-provisioner:v5
🔎  Verifying Kubernetes components...
🌟  Enabled addons: storage-provisioner, default-storageclass

👍  Starting worker node minikube-m02 in cluster minikube
🚜  Pulling base image ...
🔥  Creating docker container (CPUs=2, Memory=2200MB) ...
🌐  Found network options:
    ▪ NO_PROXY=192.168.49.2
🐳  Preparing Kubernetes v1.26.3 on Docker 23.0.2 ...
    ▪ env NO_PROXY=192.168.49.2
🔎  Verifying Kubernetes components...

👍  Starting worker node minikube-m03 in cluster minikube
🚜  Pulling base image ...
🔥  Creating docker container (CPUs=2, Memory=2200MB) ...
🌐  Found network options:
    ▪ NO_PROXY=192.168.49.2,192.168.49.3
🐳  Preparing Kubernetes v1.26.3 on Docker 23.0.2 ...
    ▪ env NO_PROXY=192.168.49.2
    ▪ env NO_PROXY=192.168.49.2,192.168.49.3
🔎  Verifying Kubernetes components...
🏄  Done! kubectl is now configured to use "minikube" cluster and "default" namespace by default
```

5. Zainstaluj narzędzie `kubectl` zgodnie z [dokumentacją](https://kubernetes.io/docs/tasks/tools/)
6. Wykonaj polecenie `kubectl cluster-info` oraz `kubectl get pods -A`
7. Rezultat powinien być następujący (oczywiście adres IP może się różnić):

```
Kubernetes control plane is running at https://192.168.49.2:8443
CoreDNS is running at https://192.168.49.2:8443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
```

```
NAMESPACE     NAME                               READY   STATUS    RESTARTS        AGE
kube-system   coredns-787d4945fb-2w6fs           1/1     Running   0               8m47s
kube-system   etcd-minikube                      1/1     Running   0               9m
kube-system   kube-apiserver-minikube            1/1     Running   0               9m
kube-system   kube-controller-manager-minikube   1/1     Running   0               9m
kube-system   kube-proxy-n6t5s                   1/1     Running   0               8m47s
kube-system   kube-scheduler-minikube            1/1     Running   0               9m
kube-system   storage-provisioner                1/1     Running   1 (8m25s ago)   8m59s
```

## Zadanie 2: Instalacja dashboardu na klastrze

### Wprowadzenie
Zarządzać i monitorować klaster można w bardzo różny sposób. Providerzy cloudowi dostarczają często swoje narzędzia. Mamy również do dyspozycji narzędzia konsolowe, dzisiaj jednak skupimy się na w miarę prostym graficznym narzędziu, które pozwoli nam przeglądać zasoby klastra. Zainstalujemy Kubernetes Dashboard za pomocą plików yaml. Dokumentacja znajduje się tutaj: https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/

### Kroki
1. Zainstaluj dashboard za pomocą komendy:

```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml
```

2. Sprawdź czy dashboard się uruchomił za pomocą komendy `kubectl get pods -n kubernetes-dashboard`
3. Rezultat powinien być następujący:

```
NAME                                        READY   STATUS    RESTARTS   AGE
dashboard-metrics-scraper-7bc864c59-rphjk   1/1     Running   0          100s
kubernetes-dashboard-6c7ccbcf87-lkx8s       1/1     Running   0          100s
```

4. Utwórz plik `dashboard-adminuser.yaml` z zawartością:

```
---
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kubernetes-dashboard

---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kubernetes-dashboard
```

5. Wrzuć pliki na klaster komendą `kubectl apply -f dashboard-adminuser.yaml`
6. Wygeneruj token, który będzie potrzebny do zalogowania `kubectl -n kubernetes-dashboard create token admin-user`
7. Powinieneś otrzymać token na kształt tego poniżej. Zapisz go na boku, przyda się za chwilę

Przykładowy token
```
eyJhbGciOiJSUzI1NiIsImtpZCI6IlZCYXdWMmJUZ3ozVEpKbHpIRVdFeHppOUV3T2llVDIxbmZ5UWpvOVQxT28ifQ.eyJhdWQiOlsiaHR0cHM6Ly9rdWJlcm5ldGVzLmRlZmF1bHQuc3ZjLmNsdXN0ZXIubG9jYWwiXSwiZXhwIjoxNjg1MTI0ODc5LCJpYXQiOjE2ODUxMjEyNzksImlzcyI6Imh0dHBzOi8va3ViZXJuZXRlcy5kZWZhdWx0LnN2Yy5jbHVzdGVyLmxvY2FsIiwia3ViZXJuZXRlcy5pbyI6eyJuYW1lc3BhY2UiOiJrdWJlcm5ldGVzLWRhc2hib2FyZCIsInNlcnZpY2VhY2NvdW50Ijp7Im5hbWUiOiJhZG1pbi11c2VyIiwidWlkIjoiZDU2MTJjZDktNjM5NS00ZWFhLTgxY2UtZGRkM2M0ODgzNDljIn19LCJuYmYiOjE2ODUxMjEyNzksInN1YiI6InN5c3RlbTpzZXJ2aWNlYWNjb3VudDprdWJlcm5ldGVzLWRhc2hib2FyZDphZG1pbi11c2VyIn0.d2Kab8lyfTb08cJS5wjoW_OEzE66UJVHCcqgIghQ6lWLDOnO4MaudwskTNhMIpUorJaMwDy3ifH0OwTo2P6WZ3Y_oywdj8T-EFws22Ot7sFvvXK1C1B8wTJYQnx4SviaqhixKnLoy1nWTMmUbtn2O7NPXB40_RvCt3ehoYzrleNUb3Cdp5nUfYYWHoyExSblQYvBVK98tj7eozGOeVMuYjvSemJVCdEmjzAXdqr7uCanzspw7I6DQDJCfgG9Hl7D-UdxiK9ZaiZjPt8xMLmmFkJ0FFNO9HuEx26HQe_1Dwim-yEcpRUmBbccEFZHioPCLW2eXElQxRX0xGHHE4OQIg
```

8. Uruchom proxy `kubectl proxy`
9. Wejdź na http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/
10. Zaloguj się za pomocą wcześniej wygenerowanego tokena
11. Obejrzyj zasoby swojego klastra

## Zadanie 3: Włączenie metryk klastra

### Wprowadzenie
Domyślnie minikube nie posiada [metrics-server](https://github.com/kubernetes-sigs/metrics-server) dlatego nie możemy zobaczyć zużycia RAMu czy CPU. Żeby to poprwić, trzeba aktywować addon w minikube

### Kroki
1. Sprawdź, że metrics-server nie działa komendą `kubectl top nodes`
2. Rezultat powinien być następujący:

```
error: Metrics API not available
```

3. Uruchom metrics-server komendą `minikube addons enable metrics-server`
4. Rezultat powinien być następujący:

```
💡  metrics-server is an addon maintained by Kubernetes. For any concerns contact minikube on GitHub.
You can view the list of minikube maintainers at: https://github.com/kubernetes/minikube/blob/master/OWNERS
    ▪ Using image registry.k8s.io/metrics-server/metrics-server:v0.6.3
🌟  The 'metrics-server' addon is enabled
```

4. Ponownie uruchom komendę `kubectl top nodes`
5. Tym razem rezultat powinien być następujący:

```
NAME           CPU(cores)   CPU%   MEMORY(bytes)   MEMORY%   
minikube       149m         7%     874Mi           22%       
minikube-m02   69m          3%     376Mi           9%        
minikube-m03   120m         6%     443Mi           11%       
```

## Zadanie 4: Poznanie komend kubectl

### Wprowadzenie
Kubectl jako podstawowe narzędzie do zarządzania klastrem powinno być dobrze poznane.

### Zadanie 4a:
1. Sprawdź jak wygląda plik `~/.kube/config
2. Skopiuj definicję klastra i daj jej nową nazwę
3. Za pomocą komendy `kubectl config set-context` stwórz nowy context, w którym użyjesz nowej definicji klastra i istniejącej definicji użytkownika
4. Za pomocą komendy `kubectl config use-context` przełącz się na nowo utworzony context
5. Sprawdź czy działa za pomocą komendy `kubectl cluster-info`

### Zadanie 4b:
1. Stwórz nowy namespace o nazwie `stacja-it-kubectl` za pomocą komendy `kubectl create`
2. Stwórz deployment o nazwie hello-world z image `hello-world` w tym namespace za pomocą komendy `kubectl create`
3. Sprawdź w jakim statusie jest pod za pomocą komendy `kubectl get pods`
4. Sprawdź logi poda za pomocą komendy `kubectl logs`
5. Przemyśl otrzymany rezultat

### Zadanie 4c:
1. Stwórz deployment o nazwie nginx z image `nginx` w namespace `stacja-it-kubectl` za pomocą komendy `kubectl create`
2. Sprawdź w jakim statusie jest pod za pomocą komendy `kubectl get pods`
3. Usuń poda za pomocą komendy `kubectl delete pod`
4. Ponownie sprawdź pody
5. Przemyśl otrzymany rezultat
6. Spraw, żeby pod faktycznie się usunął
7. Usuń namespace `stacja-it-kubectl`

### Zadanie 4d:
1. Sklonuj [repozytorium](https://github.com/rechandler12/stacjait-kubernetes)
2. Za pomocą jednej komendy `kubectl apply` utwórz wszystkie zasoby znajdujące się w katalogu `./template`
3. Sprawdź czy dane zasoby zostały utworzone za pomocą komendy `kubectl get`

### Zadanie 4e:
1. Zapoznaj się się z wynikami komend:
- `kubectl get nodes`
- `kubectl describe node <nazwa>`
- `kubectl get pods`
- `kubectl get deployments`
- `kubectl get services`
- `kubectl describe pod <nazwa>`
- `kubectl describe deployments <nazwa>`
- `kubectl describe services <nazwa>`
2. Dodaj do poprzednich komend przełączniki `-o wide`, `-o yaml`, `-n <nazwa>`, `--all-namespaces`
3. Jak zmieniły się wyniki? Czy dla każdego zasoby się zmieniły?

### Zadanie 4f:
1. Za pomocą komendy `kubectl get` wyszukaj:
- wszystkie nody, które nie mają selektora: `node-role.kubernetes.io/control-plane`
- wszystkie pody, które aktualnie są w statusie Running
2. Za pomocą komendy `kubectl get` posortuj pody:
- po czasie stworzenia
- po liczbie restartów

### Zadanie 4g:
1. Zapoznaj się z wynikami komendy `kubectl logs`
2. Spróbuj wejść do środka poda za pomocą komendy `kubectl exec <nazwa> -it -- bash`

### Zadnie 4h:
1. Możesz spróbować pobawić się innymi komendami dostępnymi w kubectl

## Zadanie 5: Własny deployment

### Wprowadzenie
W pracy z K8s podstawowym obiektem, który tworzymy są Deploymenty. Praktycznie zawsze tworzy się je za pomocą YAMLa.

### Kroki
1. Na podstawie `deployment.yaml` w katalogu `template` stwórz swój, ma się on różnić następującymi rzeczami:
- nazwa: httpd
- image: httpd (wybierz konkretny tag)
- repliki: 3
- zmień oczywiście też nazwę kontenera i labelki
2. Wgraj deployment do namespace stacja-it-httpd
3. Przeskaluj deployment do 2 replik za pomocą komendy `kubectl scale`
4. Sprawdź czy faktycznie liczba podów jest równa 2
5. Za pomocą komendy `kubectl port-forward` połącz się do poda i sprawdź rezultat w przeglądarce

## Zadanie 6: Ograniczanie zasobów

### Wprowadzenie
Nasze aplikacje często mogą zużywać więcej zasobów niż byśmy chcieli. Z tego względu możemy ograniczyć ilość zasobów jaką nasz pod dostanie.

### Kroki
1. Wykorzystaj deployment z Zadania 5
2. Na podstawie `daemonset.yaml` z katalogu `template` dodaj rozsądne requesty i limity na CPU i RAM
3. Wgraj deployment
4. Sprawdź za pomocą `kubectl describe pods` czy te wartości faktycznie się nałożyły

## Zadanie 7: Własny service

### Wprowadzenie
Oprócz Deploymentów, Service to drugi najpotrzebniejszy obiekt w K8s.

### Kroki
1. Do deploymentu z kroku 6 dopisz service. Możesz wzorować się na tym w pliku `statefulset.yaml` w katalogu `template`. Pamiętaj o usunięciu `clusterIP: None`
2. Wgraj service
3. Sprawdź za pomocą `kubectl get services` czy service się stworzył
4. Sprawdź za pomocą `kubectl describe service` czy service łączy się do podów
5. Za pomocą komendy `kubectl port-forward` połącz się do service i sprawdź rezultat w przeglądarce

## Zadanie 7: Zmiana typu service

### Wprowadzenie
Najczęściej używamy Service z typem ClusterIP, jednak czasami jest potrzeba użycia innego typu.

### Kroki
1. Zmień typ service z Zadania 7 na NodePort
2. Wgraj service
3. Sprawdź na jakim porcie wystawił się service
4. Sprawdź IP dowolnego noda
5. Sprawdź w przeglądarce czy service odpowiada pod adresem: http://ip_node:port_service/