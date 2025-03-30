# Задание 1 - Создать Deployment приложения, состоящего из двух контейнеров и обменивающихся данными.

## Создать Deployment приложения, состоящего из контейнеров busybox и multitool. Сделать так, чтобы busybox писал каждые пять секунд в некий файл в общей директории. Обеспечить возможность чтения файла контейнером multitool.
```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: deployment-busybox-multitool
  labels:
    app: app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: app
  template:
    metadata:
      labels:
        app: app
    spec:
      containers:
      - name: busybox
        image: busybox
        ports: 
        # - containerPort: 80
        command: ["/bin/sh", "-c", "while true; do echo \"$(date) - Log entry\" >> /var/log/log.txt; sleep 5; done"]
        volumeMounts:
        - name: vol-busybox-log
          mountPath: /var/log
      - name: multitool
        image: wbitt/network-multitool
        ports: 
        # - containerPort: 8080
        # command: ["/bin/sh", "-c", "httpd -p 8080 -h / & sleep infinity"]
        volumeMounts:
        - name: vol-busybox-log
          mountPath: /tmp
      volumes:
      - name: vol-busybox-log
        emptyDir: {}
        # hostPath:
        #   path: /var/log/syslog
```

## Продемонстрировать, что multitool может читать файл, который периодоически обновляется.

![Продемонстрировать, что multitool может читать файл, который периодоически обновляется](https://github.com/user-attachments/assets/5b6752ed-6b1f-485a-b432-fd6322f7bf5e)

# Задание 2 - Создать DaemonSet приложения, которое может прочитать логи ноды.

## Создать DaemonSet приложения, состоящего из multitool. Обеспечить возможность чтения файла /var/log/syslog кластера MicroK8S.

```yml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: mypods
  labels:
    app: app
spec:
  selector:
    matchLabels:
      app: app
  template:
    metadata:
      labels:
        app: app
    spec:
      containers:
      - name: multitool
        image: wbitt/network-multitool
        command: ["/bin/sh", "-c", "sleep infinity"]
        volumeMounts:
        - name: var-log
          mountPath: /tmp
          readOnly: true
      volumes:
      - name: var-log
        hostPath:
          path: /var/log
          # type: File
```

## Продемонстрировать возможность чтения файла изнутри пода.

![Продемонстрировать возможность чтения файла изнутри пода](https://github.com/user-attachments/assets/f1e2e650-c47e-45ea-9b16-0739e4ef280d)















