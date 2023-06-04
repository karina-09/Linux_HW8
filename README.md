Задание:
Установить в виртуальную машину или VDS Docker, настроить набор контейнеров через docker compose по инструкции по ссылке: https://www.digitalocean.com/community/tutorials/how-to-install-wordpress-with-docker-compose-ru. Часть с настройкой certbot и HTTPS опустить, если у вас нет настоящего домена и белого IP.
* Запустить два контейнера, связанные одной сетью (используя документацию). Первый контейнер БД (например, образ mariadb:10.8), второй контейнер — phpmyadmin. Получить доступ к БД в первом контейнере через второй контейнер (веб-интерфейс phpmyadmin).
* Собрать собственный образ для hub.docker.com. В качестве зачета по нему предоставить ссылку на образ.

Результат:
Текст команд, которые применялись при выполнении задания. При наличии: часть конфигурационных файлов, которые решают задачу. Скриншоты результата запуска контейнеров (веб-интерфейс). Присылаем в формате текстового документа: задание и команды для решения (без вывода). Формат — PDF (один файл на все задания).

version: '3.5'

services:
  db:
    image: mysql:8.0
    container_name: db
    restart: unless-stopped
    env_file: .env
    environment:
      - MYSQL_DATABASE=wordpress
    volumes:
      - dbdata:/var/lib/mysql
    command: '--default-authentication-plugin=mysql_native_password'
    networks:
      - app-network

  wordpress:
    depends_on:
      - db
    image: wordpress:6.0-fpm-alpine
    container_name: wordpress
    restart: unless-stopped
    env_file: .env
    environment:
      - WORDPRESS_DB_HOST=db:3306
      - WORDPRESS_DB_USER=$MYSQL_USER
      - WORDPRESS_DB_PASSWORD=$MYSQL_PASSWORD
      - WORDPRESS_DB_NAME=wordpress
    volumes:
      - wordpress:/var/www/html
    networks:
      - app-network
  
  webserver:
    depends_on:
      - wordpress
    image: nginx:1.23-alpine
    container_name: webserver
    restart: unless-stopped
    ports:
      - "80:80"
    volumes:
      - wordpress:/var/www/html
      - ./nginx-conf:/etc/nginx/conf.d
    networks:
      - app-network

volumes:
  wordpress:
  dbdata:
  
networks:
  app-network:

![alt text](https://github.com/karina-09/linux_HW8/blob/main/Screenshot_1.png)



![alt text](https://github.com/karina-09/linux_HW8/blob/main/Screenshot_2.png)
