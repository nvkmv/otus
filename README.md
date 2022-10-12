 Описание 
## настройка серверов и запуск  приложений в Docker контейнерах автоматизировано при помощи Ansible
### проект размещен на трех серверах:
1. name : cloud  
- env (create user, add repositories, install and update packages)
- Nextcloud (docker-compose)
- MYSQL database (docker-compose) 
- filebeat, metricbeat for ELK
- node-exporter for Prometheus/Grafana

2. name: proxy
- env (create user, add repositories, install and update packages)
- Nginx Proxy manager (docker-compose)
- Wireguard (dnf package)
- filebeat, metricbeat for ELK
- node-exporter for Prometheus/Grafana

3. name: monitoring
- Prometheus/Grafana (docker-compose)
- ELK stack ( docker-compose)
- backup and replication MYSQL detabase (docker-compose)

