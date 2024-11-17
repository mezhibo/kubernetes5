**Задание 1. Создать Deployment приложений backend и frontend**

1. Создать Deployment приложения frontend из образа nginx с количеством реплик 3 шт.

2. Создать Deployment приложения backend из образа multitool.

3. Добавить Service, которые обеспечат доступ к обоим приложениям внутри кластера.

4. Продемонстрировать, что приложения видят друг друга с помощью Service.

5. Предоставить манифесты Deployment и Service в решении, а также скриншоты или вывод команды п.4.


**Решение 1**


Создадим Deployment приложения frontend из образа nginx с количеством реплик 3 шт. frontend.yaml

```
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: dep-nginx
  name: frontend
  namespace: default
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80
```

Создадим Deployment приложения backend из образа multitool backend.yaml

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: backend
  namespace: default
spec:
  replicas: 1
  selector:
    matchLabels:
      app: multitool
  template:
    metadata:
      labels:
        app: multitool
    spec:
      containers:
      - name: multitool
        image: wbitt/network-multitool:latest
        ports:
        - containerPort: 80
        env:
        - name: HTTP_PORT
          value: "80"
```

![Image alt](https://github.com/mezhibo/kubernetes5/blob/c5e088a7c8fe5c8cc390368aa65107764da93cf0/img/1.jpg)


Добавим Service, которые обеспечат доступ к обоим приложениям внутри кластера

Для frontend

```
apiVersion: v1
kind: Service
metadata:
  name: frontend-svc
  namespace: default
spec:
  selector:
    app: nginx
  ports:
  - name: for-nginx
    port: 80
```


Для backend:

```
apiVersion: v1
kind: Service
metadata:
  name: backend-svc
  namespace: default
spec:
  selector:
    app: multitool
  ports:
  - name: for-multitool
    port: 80
```

![Image alt](https://github.com/mezhibo/kubernetes5/blob/c5e088a7c8fe5c8cc390368aa65107764da93cf0/img/2.jpg)


Теперь убедимся что приложения видят друг друга с помощью Service. Сначала с pods frontend, затем обратно с backend на frontend nginx


![Image alt](https://github.com/mezhibo/kubernetes5/blob/c5e088a7c8fe5c8cc390368aa65107764da93cf0/img/3.jpg)

