# Установка и настройка Harbor

## 1. Установка Docker
```bash
sudo apt update
sudo apt install -y docker.io docker-compose-v2
sudo systemctl enable --now docker
sudo usermod -aG docker vagrant
# Не забудьте перезайти в сессию (exit -> vagrant ssh), чтобы применились права
```
## 2. Генерация TLS-сертификатов
```bash
mkdir -p /home/vagrant/certs
cd /home/vagrant/certs

# Генерация CA (Корневого сертификата)
openssl genrsa -out ca.key 4096
openssl req -x509 -new -nodes -sha512 -days 3650 \
  -subj "/C=RU/ST=Moscow/L=Moscow/O=example/OU=My company/CN=registry.harbor.local" \
  -key ca.key -out ca.crt

# Генерация ключа и запроса для Harbor
openssl genrsa -out harbor.key 4096
openssl req -sha512 -new \
  -subj "/C=RU/ST=Moscow/L=Moscow/O=example/OU=My company/CN=registry.harbor.local" \
  -key harbor.key -out harbor.csr

# Создание файла v3.ext (обязательно для SAN)
cat <<EOF > v3.ext
authorityKeyIdentifier=keyid,issuer
basicConstraints=CA:FALSE
keyUsage = digitalSignature, nonRepudiation, keyEncipherment, dataEncipherment
extendedKeyUsage = serverAuth
subjectAltName = @alt_names

[alt_names]
DNS.1 = registry.harbor.local
DNS.2 = harbor.local
IP.1 = 192.168.56.11
EOF

# Подпись сертификата
openssl x509 -req -sha512 -days 3650 -extfile v3.ext \
  -CA ca.crt -CAkey ca.key -CAcreateserial \
  -in harbor.csr -out harbor.crt

# Копируем публичный CA в общую папку (он нужен для клиентов)
cp /home/vagrant/certs/ca.crt /vagrant/
```
3. Установка Harbor
```bash
cd /opt
sudo wget https://github.com/goharbor/harbor/releases/download/v2.14.2/harbor-offline-installer-v2.14.2.tgz
sudo tar xzf harbor-offline-installer-v2.14.2.tgz
cd harbor

# Настройка конфига
sudo cp harbor.yml.tmpl harbor.yml
sudo nano harbor.yml
# Внести изменения: hostname (registry.harbor.local), пути к сертификатам (/home/vagrant/certs/...), пароль админа.

# Запуск
sudo ./install.sh --with-trivy
