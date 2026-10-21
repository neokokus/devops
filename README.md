# Laboratory: Harbor, Docker, Falco, Rsyslog

Этот репозиторий содержит документацию и конфигурацию для тестового стенда.

## Архитектура
(тут позже будет схема)

## Статус
- [x] Созданы 2 ВМ (Ubuntu 24.04) через Vagrant.
- [x] Настроена сеть (harbor.local - .11, docker.local - .12) и прописаны /etc/hosts.
- [x] Установлен Docker и Docker Compose на ВМ harbor.
- [x] Сгенерированы TLS-сертификаты (CA, harbor.crt, harbor.key).
- [x] Установлен Harbor (v2.14.2) с Trivy. Доступен по адресу https://registry.harbor.local