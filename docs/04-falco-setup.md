# Установка и настройка Falco (Runtime Security)

**Falco** — это инструмент для мониторинга безопасности во время выполнения (runtime security). Он анализирует системные вызовы (syscalls) и генерирует алерты при обнаружении подозрительной активности (например, запуск shell внутри контейнера или чтение чувствительных файлов).

## 1. Где установлен
Falco установлен на обеих виртуальных машинах:
- `docker.local` (клиент)
- `harbor.local` (сервер с реестром)

## 2. Проблемы при установке и их решение
При попытке установки через `apt` возникла ошибка `Connection timed out` при скачивании пакета с `download.falco.org` (AWS CloudFront). 
**Решение:** Принудительно прописать публичные DNS-серверы перед установкой.

```bash
echo "nameserver 8.8.8.8" | sudo tee /etc/resolv.conf > /dev/null
echo "nameserver 1.1.1.1" | sudo tee -a /etc/resolv.conf > /dev/null
	```
Также на машине harbor возникла ошибка NO_PUBKEY (ключ репозитория не установился). Решение — скачать ключ вручную и добавить его в связку:

```bash
curl -fsSL https://falco.org/repo/falcosecurity-packages.asc -o /tmp/falco-key.asc
sudo gpg --dearmor -o /usr/share/keyrings/falco-archive-keyring.gpg < /tmp/falco-key.asc
	```

3. Команды установки (Ubuntu 24.04)
```bash
# 1. Фикс DNS
echo "nameserver 8.8.8.8" | sudo tee /etc/resolv.conf > /dev/null
echo "nameserver 1.1.1.1" | sudo tee -a /etc/resolv.conf > /dev/null
	```
# 2. Добавление репозитория
```bash
curl -fsSL https://falco.org/repo/falcosecurity-packages.asc | \
sudo gpg --dearmor -o /usr/share/keyrings/falco-archive-keyring.gpg
	
sudo bash -c 'cat << EOF > /etc/apt/sources.list.d/falcosecurity.list
deb [signed-by=/usr/share/keyrings/falco-archive-keyring.gpg] https://download.falco.org/packages/deb stable main
EOF'
	```

# 3. Установка
```bash
sudo apt-get update -y
sudo apt-get install -y dialog
sudo apt-get install -y falco
	```
Важно: В процессе установки выбираем драйвер Modern eBPF (стрелками и Enter).