# Для начала нужно установить Linux Oracle на VirtualBox:

Установить образ Linux

Выделить 4 ядра

Выделить 4096 мб оперативной памяти



# Установка docker

Устанавливает утилиту wget на систему

`sudo yum install wget`

![image](https://github.com/user-attachments/assets/dbe3beb2-2aa2-4514-84f2-ba918c01b631)

Скачиваем файл репозитория

`sudo wget -P /etc/yum.repos.d/ https://download.docker.com/linux/centos/docker-ce.repo`

![image](https://github.com/user-attachments/assets/9b835e6a-ceb6-463c-a765-c210c98ce1ee)

Устанавливаем docker

`sudo yum install docker-ce docker-ce-cli containerd.io`

![image](https://github.com/user-attachments/assets/8513ae86-f330-4faa-b1d2-d8d95dfb3fea)

Запускаем его и разрешаем автозапуск

`sudo systemctl enable docker --now`

![image](https://github.com/user-attachments/assets/7dd7c415-0dcc-4f34-8c95-a54ea0df24b2)



# Установка compose

Для начала нужно убедиться в наличии пакета curl

`sudo yum install curl`

![image](https://github.com/user-attachments/assets/ebcc6f9d-858f-4b31-85da-661414595e5e)

Объявление переменной COMVER, полученной в результате curl запроса, хранящей в себе номер последней
версии Docker Compose

`COMVER=$(curl -s https://api.github.com/repos/docker/compose/releases/latest | grep 'tag_name' | cut -d\" -f4)`

Теперь скачиваем скрипт docker-compose последней версии, используя объявленную ранее переменную и помещаем его в каталог /usr/bin

`sudo curl -L "https://github.com/docker/compose/releases/download/$COMVER/docker-compose-$(uname -s)-$(uname -m)" -o /usr/bin/docker-compose`

![image](https://github.com/user-attachments/assets/448e7919-c923-4cc3-9f5c-d8acfddb78a4)

Предоставление прав на выполнение файла docker-compose

`sudo chmod +x /usr/bin/docker-compose`

Проверка установленной версии Docker Compose

`sudo docker-compose --version`

![image](https://github.com/user-attachments/assets/ce583dc7-7904-45df-b653-72a02343d9b9)



# Делаем grafana

Установка git

`sudo yum install git`

![image](https://github.com/user-attachments/assets/7c6ed42f-297e-4003-99d7-0641ebb8749e)

Этот код скачивает содержимое репозитория skl256/grafana_stack_for_docker

`sudo git clone https://github.com/skl256/grafana_stack_for_docker.git`

Заходит в папку - cd

`cd grafana_stack_for_docker`

cd .. - возвращает в папку выше

![image](https://github.com/user-attachments/assets/458f469c-9ea7-4a65-afc8-a0165d6b61a0)

Cоздаем папки двумя разными способами

`sudo mkdir -p /mnt/common_volume/swarm/grafana/config`

`sudo mkdir -p /mnt/common_volume/grafana/{grafana-config,grafana-data,prometheus-data,loki-data,promtail-data}`

Выдаем права

`sudo chown -R $(id -u):$(id -g) {/mnt/common_volume/swarm/grafana/config,/mnt/common_volume/grafana}`

Создаем файл

`sudo touch /mnt/common_volume/grafana/grafana-config/grafana.ini`

Копирование файлов

`sudo cp config/* /mnt/common_volume/swarm/grafana/config/`

Переименовывание файла

`sudo mv grafana.yaml docker-compose.yaml`

![image](https://github.com/user-attachments/assets/0eff5b93-209e-4701-94b2-6fdeadc72e7b)

Собрать докер (нужно запускать из папки где docker-compose.yaml)

`sudo docker compose up -d`

Опустить докер - sudo docker compose stop

![image](https://github.com/user-attachments/assets/e3b10e0d-402c-40ea-a693-62eed116ef21)



# Начинаем чистку файлов

Команда открывает файл docker-compose.yaml в текстовом редакторе vi с правами суперпользователя

`sudo vi docker-compose.yaml`

Что-бы что-то изменить в тесковом редакторе нужно нажать insert на клавиатуре

Затем в docker-compose нужно вставить node-exporter и удалить ненужные файлы (можно вставить готовый докер)

![image](https://github.com/user-attachments/assets/259220f9-0dc4-4296-b0d2-6fbbdc34cf6b)

выйти не сохраняясь из vim - `esc -> :q!`

выйти сохраняясь из vim - `esc -> :wq!`

Заходим в другую папку 

`cd /mnt/common_volume/swarm/grafana/config/`

Открываем файл prometheus.yaml в текстовом редакторе vi с правами суперпользователя

`sudo vi prometheus.yaml`

![image](https://github.com/user-attachments/assets/42c4880b-e56f-45a1-9077-8bc4972354be)

Далее нужно исправить targets: на exporter:9100

![image](https://github.com/user-attachments/assets/850c7813-b767-4cb8-a01b-5c57e168a6fc)



# Делаем grafana на сайте

- Заходим на сайт localhost:3000

- Регестрируемся на сайте
     - User и Password - admin, потом где он предлагает установить пароль, нажимаем - скип

- Открываем Dashboards - create dashboard - configure a new data source - prometheus
     - Connection: http://prometheus:9090
     - Authentication - basic authentication - admin admin
     - save and test

- Dashboards - import dashboard
     - Find and import... - 1860 - load
     - prometheus - prometheus
     - import

![image](https://github.com/user-attachments/assets/d550635b-e67e-4337-bd60-b669b032f89f)



# Делаем VictoriaMetrics

Для начала зайдем в нужную папку

`cd grafana_stack_for_docker`

Открываем docker-compose

`sudo vi docker-compose.yaml`

После prometheus вставляем vmagent (но у нас уже вставлен готовый докер)

![image](https://github.com/user-attachments/assets/c6d0b8ee-ebc0-425b-a0ec-a2514620506c)

Открываем grafana на сайте и также создаем dashboard, но в Connection пишем http://victoriametrics:8428

Заменяем имя из "Prometheus-2" в "Vika" нажимаем на dashboards add visualition выбираем "Vika" снизу меняем на "code"

В терминале пишем:

`echo -e "# TYPE OILCOINT_metric1 gauge\nOILCOINT_metric1 0" | curl --data-binary @- http://localhost:8428/api/v1/import/prometheus`

команда отправляет бинарные данные (например, метрики в формате Prometheus) на локальный сервер

Потом вводим команду которая делает запрос к API для получения данных по метрике OILCOINT_metric1

`curl -G 'http://localhost:8428/api/v1/query' --data-urlencode 'query=OILCOINT_metric1'`

Значение 0 меняем на любое другое

![image](https://github.com/user-attachments/assets/3fb7d4ee-43da-47cb-aa61-204b35ad5f9e)

Заходим на сайт localhost:8428 и открываем vmui

Копируем переменную OILCOINT_metric1 и вставляем в query

![image](https://github.com/user-attachments/assets/f41a4dbe-3b33-4d13-b345-d21dadeaab1a)

![image](https://github.com/user-attachments/assets/72c330c7-1762-442d-bac5-7a38da7fd157)

Нажимаем run

Копируем переменную OILCOINT_metric1 и вставляем в code

![image](https://github.com/user-attachments/assets/a35f9ebb-224c-4715-892f-7339d3feda31)
