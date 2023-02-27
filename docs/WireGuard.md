# WireGuard

## Вырубаем дэдик

С начала бежим на хост и покупаем VPS/VDS сервер  
Я, для примера использую, `https://www.robovps.biz/` он же `rustelecom`  
Беру что нибудь вне ру зоны (Франкфурт-на-Майне, Германия) на базе Ubuntu-20.04-amd64  
Оплачиваем (250р за самую простую конфигурацию) и получаем учетные данные:  

- Тарифный план: Pluto 2.0  
- Дата открытия: 2023-02-26  
- Доменное имя: wg.vpn  
- IP-адрес сервера: SERVER_IP  
- Пользователь: root  
- Пароль: PASS  


## Настройка сервера

Далее лезем на сервер и начинаем настройку: `ssh root@<SERVER_IP>`  
Обновим сервер Ububtu: `apt update && apt upgrade -y`  
`apt install -y wireguard`
`cd /etc/wireguard/`
Генерим приватные ключи `wg genkey | tee /etc/wireguard/privatkey | wg pubkey | tee /etc/wireguard/publickey`  
Отрубаем у всех доступ к ключу кроме себя `chmod 600 privatkey`  
Создаем файл конфигурации WG сервера: `nano /etc/wireguard/wg0.conf` для извращенцев всегда есть `vim`  
В файл вставляем вот такой текст:  
```
[Interface]
PrivateKey = <Сюда вставляем содержимое файла /etc/wireguard/privatkey>
Address = 10.0.0.1/24 <- Это IP нашего сервера внутри виртуальной сети
ListenPort = 51830
PostUp = iptables -A FORWARD -i wg0 -j ACCEPT; iptables -t nat -A POSTROUTING -o ens3 -j MASQUERADE
PostDown = iptables -D FORWARD -i wg0 -j ACCEPT; iptables -t nat -D POSTROUTING -o ens3 -j MASQUERADE
```
Примечание:  
1) Если не корректно задать интерфейс в командах PostDown/PostUp то соединение будет устанавливаться успешно, 
но трафик ходить не будет. 
Чтобы этого избежать, предварительно проверьте название сетевого интерфейса с помощью `ifconfog -a` (предварительно поставьте net-wools `apt install net-tools`, в пустой Ubuntu его обычно нет).
2) в инструкции написано что интерфейс WG можно задать как `%i`, но лучше захардкодить его значение `wg0`. 

Чтобы перенаправлять трафик через WG сервер в интернет - нужно включить ip forwarding  
`echo "net.ipv4.ip_forward=1" >> /etc/sysctl.conf`  
или просто зайдите в файл `/etc/sysctl.conf` и раскоментируйте строчу `net.ipv4.ip_forward=1"`  
Настраиваем systemctl для работы с WG  
`systemctl enable wg-quick@wg0.service`  
`systemctl start wg-quick@wg0.service`  
`systemctl status wg-quick@wg0.service`  
Если все в порядке с конифгом то после последней команды увидим статус `active`  

УСТАНОВЛЕНО! СКОНФИГУРИРОВАНО! ЗАПУСЩЕНО!


## Настройка клиента 

Начнем с генерации ключей на сервере для клиента:  
`wg genkey | tee /etc/wireguard/<username>_privatkey | wg pubkey | tee /etc/wireguard/<username>_publickey`  
Далее дописываем в конфиг `/etc/wireguard/wg0.conf` пользователя:  
```
[Peer]
PublicKey = <Сюда вставляем содержимое файла /etc/wireguard/<username>_publictkey>
AllowedIPs = 10.0.0.2/32 <- Это IP нашего клиента внутри виртуальной сети
```
После сохранения конфига не забываем рестартануть systemctl  
`systemctl restart wg-quick@wg0.service`


## Настройка подключения на клиентской машине
С начала формируем конфиг.  
```
[Interface]
PrivateKey = <USER_PK>
Address = 10.0.0.2/32
DNS = 8.8.8.8

[Peer]
PublicKey = <SERVER_PUB_KEY>
Endpoint = <SERVER_IP>:51830
AllowedIPs = 0.0.0.0/0  <- Это значить что пустить весь трафик через WG 
PersistentKeepalive = 20
```
На маке и на винде можно его хранить где угодно.  
На линухе удобно его положить в etc/wireguard  
В таком случае при запуске не нужно будет указываеть точный пусть до конфига.  
`wg-quick up wg`, где `wg` = `/etc/wireguard/wg.conf`  


## Вкл-выкл 
На маке и на винде есть клиенты с GUI где достаточно добавить конфиг и использовать кнопки вкл-выкл  
На linux нужно поставить `wireguard-tools`, а далее все просто.  
`wg-quick up wg`, где `wg` -> клиентский конфиг файл.  
