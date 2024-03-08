[На главную](/){ .md-button }
##

# Deploy Django project to vps server 

!!! note "пояснения"
    === "Шаги"
        1. Здесь рассматривается запуск проекта на vps сервере разработанного на django со стандартной базой данных sqlite3.
        2. Покупаем сервер и приступаем к его настройке
        3. Поработаем с ssh
        4. Настроим пользователя root
        5. Добавим нового пользователя. Переключимся на него и уже все настройки будем производить через него
        6. Установка и настройка Nginx
        7. Клонируем проект
        8. Установим gunicorn
        9. Установим пакеты для работы с django
    === "ssl"
        1. Научимся подключать к нашему домену ssl сертификат
    === "Полезные видео"
        1. [Настройка сервера для Django 🔥 Git, Nginx, Gunicorn, PostgreSQL, домен, SSL-сертификат](https://www.youtube.com/watch?v=Qvtr7es5dpc)
        2. [Деплой Frontend приложения. Настройка nginx. Подключаем домен, настраиваем HTTPS, gzip, docker](https://www.youtube.com/watch?v=8OHe6chCWTE)
        3. [Настройка веб-сервера Nginx](https://sch9.ru/docs/nginx.html)









---
## #Ресурсные записи -> домен -> сервер
после покупки сервера мы связываем доменное имя с нашим сервером, данные по работе с сервером приходят на указанную вами почту или их можно взять в личном кабинете сервиса в котором покупался сервер
![ip](assets/img/mainip.png)

* `настроить ресурсные записи в regru` - Позовлит соединить доменное имя с купленным сервером

заходим в регру, выбираем доменное имя, изменить ресурсные записи, добавляем ip нашего сервера: 

* `в запись @` 
* `и в www`
![server](assets/img/resursdata.png)



---
## ssh

### Сгенерировать ключ
```
ssh-keygen -t rsa
```

### Вывести ключ в терминал
```
cat ~/.ssh/id_rsa.pub
```

### Скопировать ключ на удаленный сервер
После ввода команды, введите пароль (не будет отображаться) и нажмите ENTER. Утилита скопирует содержимое открытого ключа (~/.ssh/id_rsa.pub) на удаленный сервер в файл authorized_keys.
```
ssh-copy-id root@00.000.000.000
```

### Если соединение быстро обрывается можно использовать эту команду
```
ssh -o ServerAliveInterval=60 root@00.000.000.000
```

### Зайти на сервер по ssh
```
ssh root@00.000.000.000
```



---

## Обновите список пакетов
    sudo apt update

---


## Учетные записи

### меняем пароль пользователя ROOT
при вво­де сим­волы не отоб­ража­ются — нет ни букв, ни цифр, ни звез­дочек, это нор­маль­но, вве­ди новый пароль и наж­ми Enter
```
passwd
```

---

### добавить учетную запись
следуйте инструкциям, чтобы установить пароль и заполнить другую информацию
```
adduser coder
```

### добавить права суперпользователя для пользователя coder
добавьте пользователя coder в группу sudo, используя команду
```
usermod -aG sudo coder
```

### проверьте, что пользователь добавлен в группу sudo, выполните команду
```
groups coder
```



---
### #Установка программ

### Установите git
* `git --version` - проверить установлен ли git
```
git --version
```

```
sudo apt install git
```

### Установите Nginx
    sudo apt install nginx

### Полезные команды Nginx
```
sudo nginx -t
```

```
sudo systemctl start nginx
```

```
sudo systemctl restart nginx
```

```
sudo systemctl stop nginx
```

```
sudo service nginx reload
```

```
sudo service nginx status
```

---
## Склонировать проект 
перейти в папку www
```
cd /var/www/
```
склонировать сюда проект

```
git clone gitLink
```

### проблема с клонированием / Permission denied
* `Ошибка "Permission denied" при клонировании репозитория Git может возникнуть из-за недостаточных прав доступа к директории, в которую вы пытаетесь склонировать проект. В данном случае, вам не хватает прав на запись в директорию, где вы пытаетесь выполнить клонирование.`
* `Для решения этой проблемы вам следует убедиться, что у вас есть достаточные права доступа к директории /var/www/ или создать новую директорию, куда вы сможете клонировать проект. Вы можете выполнить следующие шаги:`

Убедитесь, что у вас есть права на запись в директорию /var/www/. Для этого выполните команду:
```
ls -ld /var/www/

```

Если у вас нет прав на запись в эту директорию, выполните команду для изменения прав доступа:
```
sudo chmod o+w /var/www/
```

----

## Настройка nginx.	Добавить файл в nginx каталог 
(myproject_django - придумайте свой вариант)
```
sudo nano /etc/nginx/sites-available/myproject_django
```

### добавляем в файл /etc/nginx/sites-available/myproject_django эти данные (ставим свой ip в строку server_name)
``` linenums="1"
server {
	
	server_name 00.000.000.000;
	
	location /static/ {
		root '/var/www/myproject_django/';
	}
	
	location /media/ {
		root '/var/www/myproject_django/';
	}
	
	location / {
		include proxy_params;
		proxy_pass http://unix:/run/gunicorn.sock;
	}
}
```

### Создаем симлинк
```
sudo ln -s /etc/nginx/sites-available/myproject_django /etc/nginx/sites-enabled
```

### проверим конфигурацию nginx
```
sudo nginx -t
```

---


## #Django
### Ставим нужные пакеты
* `sudo apt install gcc (python-dev) python3-pip python3-dev curl -y`

```
sudo apt install gcc 
```
```
??? sudo apt install python-dev-is-python3
```
```
sudo apt install python3
```
```
sudo apt install python3-pip
```
```
sudo apt install python3-dev
```
```
sudo apt install curl
```

Проверьте успешность установки с помощью 
```
python3 --version
```


### Установка виртуальной среды Python
```
sudo -H pip3 install --upgrade pip
```
```
pip install --upgrade pip
```
```
sudo -H pip3 install virtualenv
```

### Создайте виртуальную среду в этой директории
```
virtualenv venv
```
```
python3 -m venv venv
```


### Активируйте виртуальную среду
```
source venv/bin/activate
```

## Установка модулей или пакетов вашего django приложения
```
pip install -r requirements.txt
```

## Запуск сервера 
(временная мера - просто для проверки). Ниже настроим gunicorn
```
python manage.py runserver 0.0.0.0:8000
```

## Cобираем статические файлы
```
python manage.py collectstatic
```

---

## #Letsencrypt

Подключение сертификата
```
sudo apt install certbot python3-certbot-nginx
```
```
sudo certbot --nginx
```



* `https://letsencrypt.org/ru/`
* `https://certbot.eff.org/`

* `выбираем nginx и ubuntu20`

* `Install snapd`
* `https://snapcraft.io/docs/installing-snapd`

```
snap --version
```
```
sudo apt-get remove certbot
```
```
sudo apt install snapd
```




* `Install Certbot`
```
sudo snap install --classic certbot
```
```
sudo ln -s /snap/bin/certbot /usr/bin/certbot
```
```
sudo certbot --nginx
```

* ` - вводим емейл`
* `- y (соглашаемся)`
* `- вводим наш домен (example.com) - пишите свой домен`


---



## Настройка Gunicorn
```
sudo service gunicorn restart
```

* `в виртуальном окружении выполнить команду`
```
pip install gunicorn
```


Убедитесь, что вы создали соответствующий systemd юнитный файл для gunicorn. Обычно юнитный файл для gunicorn должен быть создан в директории 
```
/etc/systemd/system/
```

создать файл сокета gunicorn
```
sudo nano /etc/systemd/system/gunicorn.socket
```

Наполнить этими данными

```
[Unit]
Description=gunicorn socket

[Socket]
ListenStream=/run/gunicorn.sock

[Install]
WantedBy=sockets.target
```

создать файл для службы
```
sudo nano /etc/systemd/system/gunicorn.service
```

рабочий вариант (myproject_django - замените на свою папку проекта)

```
[Unit]
Description=gunicorn daemon
Requires=gunicorn.socket
After=network.target

[Service]
User=root
Group=root
WorkingDirectory=/var/www/myproject_django/
ExecStart=/var/www/myproject_django/venv/bin/gunicorn \
    --access-logfile - \
    --workers 3 \
    --bind unix:/run/gunicorn.sock \
    base.wsgi:application

[Install]
WantedBy=multi-user.target
```

вводим эти команды
```
sudo systemctl daemon-reload
```
```
sudo systemctl restart gunicorn
```



### Активировать сокет
```
sudo systemctl start gunicorn.socket
```
```
sudo systemctl enable gunicorn.socket
```

другие полезные команды
```
sudo service gunicorn start
```
```
sudo service gunicorn status
```
```
sudo systemctl daemon-reload
```
```
sudo systemctl restart gunicorn
```

```
sudo systemctl daemon-reload
```

```
sudo systemctl status gunicorn
```
```
sudo systemctl restart nginx
```







---



## Commands

* `python manage.py clear_cache` - Чтобы очистить кеш в Django, вы можете использовать команду управления clear_cache. Для этого выполните следующую команду в вашем терминале:
```
python manage.py clear_cache
```

```
sudo systemctl restart nginx
```
```
sudo systemctl restart gunicorn
```
```
python manage.py collectstatic
```
