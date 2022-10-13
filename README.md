 Описание:
 
## сервис с облаком Nextcloud, базой данных MYSQL и ее backup, доступом по domain name (Nginx Proxy Manager, https connect)+VPN (Wireguard) и мониторинг этого с помощию Grafna/Prometheus и ELK stack
## настройка серверов и запуск  приложений в Docker контейнерах автоматизировано при помощи Ansible
### пароли и переменные хранятся в зашифрованом файле
### проект размещен на трех серверах c установленными и настроенными приложениями и сервисами:

1. name : cloud  (dedic) 
     - env (create user, add repositories, install and update packages)
     - Nextcloud (docker-compose)
     - MYSQL database (docker-compose) 
     - filebeat, metricbeat for ELK
     - node-exporter for Prometheus/Grafana

2. name: proxy (VPS)
     - env (create user, add repositories, install and update packages)
     - Nginx Proxy manager (docker-compose)
     - filebeat, metricbeat for ELK
     - node-exporter for Prometheus/Grafana

3. name: monitoring (localhost)
     - Prometheus/Grafana (docker-compose)
     - ELK stack ( docker-compose)
     - backup and replication MYSQL detabase (docker-compose)

