# TryHackMe-ColddBox

## Rustscan

<img src="https://imgur.com/VW0IztZ.png"/>

<img src="https://imgur.com/Yn5ZiC8.png"/>


Открыто 10.10.218.218:80                                                             
Открыто 10.10.218.218:4512 

## Порт 80

<img src="https://imgur.com/vAiwYMa.png"/>

Это выглядит как сайт на wordpress, чтобы убедиться в этом, давайте посетим его `/wp-admin`

<img src="https://imgur.com/3PitIQV.png"/>

## Dirsearch

Я начал искать каталоги с помощью dirsearch и ожидал найти wp-admin, так как это сайт на wordpress

<img src="https://imgur.com/RzKcrg0.png"/>

<img src="https://imgur.com/8QbEzYa.png"/>

Это дало нам действительное имя пользователя, и мы можем проверить его, поскольку wordpress позволяет нам узнать, правильно ли введено имя пользователя, но неверен ли пароль

<img src="https://imgur.com/hNbxZX0.png"/>

Мы можем взломать пароль для этой учетной записи пользователя.

## WPSCAN

Для wordpress рекомендуется запустить wpscan для поиска установленных имен пользователей, плагинов и тем, а также для поиска уязвимых плагинов

<img src="https://imgur.com/4O6rJkA.png"/>

Вместе с hugo мы нашли еще несколько пользователей, так что давайте начнем атаку грубой силой через wpscan

<img src="https://imgur.com/0XPVlo5.png"/>

<img src="https://imgur.com/lcSQipa.png"/>

<img src="https://imgur.com/9iqHQDF.png"/>

Мы вошли в панель управления wordpress, теперь переходим к `Appearance` -> `Editor` -> `Select 404 Template`-> `Paste php reverse shell` 

<img src="https://imgur.com/QDzdwA2.png"/>

<img src="https://imgur.com/oEzHJ5d.png"/>

Теперь нам нужно вызвать обратную оболочку php, поскольку для настройки прослушивателя netcat мы добавили наш вредоносный файл 404.php теперь нам нужно перейти к тому месту, где он хранится, поскольку мы отредактировали тему twentyfiteen, в которой он находится `wp-content/themes/twentyfifteen/404.php`

<img src="https://imgur.com/Rd5CJtv.png"/>

Но нам нужно расширить наши привилегии, чтобы читать user.txt `user.txt`	

<img src="https://imgur.com/uQffE6i.png"/>

<img src="https://imgur.com/kQEuDQW.png"/>

Мы видим find как SUID, поэтому злоупотребляем им, чтобы получить доступ к root

<img src="https://imgur.com/VVUh9o5.png"/>

Мы видим, что наше приглашение изменилось от имени пользователя root
