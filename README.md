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

## пошаговое руководство :
### настройка сети VPN (если ваши компьютеры в одной сети - пропустите этот шаг)
1. Зайдите в папку ansible и выполните команду:

```
ansible-playbook wg_start.yml --ask-vault-pass 

```
2. зайдите на сервер proxy(VPS) и отредактируйте файл /etc/wireguard/wg0.conf

  - в поле PrivateKey вставьте значение ключа privatekey
  - в поле Peer cloud_host вставьте значение ключа cloud_pubkey
  - в поле Peer monitor_host вставте значение ключа monitor_pubkey
   
    пример просмотра значения ключа: ``` cat /etc/wireguard/privatekey ```

3. выполните команду:

```
systemctl enable --now wg-quick@wg0

```
4. настройте клиенты сети cloud и monitor редактируя файл wg0.conf на них (должен быть установлен Wireguard)
5. проверьте работу сети c клиентов командой ``` ping 10.0.0.1 ```

### Запуск настройки серверов и приложений
1. впишите в файл hosts ip адреса ваших серверов в соответствии их назначений
2. в файле ansible/group_vars/all поменяйте значения переменных, по умолчанию они такие:
   - ansible_sudo_pass: password
   - kibana_host: 10.0.0.3 (host monitoring)
   - kibana_port: 5601
   - kibana_usr: elastic
   - kibana_pass: elastic
   - elk_host: 10.0.0.3
   - elk_port: 9200
   - elk_usr: elastic
   - elk_pass: elastic

3. выполните команду запуска автоматизации:
4. ``` ansible-playbook start.yml --ask-vault-pass ```
   ## при этом действии будет выполненно:
   ## на сервере proxy:
        - добавление необходимых репозиториев
        - обновление установленых пакетов, установка новых
        - создание нового  пользователя atlas(groups wheel)ssh-ключей для него
          
          имя пользователя можно изменить в :
          ansible/roles/create_user/tasks/user.yml
          
        - деплой контейнера с Nginx Proxy Manager
   ## на сервере cloud для ускорения демонстрации проекта будет выполнена только роль с деплое контейнеров Nextcloud и MYSQL
   ### для добавления ролей к выполнению отредактируйте файл start.yml и в секции "cloud" допишите необходимые

5. ### после завершения работы Ansible настройте Nginx Proxy Manager:
       - в браузере перейдите по адресу вашего vps сервера порт 81 прим.: http://ipaddres:81
![login](https://user-images.githubusercontent.com/59445051/195842873-929509d0-e56a-4d06-81ed-3ba7d4b93046.png)

### временные данные для авторицазии: 
    - email: admin@example.com
    - password: changeme
 6. далее добавьте и настройте прокси хост и добавьте SSL сертификаты для доступа через https:// с указанием ip adress сервера "cloud"
 ![Screenshot_20221013_152647](https://user-images.githubusercontent.com/59445051/195849656-0cd5541d-e1c5-4f80-8660-63e4a8f9c566.png)
![Screenshot_20221013_152800](https://user-images.githubusercontent.com/59445051/195850399-b599edcd-d962-46b2-a820-80f3c9fc0d69.png)

7. ## в браузере перейдите по адресу сервера "cloud" порт: 80 и введите данные:
        - имя админа и пароль - произвольные
   ## в секции "Настройка базы данных"
        - имя пользователя - root
        - пароль базы данных - db123 (меняется в ansible/roles/dc_nextcloud-mysql/templates/dc_nextcloud-mysql/docker-compose.yml)
        - имя базы данных - ncdb ( меняеться там же)
        - расположение - nc_db (имя контейнера с БД)

8. ## на сервере "cloud" отрадактируйте фаил /nc_app/config/config.php и добавьте блок кода "trusted_domains" следующее:
        - 1 => 'ip adress serv2 => 'you-domains.com'
        - 2 => 'you-domains.com',
        - после блока "trusted_domains" добавьте строку 'overwritehost' => 'your domain.com',
 ### пример конфига:
 
 ![Screenshot_20221014_161621](https://user-images.githubusercontent.com/59445051/195857635-619135b9-c190-41ab-8bfc-aed4ece3fc75.png)
