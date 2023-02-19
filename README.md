## Ansible Playbook для разворачивания CVL-stack (ClickHouse, Vector, LightHouse)

Playbook разворачивает систему логирования на основе базы данных ClickHouse, системы сбора/обработки данных Vector и web-интерфейса доступа к данным LightHouse.

---

Теги playbook:
- `clickhouse` - установка, генерация config и запуск сервиса ClickHouse
- `clickhouse-db` - создание базы данных и пользователя для Vector
- `vector` - установка, генерация config и запуск сервиса Vector
- `lighthouse` - установка Nginx, клонирование  репозитория и генерация config (Nginx) 

---

Параметры и переменные playbook:
- в `inventory/prod.yml` хранятся данные о hosts
- в `group_vars/all/vars.yml` содержатся переменные:
  * `vector_clickhouse_password` - пароль для пользователя Vector в ClickHouse (заменить существующее значение, **использовать:** `ansible-vault encrypt_string`)
- в `group_vars/clickhouse/vars.yml` содержатся переменные:
  * `clickhouse_version` - версия ClickHouse
  * `clickhouse_packages` - список пакетов для работы ClickHouse
- в `group_vars/lighthouse/vars.yml` содержатся переменные:
  * `lighthouse_branch` - ветка репозитория LightHouse
  * `lighthouse_dir` - директория для LightHouse
- в `group_vars/vector/vars.yml` содержатся переменные:
  * `vector_version` - версия Vector
  * `vector_config` - конфигурация Vector. Генерируется в `vector.yml` с помощью `to_nice_yaml`

---

Jinja2 шаблоны:
- `config.xml.j2` - конфигурация сервера ClickHouse
- `lighthouse.j2` - конфигурация сервера Nginx для LightHouse
- `users.xml.j2` - конфигурация сервера ClickHouse (пользователи)
- `vector.service.j2` - systemd unit для Vector
- `vector.yml.j2` - конфигурация Vector

---

Этапы работы playbook:
1. Получение rpm-пакетов ClickHouse
2. Установка ClickHouse из полученных пакетов
3. Генерация config сервера ClickHouse
4. Запуск обработчика для перезапуска сервиса ClickHouse
5. Создание базы данных и пользователя для Vector
6. Создание пользователя в системе для Vector
7. Получение архива tar.gz Vector (скачивается в домашнюю директорию пользователя `vector`)
8. Создание необходимых директорий для Vector
9. Распаковка Vector
10. Генерация systemd unit для Vector
11. Генерация config для Vector из `group_vars`
12. Запуск обработчика для перезапуска сервиса Vector
13. Установка необходимых пакетов для LightHouse (epel-release, nginx, git)
14. Клонирование репозитория с LightHouse
15. Установка SELinux Type для директории
16. Генерация config Nginx для LightHouse
17. Запуск обработчика для перезапуска сервиса Nginx

---

ClickHouse располагается в директории `/etc/clickhouse`.

LightHouse располагается в директории `/usr/share/nginx/html/lighthouse`

Vector располагается в директории`/opt/vector`. 

После успешного завершения playbook tasks пользователю необходимо дать права в БД и создать таблицу для логов.

Пример:

```sql
GRANT INSERT ON logs.* TO vector;
CREATE TABLE app_logs ("host" String, "file" String, "source_type" String, "message" String, "timestamp" DateTime) ENGINE=Log;
```
