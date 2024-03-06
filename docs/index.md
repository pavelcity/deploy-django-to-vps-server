[На главную](/){ .md-button }
##

# Deploy Django project to vps server 

## Настройка nginx / gunicorn / ssl



---
## #Полезные видео
* [Настройка сервера для Django 🔥 Git, Nginx, Gunicorn, PostgreSQL, домен, SSL-сертификат](https://www.youtube.com/watch?v=Qvtr7es5dpc)

* [Деплой Frontend приложения. Настройка nginx. Подключаем домен, настраиваем HTTPS, gzip, docker](https://www.youtube.com/watch?v=8OHe6chCWTE)

* [Настройка веб-сервера Nginx](https://sch9.ru/docs/nginx.html)

---
## Ресурсные записи
* `настроить ресурсные записи в regru` - Позовлит соединить доменное имя с купленным сервером

---
## Зайти по ssh на купленный сервер
```
ssh root@000.0.000.00
```

## Обновите список пакетов
    sudo apt update

## Установите git
* `git --version` - проврить установлен ли git
```
git --version
```

```
sudo apt install git
```

## Установите Nginx
    sudo apt install nginx

#### Полезные команды Nginx
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


## Настройка nginx.	Добавить файл в nginx каталог 
### (myproject_django - придумайте свой вариант)
```
sudo nano /etc/nginx/sites-available/myproject_django
```

## Создаем симлинк
```
sudo ln -s /etc/nginx/sites-available/myproject_django /etc/nginx/sites-enabled
```


## Ставим нужные пакеты
```
sudo apt install gcc python-dev python3-pip python3-dev curl -y
```
```
sudo apt install python-dev-is-python3
```


## Установка виртуальной среды Python
```
sudo -H pip3 install --upgrade pip
```
```
pip install --upgrade pip
```
```
sudo -H pip3 install virtualenv
```

## Создайте виртуальную среду в этой директории
```
virtualenv venv
```
```
python3 -m venv venv
```


## Активируйте виртуальную среду
```
source venv/bin/activate
```

## Установка модулей или пакетов вашего django приложения
```
pip install -r requirements.txt
```

## Запуск сервера (временная мера - просто для проверки). Ниже настроим gunicorn
```
python manage.py runserver 0.0.0.0:8000
```

## Cобираем статические файлы
```
python manage.py collectstatic
```

---

## #Letsencrypt

### Подключение сертификата
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


## Commands

* `mkdocs new [dir-name]` - Create a new project.
* `mkdocs serve` - Start the live-reloading docs server.
* `mkdocs build` - Build the documentation site.
* `mkdocs -h` - Print help message and exit.

```
sudo systemctl restart gunicorn
```

