# Laboratory: Harbor, Docker, Falco, Rsyslog
Этот репозиторий содержит документацию и конфигурацию для тестового стенда.
## Архитектура
![Схема архитектуры](Documents/arc.jpg)
Стенд состоит из двух виртуальных машин в приватной сети:

1. **`harbor.local` (192.168.56.11)** — 4 GB RAM, 2 CPU.
   - Приватный реестр образов (Harbor).
   - Сканер уязвимостей (Trivy).
   - (В планах) Falco и Rsyslog.
2. **`docker.local` (192.168.56.12)** — 2 GB RAM, 1 CPU.
   - Docker Engine (клиент).
   - Falco и Rsyslog.

## Используемые версии

- **Vagrant:** 2.4.9
- **VirtualBox:** 7.2
- **OS:** Ubuntu 24.04 LTS (bento/ubuntu-24.04)
- **Docker Engine:** 29.1.3
- **Docker Compose:** 2.40.3
- **Harbor:** v2.14.2

## Быстрый старт (как поднять заново)

## 🚀 Быстрый старт (как поднять заново)

1. Убедитесь, что установлены Vagrant и VirtualBox.
2. Склонируйте репозиторий.
3. Выполните `vagrant up` (поднимутся **три** ВМ: `harbor`, `docker`, `storage`).
4. **Если при установке пакетов возникает ошибка `Temporary failure resolving`**, выполните фикс DNS на всех ВМ (см. [docs/00-dns-fix.md](docs/00-dns-fix.md)).
5. Пропишите в `/etc/hosts` на **всех трех ВМ** (и на вашем ПК для доступа к веб-интерфейсу):
	```text
	192.168.56.10 storage.local
	192.168.56.11 registry.harbor.local
	192.168.56.12 docker.local 
	```
6. Настройте NFS-хранилище на ВМ storage и подключите его к Harbor (см. docs/03-storage-nfs.md) *Необходимо далее для storage.local.
7. На ВМ harbor: установите Docker, сгенерируйте TLS-сертификаты и запустите Harbor (см. docs/01-harbor-setup.md).
8. На ВМ docker: установите Docker, настройте доверие к сертификату и сделайте первый docker push (см. docs/02-docker-client-setup.md).


- [x] Созданы 2 ВМ (Ubuntu 24.04) через Vagrant.
![VirtualBox](Documents/VM.png)
- [x] Настроена сеть и пропиcаны /etc/hosts на всех узлах.
![hosts](Documents/hosts.png)
- [x] Установлен Docker и Docker Compose на ВМ harbor.
- [x] Сгенерированы TLS-сертификаты (CA, harbor.crt, harbor.key).
![Certs](Documents/certs.png)
- [x] Установлен Harbor (v2.14.2) с Trivy. Доступен по адресу https://registry.harbor.local.
![Harbor](Documents/harbor_status.png)
![Дашборд Harbor](Documents/harborApi.png)
- [x] Настроен Docker на ВМ `docker`.
![Проверка работоспособности докера](Documents/docker_work.png)
- [x] Настроено доверие к сертификату Harbor.
![Certs](Documents/dockercerts.png)
- [x] Создан проект `test` в Harbor.
![Проект test](Documents/test.png)
- [x] Выполнен первый успешный `docker push` (образ alpine:v1.0).
![Докер пуш](Documents/docker_push_harbor.png)
![Успешный пуш (Дашборд Харбор](Documents/pushAlp.png)
- [x] Установлен и испытан Falco 
![Успешный пуш (Дашборд Харбор](Documents/falco_test.png)
- [x] Установлен и испытан Rsyslog
![Успешный сбор логов rsyslog на ВМ harbor](Documents/rsyslog(read).png)