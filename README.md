# Для начала нужно установить Linux Oracle на VirtualBox:

Установить образ Linux

Выделить 4 ядра

Выделить 4096 мб оперативной памяти



# Установка docker

Устанавливает утилиту wget на систему

`sudo yum install wget`

![1](https://github.com/user-attachments/assets/7080fcfe-dba3-4c01-8c80-14760da2d679)

Скачиваем файл репозитория

`sudo wget -P /etc/yum.repos.d/ https://download.docker.com/linux/centos/docker-ce.repo`

![2](https://github.com/user-attachments/assets/a9e12c8b-0519-467b-aea1-ccff0bb458ec)

Устанавливаем docker

`sudo yum install docker-ce docker-ce-cli containerd.io`

![3](https://github.com/user-attachments/assets/c41c916e-d371-4db1-8690-7f1d0033501d)

Запускаем его и разрешаем автозапуск

`sudo systemctl enable docker --now`

![4](https://github.com/user-attachments/assets/b8ab9bfe-d788-4b85-be73-721b654c0703)



# Установка compose

Для начала нужно убедиться в наличии пакета curl

`sudo yum install curl`

![5](https://github.com/user-attachments/assets/8f06b64e-f387-495c-b658-35a3a974c65a)

Объявление переменной COMVER, полученной в результате curl запроса, хранящей в себе номер последней
версии Docker Compose

`COMVER=$(curl -s https://api.github.com/repos/docker/compose/releases/latest | grep 'tag_name' | cut -d\" -f4)`

Теперь скачиваем скрипт docker-compose последней версии, используя объявленную ранее переменную и помещаем его в каталог /usr/bin

`sudo curl -L "https://github.com/docker/compose/releases/download/$COMVER/docker-compose-$(uname -s)-$(uname -m)" -o /usr/bin/docker-compose`

![6](https://github.com/user-attachments/assets/4651baef-ca76-4a8d-a7db-b0f559f08a8d)

Предоставление прав на выполнение файла docker-compose

`sudo chmod +x /usr/bin/docker-compose`

Проверка установленной версии Docker Compose

`sudo docker-compose --version`

![7](https://github.com/user-attachments/assets/a850321e-a6e5-4d19-b156-9145c6ad6bc1)



# Делаем grafana

Установка git

`sudo yum install git`

![8](https://github.com/user-attachments/assets/09ab8707-deb9-483d-9de3-86b9468bc5fb)

Этот код скачивает содержимое репозитория skl256/grafana_stack_for_docker

`sudo git clone https://github.com/skl256/grafana_stack_for_docker.git`

Заходит в папку - cd

`cd grafana_stack_for_docker`

cd .. - возвращает в папку выше

![9](https://github.com/user-attachments/assets/fdada31f-6e3c-4b9f-ad70-247655b04907)

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

![10](https://github.com/user-attachments/assets/ee891dcf-79f7-4066-9e2d-33bdd587762c)

Собрать докер (нужно запускать из папки где docker-compose.yaml)

`sudo docker compose up -d`

Опустить докер - sudo docker compose stop

![11 1](https://github.com/user-attachments/assets/b02d38c0-8708-4baf-8e42-2af743c44e92)

![11 2](https://github.com/user-attachments/assets/4b73523b-821d-4fd7-951a-64b3ee99eacc)



# Начинаем чистку файлов

Команда открывает файл docker-compose.yaml в текстовом редакторе vi с правами суперпользователя

`sudo vi docker-compose.yaml`

Что-бы что-то изменить в тесковом редакторе нужно нажать insert на клавиатуре

Затем в docker-compose нужно вставить node-exporter и удалить ненужные файлы (можно вставить готовый докер)

![12](https://github.com/user-attachments/assets/1032e3ae-da21-4bb6-bc58-cdf0c7de66e4)

выйти не сохраняясь из vim - `esc -> :q!`

выйти сохраняясь из vim - `esc -> :wq!`

Заходим в другую папку 

`cd /mnt/common_volume/swarm/grafana/config/`

Открываем файл prometheus.yaml в текстовом редакторе vi с правами суперпользователя

`sudo vi prometheus.yaml`

![13](https://github.com/user-attachments/assets/55b779b4-df15-4588-bcc3-0956b7478f23)

Далее нужно исправить targets: на exporter:9100

![14](https://github.com/user-attachments/assets/1984dbe9-1005-476c-a295-ee13bc522244)



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

![15](https://github.com/user-attachments/assets/b0e2301d-968f-4e56-9be4-8cdb41de3a57)



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

![16](https://github.com/user-attachments/assets/e1f672c3-311f-4007-bec6-143b4a38bf1d)

Заходим на сайт localhost:8428 и открываем vmui

Копируем переменную OILCOINT_metric1 и вставляем в query

![17](https://github.com/user-attachments/assets/815d7309-08dd-4e52-87a0-a9607bfe05a6)

![18](https://github.com/user-attachments/assets/44ed1aec-6ee1-4a46-bd0d-da01741bea72)

Нажимаем run

Копируем переменную OILCOINT_metric1 и вставляем в code

![19](https://github.com/user-attachments/assets/c782acf3-8dd4-494a-b171-3b0dc8ef6479)
