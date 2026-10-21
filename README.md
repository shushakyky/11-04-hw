# Домашнее задание к занятию «Очереди RabbitMQ»

### Выполнил: Шушнов Александр

---

## Стенд

Две виртуальные машины в VirtualBox, поднятые через Vagrant ([Vagrantfile](Vagrantfile)):

| Хост | IP | ОС | RabbitMQ / Erlang |
| --- | --- | --- | --- |
| rmq01 | 192.168.56.10 | Ubuntu 22.04 | 3.9.27 / 24.2.1 |
| rmq02 | 192.168.56.11 | Ubuntu 22.04 | 3.9.27 / 24.2.1 |

Provision каждой машины: запись хостов в `/etc/hosts`, установка `rabbitmq-server`, одинаковый `.erlang.cookie`
(нужен для объединения в кластер), включение `rabbitmq_management` и создание пользователя `admin`
(пользователю `guest` подключаться не с localhost запрещено).

---

## Задание 1. Установка RabbitMQ

```bash
apt install rabbitmq-server
rabbitmq-plugins enable rabbitmq_management
rabbitmqctl add_user admin admin
rabbitmqctl set_user_tags admin administrator
rabbitmqctl set_permissions -p / admin ".*" ".*" ".*"
```

Веб-интерфейс `http://192.168.56.10:15672`:

![RabbitMQ web](img/01-rabbitmq-web.png)

---

## Задание 2. Отправка и получение сообщений

Скрипты запускаются с хостовой машины (Python 3.12, `pip install pika`, pika 1.4.4). В скриптах указан IP `rmq01`
и учётка `admin`; `consumer.py` поправлен под API pika 1.x (`basic_consume(queue=..., on_message_callback=..., auto_ack=True)`).

[producer.py](scripts/producer.py), [consumer.py](scripts/consumer.py)

После запуска `producer.py` в веб-интерфейсе появилась очередь `hello` с одним сообщением:

![queue hello](img/02-queue-hello.png)

Результат работы `consumer.py`:

![consumer](img/03-consumer.png)
