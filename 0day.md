# TryHackMe-0day

Сегодня решим задачу boot2root, под названием “0day“. Она доступна на TryHackMe для практики тестирования на проникновение. Эта лабораторная работа несложна, если у нас есть необходимые базовые знания для взлома лабораторий и мы внимательно относимся ко всем деталям, которые обнаруживаем во время разведки. Заслуга в создании этой лабораторной работы принадлежит MuirlandOracle и 0 day. Давайте начнем и узнаем, как успешно получить рут-права.

Уровень: Средний

## NMAP

```
Nmap scan report for 10.10.44.55                                                                                      
Host is up (0.41s latency).                                                                                           
Not shown: 998 closed ports                                                                                           
PORT   STATE SERVICE VERSION                                                                                          
22/tcp open  ssh     OpenSSH 6.6.1p1 Ubuntu 2ubuntu2.13 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   1024 57:20:82:3c:62:aa:8f:42:23:c0:b8:93:99:6f:49:9c (DSA)                                                       
|   2048 4c:40:db:32:64:0d:11:0c:ef:4f:b8:5b:73:9b:c7:6b (RSA)
|   256 f7:6f:78:d5:83:52:a6:4d:da:21:3c:55:47:b7:2d:6d (ECDSA)
|_  256 a5:b4:f0:84:b6:a7:8d:eb:0a:9d:3e:74:37:33:65:16 (ED25519)
80/tcp open  http    Apache httpd 2.4.7 ((Ubuntu))
|_http-server-header: Apache/2.4.7 (Ubuntu)
|_http-title: 0day
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 27.55 seconds
```

## PORT 80

<img src="https://imgur.com/EDnud0k.png"/>

## Feroxbuster

Для фаззинга каталогов я использовал `feroxbuster`

<img src="https://imgur.com/uPEPTsy.png"/>

<img src="https://imgur.com/WoP8k8m.png"/>

Каталог резервных копий содержит закрытый ключ

<img src="https://imgur.com/z4XnVU0.png">

Но у нас нет действительного имени пользователя для подключения по SSH.

## Nikto

<img src="https://imgur.com/PzXw7Dr.png"/>

В качестве альтернативы мы могли бы сделать это с помощью gobuster или wfuzz

<img src="https://imgur.com/zI7ZKQV.png"/>


С wfuzz

<img src="https://imgur.com/wt3yUft.png"/>


Это подсказало нам, что существует файл test.cgi, к которому мы можем получить доступ, и для него есть эксплойт, который называется уязвимостью shellshock для cgi-bin.

<img src="https://imgur.com/aG1YvUu.png"/>

Теперь я запустил оболочку в сеансе meterpreter через shell, а затем стабилизировал ее с помощью python. Получил флаг пользователя, теперь осталось только повысить привилегии

## Privilege Escalation

<img src="https://imgur.com/ieAYkPK.png"/>

Поищем эксплойт для этой версии, мы получим его в базе данных эксплойтов:


<img src="https://imgur.com/DyBUiNI.png"/>


Установите эксплойт на свой локальный компьютер и передайте его на целевой сервер через веб-сервер netcat или python:

<img src="https://imgur.com/EmcReja.png"/>

<img src="https://imgur.com/96rP4fx.png"/>

<img src="https://imgur.com/pLxyAfz.png"/>

И у нас есть рут!!!
