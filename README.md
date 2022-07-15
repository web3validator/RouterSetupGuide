# RouterSetupGuide

## Connext Router Setup

https://user-images.githubusercontent.com/59205554/169454442-6d328a2c-eda9-4355-92ae-6119ef5dfa6f.mp4


[![LOGO](https://images.squarespace-cdn.com/content/v1/619f86b8de2c6f4f7fa201c0/8eaeca35-ccf3-495f-9e9a-19fbec796187/connext__Logo+%2B+WhiteText+MultiColor.png)](https://www.connext.network/)

Настройка узла маршрутизатора Connext для программы Connext Contributor

## Минимальные требования к оборудованию:
* 8GB ОЗУ
* 30GB Хранилище
* Redis

## Способ для быстрой установки
Утуб посилання для встановлення через bash скрипт https://www.youtube.com/watch?v=MLVI7PYNDPM&ab_channel=VasyaBTC:
```
wget  https://raw.githubusercontent.com/cybernekit/RouterSetupGuide/main/router_WEB3.sh && chmod +x router_WEB3.sh && sudo /bin/bash router_WEB3.sh
```




## Заходим в root пользователя
```
sudo su

```

## Cначала обновляем пакеты
```
sudo apt update && sudo apt upgrade -y

```
![Image text](https://github.com/cybernekit/RouterSetupGuide/blob/main/img/Screenshot%20from%202022-05-17%2016-49-11.png)
## Устанавливаем docker
(не обязательно если установленный docker)
```
sudo apt-get install ca-certificates curl gnupg lsb-release -y
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io -y

```
## Устанавливаем docker-compose
(не обязательно если установлен docker-compose)
```
mkdir -p ~/.docker/cli-plugins/
curl -SL https://github.com/docker/compose/releases/download/v2.2.3/docker-compose-linux-x86_64 -o ~/.docker/cli-plugins/docker-compose
chmod +x ~/.docker/cli-plugins/docker-compose
sudo chown $USER /var/run/docker.sock

```
## Клонируем репозиторий connext
```
cd ~
git clone https://github.com/connext/nxtp-router-docker-compose.git
cd ~/nxtp-router-docker-compose
git checkout amarok

```
![Screenshot from 2022-05-18 13-41-41](https://user-images.githubusercontent.com/59205554/169020905-9d748639-019b-4395-932a-9a6bdef1abd0.png)

## Далее заходим в папку nxtp-router-docker-compose и переименовываем файлы
Переименовываем ```.env.example``` -> ```.env```, ```config.example.yaml``` -> ```config.yaml``` и ```key.example.yaml``` -> ```key.yaml```
```
mv .env.example .env && mv config.example.json config.json && mv key.example.yaml key.yaml

```
Проверяем ```.env```
```
cat .env

```
![Screenshot from 2022-05-18 15-03-55](https://user-images.githubusercontent.com/59205554/169034699-0ac24dfc-5b31-47fd-8343-d0024f12c282.png)


Теперь нам нужно получить ```LOGDNA_KEY``` ```DISCORD_WEBHOOK```
* Для получения ```LOGDNA_KEY``` переходим по ссылке и регистрируемся https://www.logdna.com/
![Screenshot from 2022-05-18 13-58-38](https://user-images.githubusercontent.com/59205554/169023703-fb5109ca-ddb9-491c-a710-9b63e5ecb596.png)
* Для получения ```DISCORD_WEBHOOK``` переходим по ссылке [DISCORD_WEBHOOK](https://squadguide.net/ru/%D0%BA%D0%B0%D0%BA-%D1%81%D0%BE%D0%B7%D0%B4%D0%B0%D1%82%D1%8C-discord-webhook-%D0%B4%D0%BB%D1%8F-%D0%BF%D1%80%D0%BE%D1%81%D1%82%D0%BE%D0%B9-%D0%BE%D1%82%D0%BF%D1%80%D0%B0%D0%B2%D0%BA%D0%B8-%D1%81)

Изменяем файл config.json
```
tee ~/nxtp-router-docker-compose/config.json &>/dev/null <<EOF
{
  "chains": {
    "1111": {
      "assets": [
        {
          "address": "0xB7b1d3cC52E658922b2aF00c5729001ceA98142C",
          "name": "TEST"
        }
      ],
      "providers": ["https://eth-rinkeby.alchemyapi.io/v2/Bi4KoxT0rgjHsmVvMRtdY_9rwrTsdY2h", "https://rpc.ankr.com/eth_rinkeby"]
    },
    "2221": {
      "assets": [
        {
          "address": "0xB5AabB55385bfBe31D627E2A717a7B189ddA4F8F",
          "name": "TEST"
        }
      ],
      "providers": ["https://eth-kovan.alchemyapi.io/v2/eEcEgHdAt3fkZkRmzbaI-Sv_1Gw6OxrB"]
    }
  },
  "logLevel": "debug",
  "mnemonic": "",
  "sequencerUrl": "https://sequencer.testnet.connext.ninja",
  "server": { "adminToken": "blahblahblah" },
  "environment": "production"
}
EOF

```
### Redis
Маршрутизатор по умолчанию использует внутренний экземпляр Redis в Docker. Однако, если вы предпочитаете использовать внешний экземпляр Redis, вы можете указать соответствующие поля хоста и порта в config.json. Инструкции можно найти на веб-сайте [Redis](https://redis.io/).

## Изменяем файл key.yaml
Твой частный ключ:
```
YOUR_PRIKEY=
```
Изменяем файл key.yaml
```
tee ~/nxtp-router-docker-compose/key.yaml &>/dev/null <<EOF
type: "file-raw"
keyType: "SECP256K1"
privateKey: "$YOUR_PRIKEY"
EOF

```
Проверяем key.yaml.
В строке ```privateKey:``` ты увидишь свой ключ
```
cat key.yaml

```
![Screenshot from 2022-05-18 12-58-35](https://user-images.githubusercontent.com/59205554/169013248-2a7c6bda-fc13-4528-8664-8e62af896b8f.png)


## Создаем docker-compose service
```
cd ~/nxtp-router-docker-compose
docker-compose create

```
## Запустить docker-compose
```
docker-compose up -d

```
## Проверить logs router
```
docker-compose logs router

```
## Проверить logs node 
```
docker logs --follow --tail 100 router

```
## Перезапустите docker-compose service
```
docker-compose restart

```



### Как получить к твоей безопасности 90%

