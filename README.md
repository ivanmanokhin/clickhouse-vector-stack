## Ansible Playbook для разворачивания CVL-stack (ClickHouse, Vector, LightHouse)

Playbook разворачивает систему логирования на основе базы данных ClickHouse, системы сбора/обработки данных Vector и WEB-интерфейса доступа к данным LightHouse.

Исползует роли:
- [ansible-clickhose](https://github.com/AlexeySetevoi/ansible-clickhouse.git)
- [vector-role](https://github.com/ivanmanokhin/vector-role)
- [lighthouse-role](https://github.com/ivanmanokhin/lighthouse-role)

---
Перед запуском требуется установить роли: `ansible-galaxy install -r requirements.yml -p roles`

---

Теги playbook:
- `clickhouse` - установка, генерация config'а и запуск сервиса ClickHouse
- `vector` - установка, генерация config'а и запуск сервиса Vector
- `lighthouse` - установка Nginx, генерация config'а, клонирование репозитория LightHouse
