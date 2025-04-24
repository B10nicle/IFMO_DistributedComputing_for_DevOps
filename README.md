## 📦 Distributed Computing Project: Ansible + Docker + PostgreSQL + Strapi

Проект автоматизирует развёртывание CMS Strapi и PostgreSQL-кластера (мастер + слейв) с помощью **Ansible**, **Docker** и централизованного управления конфигурацией через `.env`.

---

### 📌 Зависимости

- Ansible 2.15+
- Коллекция `community.docker`
- Docker установлен на целевой машине

---

### 📁 Структура

```
IFMO_DistributedComputing_for_DevOps/
├── .env                  # Переменные окружения
├── .env.example          # Шаблон .env
├── .gitignore
├── ansible.cfg
├── inventory.yml         # Инвентарь с переменными из .env
├── Makefile              # Алиасы (hw1, hw2, check, clean)
├── README.md

├── group_vars/
│   └── all.yml           # Глобальные переменные Ansible (читают .env)

├── playbooks/
│   ├── check_cluster.yml # Проверка репликации (чтение + write-фейл)
│   ├── cleanup.yml       # Очистка контейнеров, volume, сети
│   ├── hw1.yml           # Развёртывание Strapi и master PostgreSQL
│   └── hw2.yml           # Добавление реплики к master

├── roles/
│   ├── pg_cluster_master/
│   │   └── tasks/
│   │       └── main.yml  # Настройка PostgreSQL master
│   ├── pg_cluster_replica/
│   │   └── tasks/
│   │       └── main.yml  # Настройка PostgreSQL replica
│   └── strapi/
│       └── tasks/
│           └── main.yml  # Развёртывание CMS Strapi
```

---

### ⚙️ Пример .env

```env
# SSH
VM1_HOST_IP=
VM1_SSH_USER=
VM1_SSH_KEY_PATH=

# PostgreSQL / Strapi
PG_REPLICATION_USER=
PG_REPLICATION_PASSWORD=
PG_USERNAME=
PG_PASSWORD=
PG_DATABASE=
PG_POSTGRES_PASSWORD=

# Статус-проверка Strapi
STRAPI_CHECK_DELAY=
STRAPI_CHECK_RETRIES=
```

> Используется `group_vars/all.yml` для подстановки значений из `.env`.

---

### 🚀 Команды Makefile

| Команда         | Действие                                                   |
|------------------|------------------------------------------------------------|
| `make init`      | Загружает переменные окружения из `.env`                  |
| `make install-deps` | Устанавливает Docker-коллекцию Ansible                |
| `make hw1`       | Разворачивает Strapi и PostgreSQL master (`hw1.yml`)     |
| `make hw2`       | Добавляет PostgreSQL реплику (`hw2.yml`)                 |
| `make check`     | Проверяет репликацию и read-only статус (`check_cluster.yml`) |
| `make clean`     | Удаляет все контейнеры, volumes и сети (`cleanup.yml`)    |
| `make ping`      | Проверяет SSH-доступ к хосту                              |

---

### ✅ Функциональность

- 📆 Развёртывание PostgreSQL master через Bitnami-образ
- 🔁 Добавление PostgreSQL replica с WAL-репликацией
- 🌐 Strapi CMS подключается к мастеру через Docker-сеть
- 🔐 Все пароли и параметры — в `.env`, не хранятся в плейбуках
- 🧠 Проверка через `check_cluster.yml`:
    - наличие таблицы и данных на реплике
    - запрет записи на слейв
