# Настройка NFS-хранилища для Harbor (ВМ harbor)

## Проблема
При использовании NFS (сетевого хранилища) в Harbor возникает известный баг: Docker не может смонтировать файл `root.crt` (видит директорию вместо файла). Из-за этого контейнер `registry` падает в бесконечный цикл перезапуска.

## Решение (Обходной путь)
1. В файле `harbor.yml` укажите `data_volume: /mnt/storage/harbor_data` (вместо `data_volume: /data`) .
2. Выполните `sudo ./prepare` (НЕ `install.sh`).
3. Откройте `docker-compose.yml` (сервис `registry`) и закомментируйте блок монтирования `root.crt`:
   ```yaml
   # - type: bind
   #   source: /mnt/storage/harbor_data/secret/registry/root.crt
   #   target: /etc/registry/root.crt
   ```
## Подключение NFS к Docker-клиенту (ВМ docker)

```bash
sudo apt update
sudo apt install -y nfs-common
sudo mkdir -p /mnt/storage
sudo mount -t nfs 192.168.56.10:/srv/nfs/storage /mnt/storage
echo '192.168.56.10:/srv/nfs/storage /mnt/storage nfs defaults 0 0' | sudo tee -a /etc/fstab
```
## Итог по NFS 

**Вывод:** Использование NFS для базы данных Harbor — это тупиковый путь в рамках данного тестового стенда.

**Причина:** PostgreSQL (база данных Harbor) категорически отказывается работать с правами `root` и не может корректно создавать файлы на NFS из-за ограничений прав доступа (UID 999). Все попытки обойти это (chmod 777, chown) приводят к ошибке `Permission denied` при инициализации базы.

**Решение:** 
1. Хранилище для данных Harbor возвращено на локальный диск виртуалки (`data_volume: /data`).
2. NFS-сервер (`storage.local`) оставлен в инфраструктуре как отдельный сетевой диск, который смонтирован на обе машины (`harbor` и `docker`) в `/mnt/storage`. Он может использоваться для хранения логов, бэкапов или других задач, не связанных с базой данных Harbor.

**Итог:** Функционал Harbor (сканирование уязвимостей, роли, репликация) важнее, чем борьба с NFS. Стенд работает стабильно.