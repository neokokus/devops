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