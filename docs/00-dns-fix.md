# Решение проблемы с DNS (Temporary failure resolving)

## Проблема
При выполнении `sudo apt update` или установке пакетов возникает ошибка:
`Temporary failure resolving 'us.archive.ubuntu.com'`

Это происходит из-за того, что виртуальная машина Vagrant не может получить рабочий DNS-сервер от VirtualBox.

## Решение
Необходимо вручную прописать публичные DNS-серверы (Google и Cloudflare) в файл `/etc/resolv.conf`.

Выполните на проблемной ВМ:

echo "nameserver 8.8.8.8" | sudo tee /etc/resolv.conf > /dev/null
echo "nameserver 1.1.1.1" | sudo tee -a /etc/resolv.conf > /dev/null
Проверьте, что резолвинг работает:

ping -c 2 us.archive.ubuntu.com
После этого apt update и установка пакетов должны проходить без ошибок.