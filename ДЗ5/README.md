### ЗАДАНИЕ
![image](https://github.com/user-attachments/assets/7fa68b26-e19c-4d91-8922-543e4db17650)

### установка
```
используемая ОС Redos 7.3.5

dnf install docker

mkdir /etc/containers/nodocker

vim /etc/containers/registries.conf
unqualified-search-registries = ["docker.io"]

wget -qO - https://raw.githubusercontent.com/percona/pmm/refs/heads/v3/get-pmm.sh | /bin/bash

wget https://repo.percona.com/pmm3-client/yum/release/2023/RPMS/x86_64/pmm-client-3.2.0-7.amzn2023.x86_64.rpm

dnf localinstall pmm-client-3.2.0-7.amzn2023.x86_64.rpm

pmm-admin config --server-insecure-tls --server-url=https://admin:Root@192.168.1.150:443

psql
CREATE USER pmm WITH SUPERUSER ENCRYPTED PASSWORD 'pmm';

pmm-admin add postgresql --username=pmm --password=pmm
```
### пример собираемой статистики запросов
![image](https://github.com/user-attachments/assets/d14fa464-ddb5-4491-9435-5d1e7dedd2f4)

### алерты настраиваются в разделе Alerting. настроить на тестовом стенде пока что не представляется возможным. возникают ошибки шаблона. разбираюсь
