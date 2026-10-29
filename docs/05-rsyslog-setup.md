# Настройка централизованного логирования (Rsyslog)

В рамках стенда настроен централизованный сбор логов: машина `docker.local` выступает в роли клиента и отправляет все свои системные логи (включая алерты Falco) на машину `harbor.local`, которая выступает в роли сервера и складывает их в отдельные файлы.

## 1. Настройка сервера (ВМ harbor)

На машине `harbor` устанавливаем Rsyslog (обычно уже установлен) и настраиваем его на прием логов по TCP.

```bash
sudo apt update
sudo apt install -y rsyslog
Создаем конфигурационный файл:
bash```

```bash
sudo nano /etc/rsyslog.d/10-server.conf
bash```
Вставляем в него следующие правила (прием по TCP порту 514 и сохранение в папку /var/log/remote/ с разбивкой по имени хоста и программе):
![10-server.conf](Documents/conf(server).png)
```text
module(load="imtcp")
input(type="imtcp" port="514")
template(name="RemoteLogs" type="string" string="/var/log/remote/%HOSTNAME%/%PROGRAMNAME%.log")
*.* action(type="omfile" dynaFile="RemoteLogs")
```
Перезапускаем службу и проверяем, что порт открыт:

```bash
sudo systemctl restart rsyslog
sudo ss -tulnp | grep 514
bash```
2. Настройка клиента (ВМ docker)
На машине docker настраиваем отправку всех логов на сервер harbor (IP 192.168.56.11). Символ @@ означает использование надежного TCP-протокола.

```bash
sudo apt update
sudo apt install -y rsyslog
bash```
Создаем конфигурационный файл:

```bash
sudo nano /etc/rsyslog.d/50-forward.conf
bash```
Вставляем строку:

```text
*.* @@192.168.56.11:514
```
![50-forward.conf](Documents/conf(client).png)
Перезапускаем Rsyslog:

```bash
sudo systemctl restart rsyslog
bash```
3. Проверка работы
На машине docker отправляем тестовое сообщение:
```bash
logger -t test-client "Hello from Docker"
bash```
На машине harbor проверяем, что файл с логом создался и в нем есть наше сообщение:

```bash
ls -R /var/log/remote/
cat /var/log/remote/docker/test-client.log
bash```
![cat](Documents/rsyslog(read).png)
Ожидаемый результат: В папке /var/log/remote/ появляется директория с именем хоста клиента, внутри которой лежат лог-файлы. Алерты Falco (которые он пишет в syslog) также будут автоматически попадать в этот централизованный сбор.