# Домашнее задание к занятию "`Практическое применение Docker`" - `Татаринцев Алексей`


#
---

### Задание 0 и 1


1. `Проверяю, что docker-compose  не установлен`
```
alexey@dell:~/practice_docker$ docker-compose --version

Команда «docker-compose» не найдена, но может быть установлена с помощью:

sudo snap install docker          # version 27.5.1, or
sudo apt  install docker-compose  # version 1.25.0-1

See 'snap info docker' for additional versions.

```


2. `Провеверяю какая версия Docker Compose `
```
alexey@dell:~/practice_docker$ docker compose version
Docker Compose version v2.24.5
```
3. `Сделал fork`
```
https://github.com/Foxbeerxxx/shvirtd-example-python
```
4. `Создаю и дописываю Dockerfile.python`
```
FROM python:3.9-slim
WORKDIR /app
COPY . .
RUN pip install --no-cache-dir -r requirements.txt
CMD ["python", "not_tested_main.py"] 
```
5. `Запускаю docker compose up -d`

![1](https://github.com/Foxbeerxxx/practice_docker/blob/main/img/img1.png)


---

### Задание 3


1. `Создаю файл compose.yaml и записываю наполнение`
```
version: '3.8'

include:
  - proxy.yaml

services:
  web:
    build:
      context: .
      dockerfile: Dockerfile.python
    restart: always
    networks:
      backend:
        ipv4_address: 172.20.0.5
    environment:
      MYSQL_HOST: db
      MYSQL_DATABASE: ${MYSQL_DATABASE}
      MYSQL_USER: ${MYSQL_USER}
      MYSQL_PASSWORD: ${MYSQL_PASSWORD}
    depends_on:
      - db

  db:
    image: mysql:8
    restart: always
    networks:
      backend:
        ipv4_address: 172.20.0.10
    environment:
      MYSQL_ROOT_PASSWORD: ${MYSQL_ROOT_PASSWORD}
      MYSQL_DATABASE: ${MYSQL_DATABASE}
      MYSQL_USER: ${MYSQL_USER}
      MYSQL_PASSWORD: ${MYSQL_PASSWORD}
    volumes:
      - mysql_data:/var/lib/mysql

volumes:
  mysql_data:
```
2. `Запускаю docker compose up -d и проверяю curl`

```
alexey@dell:~/shvirtd-example-python$ curl -L http://127.0.0.1:8090
<html><head><meta http-equiv='refresh' content='1;url=/login?from=%2F'/><script id='redirect' data-redirect-url='/login?from=%2F' src='/static/8880369f/scripts/redirect.js'></script></head><body style='background-color:white; color:white;'>
Authentication required
<!--
-->

</body></html>                                                                                                                                                                                                                                                                                                            alexey@dell:~/shvirtd-example-python$ 

```


3. `Подключаюсь к sql`
```
docker exec -it shvirtd-example-python-db-1 mysql -uroot -pYtReWq4321
```
![2](https://github.com/Foxbeerxxx/practice_docker/blob/main/img/img2.png)


---

### Задание 4



1. `Ссоздал ВМ на yandex cloud`

![3](https://github.com/Foxbeerxxx/practice_docker/blob/main/img/img3.png)

2. `Подключился к ней и пишу Bash скрипт alexey.sh для установки докера и докер композ`

![4](https://github.com/Foxbeerxxx/practice_docker/blob/main/img/img4.png)

```
#!/bin/bash

# Установка Docker
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io

# Клонирование репозитория и запуск проекта
sudo mkdir -p /opt
cd /opt
sudo git clone https://github.com/Foxbeerxxx/shvirtd-example-python.git
cd shvirtd-example-python

# Запуск контейнеров
sudo docker-compose up -d
```
![5](https://github.com/Foxbeerxxx/practice_docker/blob/main/img/img5.png)

![6](https://github.com/Foxbeerxxx/practice_docker/blob/main/img/img6.png)

3. `ссылка на fork`
```
https://github.com/Foxbeerxxx/shvirtd-example-python
```


### Задание 6

1. `Скачивание образа Terraform`
```
docker pull hashicorp/terraform:latest
```
![7](https://github.com/Foxbeerxxx/practice_docker/blob/main/img/img7.png)

2. `Запускаю анализ dive`
```
dive hashicorp/terraform:latest
```
![8](https://github.com/Foxbeerxxx/practice_docker/blob/main/img/img8.png)

3. `Проверяем вручну manifest.json`

```
# Проверяем первый слой
tar -tf 051fa37b6c1a0c02ffa19c193405eb7116661cde224e577ade0de869d203816b/layer.tar | grep "bin/terraform"

# Проверяем второй слой
tar -tf b6716ac15f5ba9f6c9cab43972d5a0e6544d0273d4a1670791d8e64bcb975ece/layer.tar | grep "bin/terraform"

# Проверяем третий слой
tar -tf 67703911f78805cbb6b6d7a0f3d06d55a59eb04d8bdd91e9827214a9a3fd7b37/layer.tar | grep "bin/terraform"

# Проверяем четвертый слой
tar -tf f03fd3159750ff999526950569b5bc04dab003e98d85a7155b153abff6f9678a/layer.tar | grep "bin/terraform"

```
![9](https://github.com/Foxbeerxxx/practice_dockernew/blob/main/img/img9.png)

4. `Извлечение бинарного файла`

```
tar -xvf f03fd3159750ff999526950569b5bc04dab003e98d85a7155b153abff6f9678a/layer.tar bin/terraform
```

5. `Проверка работоспособности`

```
chmod +x bin/terraform
./bin/terraform version
```
![10](https://github.com/Foxbeerxxx/practice_dockernew/blob/main/img/img10.png)

6. `Копирование на локальную машину`
```
cp bin/terraform ~/bin/  # или в другую удобную директорию
```

