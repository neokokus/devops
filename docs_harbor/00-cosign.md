### 1.1. Установка Cosign (на ВМ docker)

```bash
wget https://github.com/sigstore/cosign/releases/latest/download/cosign-linux-amd64
sudo mv cosign-linux-amd64 /usr/local/bin/cosign
sudo chmod +x /usr/local/bin/cosign
cosign version
bash```
1.2. Генерация ключей
```bash
cosign generate-key-pair
bash```
(Придумайте и запомните пароль для приватного ключа. В текущей папке появятся cosign.key и cosign.pub).

1.3. Подпись образа
Используем digest образа (а не тег), чтобы гарантировать подпись конкретного содержимого:

```bash
cosign sign --key cosign.key --new-bundle-format=false --use-signing-config=false registry.harbor.local/test/alpine@sha256:c26612752c78b18faacd63c7d50f4592c2e7b0f25e0e20e82bd74e465c57ab0d```bash
(Вставьте свой digest, полученный командой sudo docker images --digests | grep alpine).

Примечание: Флаги ```bash--new-bundle-format=false```bash и ```bash--use-signing-config=false```bash нужны из-за бага совместимости Cosign 3.1.x и Harbor 2.14. Без них подпись создается, но не отображается в UI.

1.4. Настройка Harbor
Зайдите в проект test -> Configuration.

В разделе Deployment security включите галочку Cosign.
![Prevent vulnerable](Harbor_func/prevent.png)
Нажмите Save.

После этого Harbor разрешает скачивать только подписанные образы.

1.5. Проверка
Попробуйте скачать неподписанный образ (например, ubuntu):
```bash
sudo docker pull registry.harbor.local/test/ubuntu:v1.0 

bash```
Ожидаемый результат: Ошибка signature not found или denied. Это доказывает, что Content Trust работает.
![unsigned](Harbor_func/no_signed.png)