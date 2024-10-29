# Docker

#### Установка DOCKER

```sh
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
```

#### Info and Registry

| Команда        | Описание                                   |
|:---------------|:-------------------------------------------|
|`docker info`   | Информация обо всём в установленном Docker |
|`docker history`| История образа                             |
|`docker tag`    | Дать тег образу локально или в registry    |
|`docker login`  | Залогиниться в registry                    |
|`docker search` | Поиск образа в registry                    |
|`docker pull`   | Загрузить образ из Registry себе на хост   |
|`docker push`   | Отправить локальный образ в registry       |


#### Container Management

| Команда                                | Описание                                         |
|:---------------------------------------|:-------------------------------------------------|
|`docker ps -а`                          | Посмотреть все контейнеры                        |
|`docker start container-name`           | Запустить контейнер                              |
|`docker kill/stop container-name`       | Убить (SIGKILL) /Остановить (SIGTERM) контейнер  |
|`docker logs --tail 100 container-name` | Вывести логи контейнера, последние 100 строк     |
|`docker inspect container-name`         | Вся инфа о контейнере + IP                       |
|`docker rm container-name`              | Удалить контейнер                                |
|`docker rm -f $(docker ps -aq)`         | Удалить все запущенные и остановленные контейнеры|
|`docker events container-name`          | Отслеживать в реальном времени события           |
|`docker port container-name`            | Показать публичный порт контейнера               |
|`docker top container-name`             | Отобразить процессы в контейнере                 |
|`docker stats container-name`           | Статистика использования ресурсов в контейнере   |
|`docker diff container-name`            | Изменения в ФС контейнера                        |


#### Images

| Команда                                                        | Описание                                         |
|:---------------------------------------------------------------|:------------------------------------------------------------------------------------------------------------------------------------------|
|`docker build -t my_app .`                                      | Билд контейнера в текущей папке, Скачивает все слои для запуска образа                                                                    |
|`docker images / docker image ls`                               | Показать все образы в системе                                                                                                             |
|`docker image rm / docker rmi image`                            | Удалить image                                                                                                                             |
|`docker commit  <containerName/ID> alsnix/debian11slim:version3`| Создает образ из контейнера                                                                                                               |
|`docker insert URL`                                             | Вставляет файл из URL в контейнер                                                                                                         |
|`docker save -o backup.tar`                                     | Сохранить образ в backup.tar в STDOUT с тегами, версиями, слоями                                                                          |
|`docker load`                                                   | Загрузить образ в .tar в STDIN с тегами, версиями, слоями                                                                                 |
|`docker import`                                                 | Создать образ из .tar                                                                                                                     |
|`docker image history --no-trunc`                               | Посмотреть историю слоёв образа                                                                                                           |
|`docker system prune -f`                                        | Удалит все, кроме используемого (лучше не использовать на проде, ещё кстати из-за старого кеша может собираться cтарая версия контейнера) |


#### Run

| Команда                                                                                                                                                         | Описание                                                                                                  |
|:----------------------------------------------------------------------------------------------------------------------------------------------------------------|:----------------------------------------------------------------------------------------------------------|
|`docker run -d -p 80:80 -p 22:22 debian:11.1-slim sleep infinity (--rm удалит после закрытия контейнера, --restart unless-stopped добавит автозапуск контейнера)`| Запуск контейнера интерактивно или как демона/detached (-d), Порты: слева хостовая система, справа в контейнере, пробрасывается сразу 2 порта 80 и 22, используется легкий образ Debian 11 и команда бесконечный сон|
|`docker update --restart unless-stopped redis`                                                                                                                   | Добавит к контейнеру правило перезапускаться при закрытии, за исключением команды стоп, автозапуск по-сути|
|`docker exec -it container-name /bin/bash (ash для alpine)`                                                                                                      | Интерактивно подключиться к контейнеру для управления, exit чтобы выйти                                   |
|`docker exec -u root -it container-name /bin/bash (ash для alpine)`                                                                                              | Интерактивно подключиться к контейнеру для управления из под root, exit чтобы выйти                       |
|`docker attach container-name`                                                                                                                                   | Подключиться к контейнеру чтоб мониторить ошибки логи в реалтайме                                         |


#### Volumes

| Команда                                                                                     | Описание                                                                   |
|:--------------------------------------------------------------------------------------------|:---------------------------------------------------------------------------|
|`docker cp file <containerID>:/`                                                             | Скопировать в корень контейнера file                                       |
|`docker cp <containerID>:/file .`                                                            | Скопировать file из корня контейнера в текущую директорию командной строки |
|`docker volume create todo-db`                                                               | Создать volume для постоянного хранения файлов                             |
|`docker run -dp 3000:3000 --name=dev -v todo-db:/etc/todos container-name`                   | Создать volume для постоянного хранения файлов                             |
|`docker run -dp 3000:3000 --name=dev --mount source=todo-db,target=/etc/todos container-name`| Создать volume для постоянного хранения файлов                             |
|`docker volume ls`                                                                           | Отобразить список всех volume’ов                                           |
|`docker volume inspect`                                                                      | Инспекция volume’ов                                                        |
|`docker volume rm`                                                                           | Удалить volume                                                             |


#### Network

| Команда                         | Описание              |
|:--------------------------------|:----------------------|
|`docker network create todo-app` | Создать сеть          |
|`docker network rm`              | Удалить сеть          |
|`docker network ls`              | Показать все сети     |
|`docker network inspect`         | Вся информация о сети |
|`docker network connect`         | Соединиться с сетью   |
|`docker network disconnect`      | Отсоединиться от сети |


Пробросить текущую папку в контейнер и работать на хосте, -w working dir, sh shell

```sh
docker run -dp 3000:3000 \
-w /app -v "$(pwd):/app" \
node:12-alpine \
sh -c "yarn install && yarn run dev"
```


Запуск контейнера с присоединением к сети и заведение переменных окружения

```sh
docker run -d \
--network todo-app --network-alias mysql \ (алиас потом сможет резолвить докер для других контейнеров)
-v todo-mysql-data:/var/lib/mysql \ (автоматом создает named volume)
-e MYSQL_ROOT_PASSWORD=secret \ (в проде нельзя использовать, небезопасно)
-e MYSQL_DATABASE=todos \ (в проде юзают файлы внутри конейнера с логинами паролями)
mysql:5.7
```


Запуск контейнера с приложением
```sh
docker run -dp 3000:3000 \
-w /app -v "$(pwd):/app" \
--network todo-app \
-e MYSQL_HOST=mysql \
-e MYSQL_USER=root \
-e MYSQL_PASSWORD=secret \
-e MYSQL_DB=todos \
node:12-alpine \
sh -c "yarn install && yarn run dev"
```