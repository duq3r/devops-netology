# **Домашнее задание к занятию "4.3. Языки разметки JSON и YAML"**


## 
**Выполнил Мадан Евгений**



1. Мы выгрузили JSON, который получили через API запрос к нашему сервису:

    ```
    { "info" : "Sample JSON output from our service\t",

        "elements" :[
            { "name" : "first",
            "type" : "server",
            "ip" : 7175 
            },
            { "name" : "second",
            "type" : "proxy",
            "ip : 71.78.22.43
            }
        ]
    }
    ```
    Ответ:
    ```
        { "info" : "Sample JSON output from our service\t",
        "elements" :[
            { "name" : "first",
            "type" : "server",
            "ip" : "7175"  #Тут тоже желательны кавычки, т.к. из-за их отсутствия ошибки могут быть при правильном IP адресе
            },
            { "name" : "second",
            "type" : "proxy",
            "ip" : "71.78.22.43"  #Не установлены кавычки 
            }
        ]
    }
    ```


Нужно найти и исправить все ошибки, которые допускает наш сервис



1. В прошлый рабочий день мы создавали скрипт, позволяющий опрашивать веб-сервисы и получать их IP. К уже реализованному функционалу нам нужно добавить возможность записи JSON и YAML файлов, описывающих наши сервисы. Формат записи JSON по одному сервису: { "имя сервиса" : "его IP"}. Формат записи YAML по одному сервису: - имя сервиса: его IP. Если в момент исполнения скрипта меняется IP у сервиса - он должен так же поменяться в yml и json файле.


```
#!/usr/bin/env python3

import socket as s
import time as t
import datetime as d
import json
import yaml

# set variables
i = 1
wait = 2 # интервал проверок в секундах
srv = {'drive.google.com':'0.0.0.0', 'mail.google.com':'0.0.0.0', 'google.com':'142.251.1.113', 'docs.google.com':'4.4.4.4'}
error=0
fpath = "/home/vagrant/python/" #путь к файлам конфигов
flog  = "/home/vagrant/python/error.log" #путь к файлам логов

print('*** Текущие IP адреса серверов: ***')
print(srv)
print('********************')

while i<=5 : #отладочное число проверок
  for host in srv:
    ip = s.gethostbyname(host)
    if ip != srv[host]:
      print(str(d.datetime.now().strftime("%Y-%m-%d %H:%M:%S")) +' [ERROR] ' + str(host) +' IP mistmatch Old IP:'+srv[host]+' New IP:'+ip)
      srv[host]=ip
      with open(flog,'a') as fl:
          print(str(d.datetime.now().strftime("%Y-%m-%d %H:%M:%S")) +' [ERROR] ' + str(host) +' IP mistmatch: '+srv[host]+' '+ip,file=fl)
      # json
      with open(fpath+host+".json",'w') as jsf:
          json_data= json.dumps({host:ip})
          jsf.write(json_data)
        # yaml
      with open(fpath+host+".yaml",'w') as ymf:
          yaml_data= yaml.dump([{host : ip}])
          ymf.write(yaml_data)
      #в один общий файл
      data = []
      for host in srv:
        data.append({host:ip})
      with open(fpath+"services_conf.json",'w') as jsf:
        json_data= json.dumps(data)
        jsf.write(json_data)
      with open(fpath+"services_conf.yaml",'w') as ymf:
        yaml_data= yaml.dump(data)
        ymf.write(yaml_data)

# На след итерацию через таймаут
  i+=1
  t.sleep(wait)

print('*** Последние IP адреса серверов: ***')
print(srv)
print('********************')
```

Скрин
[![Image alt][((https://github.com/duq3r/devops-netology/yaml/scrnshot.jpg))](https://github.com/duq3r/devops-netology/blob/main/yaml/scrnshot.jpg)
