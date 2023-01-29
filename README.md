## Ansible Playbook для установки стека ClickHouse/Vector

Данный playbook разворачивает систему логирования на основе базы данных ClickHouse и системы сбора и обработки данных Vector.

Теги плейбука:
- `clickhouse` - установка, генерация конфигурации и запуск сервиса ClickHouse
- `clickhouse-db` - создание базы данных и пользователя для 
- `vector` - установка, генерация конфигурации и запуск сервиса Vector

Параметры и переменные плейбука:
- в `inventory/prod.yml` хранятся данные о хостах
- в `group_vars/all/vars.yml` содажится переменные:
  * `vector_clickhouse_password` - пароль для пользователя vector в ClickHouse (заменить существующее значение, **использовать:** `ansible-vault encrypt_string`)
- в `group_vars/clickhouse/vars.yml` содержатся переменные:
  * `clickhouse_version` - версия ClickHouse
  * `clickhouse_packages` - список пакетов для работы ClickHouse
- в group_vars/vector/vars.yml содержатся переменные:
  * `vector_version` - версия Vector
  * `vector_config` - конфигурация Vector. Рендерится в `vector.yml` с помощью `to_nice_yaml`

Jinja2 шаблоны:
- `config.xml.j2` - конфигурация сервера ClickHouse
- `users.xml.j2` - конфигурация сервера ClickHouse (пользователи)
- `vector.service.j2` - systemd unit для Vector
- `vector.yml.j2` - конфигурация Vector


Этапы работы плейбука:
1. Получение RPM-пакетов ClickHouse
2. Установка ClickHouse из полученных пакетов
3. Генерация конфигурации сервера ClickHouse
4. Запуск обработчика для перезапуска сервиса ClickHouse
5. Создание базы данных и пользователя для Vector
6. Создание пользователя в системе для Vector
7. Получение архива tar.gz Vector (скачивается в домашнюю директорию пользователя vector)
8. Создание необходимых директорий для Vector
9. Распаковка Vector
10. Генерация systemd unit для Vector
11. Рендеринг конфигурации для Vector из group_vars
12. Запуск обработчика для перезапуска сервиса Vector

Vector будет доступен по пути `/opt/vector`. Для ClickHouse используется стандартное расположение.

После использования плейбука необходимо дать права пользователю в БД и создать таблицу

Пример:

```
GRANT INSERT ON logs.* TO vector;
CREATE TABLE app_logs ("host" String, "file" String, "source_type" String, "message" String, "timestamp" DateTime) ENGINE=Log;
```
