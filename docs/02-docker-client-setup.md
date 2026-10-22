# Настройка Docker-клиента

## 1. Установка Docker
```bash
sudo apt update
sudo apt install -y docker.io docker-compose-v2
sudo systemctl enable --now docker
sudo usermod -aG docker vagrant
# Перезайти в сессию
```
2. Настройка доверия к сертификату Harbor
```bash
# Создаем папку (имя папки должно точно совпадать с hostname Harbor)
sudo mkdir -p /etc/docker/certs.d/registry.harbor.local/

# Копируем публичный CA из общей папки
sudo cp /vagrant/ca.crt /etc/docker/certs.d/registry.harbor.local/

# Перезапускаем Docker, чтобы он подхватил сертификат
sudo systemctl restart docker
```
3. Проверка и первый Push
```bash
# Логин
docker login registry.harbor.local

# Скачиваем тестовый образ (можете качать любой альпайн тут для теста)
docker pull alpine:latest

# Тегируем для нашего Harbor (проект test должен быть создан в веб-интерфейсе)
docker tag alpine:latest registry.harbor.local/test/alpine:v1.0

# Пушим
docker push registry.harbor.local/test/alpine:v1.0
