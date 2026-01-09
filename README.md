# nginx-app

# Установка и запуск NGINX и серверного приложения с помощью Docker

## Содержание



1. [1. Запуск NGINX](#1-запуск-nginx)
2. [2. Управление Контейнерами NGINX](#2-управление-контейнерами-nginx)
3. [3. Сборка Docker-образа](#3-сборка-docker-образа)
4. [4. Создание Docker-сети](#4-создание-docker-сети)
5. [5. Запуск Backend Приложений](#5-запуск-backend-приложений)
6. [6. Запуск Второго Контейнера NGINX](#6-запуск-второго-контейнера-nginx)
7. [Дополнительные Рекомендации](#дополнительные-рекомендации)

### 1. Запуск NGINX

Запуск контейнера NGINX с пробросом порта 80 на локальный порт 8070:

```bash
docker run --name nginx-server -p 8070:80 -d nginx:latest
```

### 2. Управление контейнерами NGINX

Проверка запущенных контейнеров:

```bash
docker ps
```

Удаление существующего контейнера NGINX:

```bash
docker rm -f nginx-server
```

Запуск нового контейнера NGINX с монтированием локальной директории для сервера:

```bash
docker run --name nginx-server -p 8070:80 \
-v /D:/projects/nginx-test/html:/usr/share/nginx/html \
-d nginx
```

### 3. Сборка Docker-образа

Создание Docker-образа для backend приложения.

```bash
docker build --tag=backendapp:latest .
```

### 4. Создание Docker-сети

Создание собственной Docker-сети для связи контейнеров backend приложений:

```bash
docker network create backend-network
```

### 5. Запуск Backend Приложений

Запуск пяти экземпляров backend приложения, подключённых к созданной сети:

```bash
docker run --network=backend-network --name backendapp1 -p 8091:8090 --hostname=backendapp1 -d backendapp
docker run --network=backend-network --name backendapp2 -p 8092:8090 --hostname=backendapp2 -d backendapp
docker run --network=backend-network --name backendapp3 -p 8093:8090 --hostname=backendapp3 -d backendapp
docker run --network=backend-network --name backendapp4 -p 8094:8090 --hostname=backendapp4 -d backendapp
docker run --network=backend-network --name backendapp5 -p 8095:8090 --hostname=backendapp5 -d backendapp
```

### 6. Запуск Второго Контейнера NGINX

Запуск дополнительного контейнера NGINX, который будет использовать собственный конфигурационный файл и подключён к
той же сети:

```bash
docker run --network=backend-network --name nginx-server -p 8070:8080 \
-v /D:/projects/nginx-test/nginx.conf:/etc/nginx/nginx.conf \
-d nginx
```

## Дополнительные Рекомендации

Проверка Конфигурации NGINX: После запуска контейнеров убедитесь, что NGINX корректно обрабатывает запросы к backend
приложениям. Проверьте логи NGINX для выявления возможных ошибок:

```bash
docker logs nginx-server
docker logs nginx-server-2
``