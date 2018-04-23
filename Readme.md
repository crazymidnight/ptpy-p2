# HW-2

## 2.1 Транспорты
По большей части уже готово, но нужно сделать некоторое количество доработок
функция `get_transport` теперь принемает обязательным аргументом только (transport_name) 
 `host, port, login, passowrd` всё равно можно передать, но если они не переданы, то их значения берутся из файла env.json следующей структуры
```
{
  "host": "localhost",
  "transports": {
    "SSH": {
      "password": "pwd",
      "login": "root",
      "port": 22022
    }
  }
}
```

## 2.2 "Движок"
В пакет scripts проекта добавляются новые скрипты, которые реализуют некую бизнесс-логику.  
Из модуля main.py последовательно запускуются функции main во всех модулях, которые лежат в пакете scripts. 
* Наличие или остутвие какого-либо скрипта никак не влияет на работоспособность остальной программы
* Остальная программа знает только про то что есть директория scripts и всё что в ней есть надо запустить.

В модуле main.py реализовать следующую логику
1. Считать названия файлов из директории scripts и если это исполняемый python файл импортировать его.
2. Для каждого загруженого модуля, вызвать у него функцию main()

## 2.3 Инициализация БД 
1. Создание таблицы `control` со структурой (идентификатор комплаенса, описание комплаенса).
2. Чтение json файла `controls.json` и загрузка данных из него в таблицу `control` структура файла controls.json
```
[
  ["000","File named 'testfile' is present in the root folder on target system."],
  ["456","some other control"]
]
```
3. Создание таблицы `scandata` С результатми сканирования, структура (идентификатор комплаенса, описание комплаенса, статус)  

## 2.4 Функция add_control
Реализовать функцию `add_control(идентификатор контроля, статус)`: которая будет добавлять данные в таблицу scandata. 
Статус контроля вычисляется в процессе выполнения скрипта. Статусы бывают:
    
* STATUS_COMPLIANT = 1 # совместимо
* STATUS_NOT_COMPLIANT = 2 # несовместимо
* STATUS_NOT_APPLICABLE = 3 # неприменимо (отствует транспорт)
* STATUS_ERROR = 4 # ошибка обработаная скриптом
* STATUS_EXCEPTION = 5 # ошибка скрипта обработаная модулем main

Фукнция должна брать описание контроля из таблицы `control` по его идентификатору, и добавлять в записи в таблицу scandata. Идентификатора контролая для статуса 5 можно брать из названия файла.

## 2.5 Первый тестовый контроль
Написать первый тестовый скрипт `000_test_file_exists.py`. 
Скрипт будет проверять наличие тестового файла testfile в корневой директории, сканируемой системы. Если файл существует то, то в таблицу scandata идёт запись со статусом STATUS_COMPLIANT иначе со статусом STATUS_NOT_COMPLIANT. В случае ошибок подключения со статусом STATUS_ERROR

