# Docker

Установка докера и docker-compose без проблем осущсествляется по официальной документации для конкретной ОС.  
Тут я хочу отразить всякие мелочи которые необходимы для работы.

### Запуск контейнера с Clickhouse
Создадим папку на локеле: куда будут сохранятся все данные  
`mkdir $HOME/clickhouse_database`  
Далее, запускаем контейнер в фоновом режиме  
`docker run -d --name clickhouse -p 18123:8123 -p19000:9000 --ulimit nofile=262144:262144 --volume=$HOME/clickhouse_database:/var/lib/clickhouse yandex/clickhouse-server`  
Дальше через localhost:18123/play можем поиграться с базой  
Начать можно с `select * from system.tables`
Как наигрались - выключаем  
`docker stop clickhouse` Можно гасить по CONTAINER_ID: `docker ps -a`  
