# Диаграммы последовательности сервиса получения данных по процессу миграции объектов БД

```uml
@startuml
autonumber
title Получение списка проектов, модулей и привязанных пользователей
box Сбор статистики #LightYellow
boundary "Сервис расчёта" as SPC
end box
box Core #LightBlue
participant "API Gateway" as Gate
end box
box Анализ объектов БД #LightGreen
boundary "Сервис проектов и модулей" as SPM
dataBase "БД модулей и пользователей" as DBLoc
end box

hide footbox
autonumber "<b>[0]"
SPC++
SPC-->Gate++: Передача CRUD в "Сервис проектов и модулей"
Gate-->SPM++: CRUD по проектам, модулям и пользователям привязанных к модулям
Gate--
SPM-->SPM: Обработка
SPM-->DBLoc++: Запись в БД
@enduml
```

```uml
@startuml
actor "API Gateway" as GW 
header Система анализа клиентской части
title CRUD модуля
box #LightGreen   
participant  "Artifacts Info Service" as AS 
dataBase "In-mem cache" as C
dataBase "DB Модулей и артефактов" as DB 

AS++
GW --> AS: CRUD модуля
alt Удаление модуля
AS --> DB++: Удаление модуля
DB --> DB: Установка связанных ключей в null 
AS --> DB: Выбрать все файлы где ключ модуля null
return Список файлов
AS --> C: Удалить модуль по ключу из кеша
AS --> C: Добавить в список непривязанные файлы
else Вставка/обновление наименования модуля
AS --> DB: Операция над модулем
AS --> C: Обновление данных в кеше
end alt
opt Произошла ошибка в процессе обработки
AS --> GW: ERROR_CODE
end opt
AS --> GW: OK
AS--
@enduml
```

```uml
@startuml
actor "API Gateway" as GW 
header Система анализа клиентской части
title Запрос списков файлов, модулей с файлами
box #LightGreen   
participant  "Artifacts Info Service" as AS 
dataBase "In-mem cache" as C
dataBase "DB Модулей и артефактов" as DB 

opt Прогрев кеша при запуске Artifacts Info service
AS++
AS --> DB++: Запрос список модулей и файлов
return Список модулей и файлов
AS --> C: Добавление списков в кеш
AS--
end opt
GW --> AS++: Запрос списка
AS --> C++: Запрос данных из кеша
return Данные
opt Произошла ошибка в процессе выборки данных
AS --> GW: ERROR_CODE
end opt
return Данные
@enduml
```

```uml
@startuml
actor "API Gateway" as GW 
header Система анализа клиентской части
title Удаление файла из списка файлов
box #LightGreen   
participant  "Artifacts Info Service" as AS 
dataBase "In-mem cache" as C
dataBase "DB Модулей и артефактов" as DB 

AS++
GW --> AS: Запрос на удаление файла
AS --> DB: Удаление файла
AS --> C: Обновление данных в кеше
opt Произошла ошибка в процессе обработки
AS --> GW: ERROR_CODE
end opt
AS --> GW: OK
AS--
@enduml
```

```uml
@startuml
header Система анализа клиентской части
title Обновление данных о файлах
box #LightGreen   
participant  "Artifacts Info Service" as AS 
dataBase "In-mem cache" as C
dataBase "DB Модулей и артефактов" as DB 
boundary "FTP-сервер" as FTP

loop Периодическое обновление данных о файлах на FTP сервере
AS++
AS --> FTP++: Получение списка всех файлов на сервере
return: Список файлов
AS --> C++: Получение списка непривязанных файлов и модулей
return Список файлов и модулей с файлами
AS --> AS: Сравнение списков, формирование списка изменений
AS --> DB: Обновление данных о файлах в БД
AS --> C: Обновление данных о файлах в кеше
AS--
end loop
@enduml
```

```uml
@startuml
actor "API Gateway" as GW 
header Система анализа клиентской части
title Привязка/отвязка файлов
box #LightGreen   
participant  "Artifacts Info Service" as AS 
dataBase "In-mem cache" as C
dataBase "DB Модулей и артефактов" as DB 

AS++
GW --> AS: Привязка/отвязка файлов 
AS --> DB: Обновление данных в БД
AS --> C: Обновление данных в кеше
opt Произошла ошибка в процессе обработки
AS --> GW: ERROR_CODE
end opt
AS --> GW: OK
AS--
@enduml
```

```uml
@startuml
header Система анализа клиентской части
title Запуск сервиса CalculationService
box #LightGreen   
boundary "FTP-сервер" as FTP
participant  "Calculation Service" as CS 
dataBase "DB Анализатора файлов" as CSDB 
queue "RabbitMQ" as RMQ

loop Анализ файлов любой незавершенной обработки
CS --> CS: Выбор первого необработанного файла
CS --> CSDB++: Выборка данных анализа из результата последнего анализа файла
return Данные анализа
alt Дата создания файла в последнем анализе эквивалентна дате создания файла
CS --> RMQ: Отправка данных предыдущего анализа (включая uuid текущего процесса)
CS --> CSDB: Удаление файла из списка рекалькуляции
else Не эквивалентна/отсутствует
CS --> FTP++: Выгрузка файла
return Файл
CS --> CS: Анализ файла
CS --> CSDB: Обновление/добавление данных анализа
CS --> RMQ: Отправка данных анализа (включая uuid текущего процесса)
CS --> CSDB: Удаление файла из списка рекалькуляции
CS--
end alt
end loop
@enduml
```

```uml
@startuml
actor "API Gateway" as GW 
header Система анализа клиентской части
title Запрос полного перерасчета
box #LightGreen   
participant  "Artifacts Info Service" as AS 
dataBase "In-mem cache" as C
dataBase "DB Модулей и артефактов" as DB 
boundary "FTP-сервер" as FTP
participant  "Calculation Service" as CS 
dataBase "DB Анализатора файлов" as CSDB 
queue "RabbitMQ" as RMQ

GW --> CS: Запрос перерасчета
CS++
CS --> AS++: Запрос полного списка файлов
AS --> C++: Получение списка полного файлов
return Список файлов
return Список файлов для анализа
CS --> CSDB: Добавление процесса рекалькуляции данных (uuid + спискок файлов)
CS --> GW: Кол-во файлов с uuid рекалькуляции

loop Анализ файлов
CS --> CS: Выбор первого необработанного файла
CS --> CSDB++: Выборка данных анализа из результата последнего анализа файла
return Данные анализа
alt Дата создания файла в последнем анализе эквивалентна дате создания файла
CS --> RMQ: Отправка данных предыдущего анализа (включая uuid текущего процесса)
CS --> CSDB: Удаление файла из списка рекалькуляции
else Не эквивалентна/отсутствует
CS --> FTP++: Выгрузка файла
return Файл
CS --> CS: Анализ файла
CS --> CSDB: Обновление/добавление данных анализа
CS --> RMQ: Отправка данных анализа (включая uuid текущего процесса)
CS --> CSDB: Удаление файла из списка рекалькуляции
CS--
end alt
end loop
@enduml
```

```uml
@startuml
actor "API Gateway" as GW 
header Система анализа клиентской части
title Запрос перерасчета по модулям
box #LightGreen   
participant  "Artifacts Info Service" as AS 
dataBase "In-mem cache" as C
dataBase "DB Модулей и артефактов" as DB 
boundary "FTP-сервер" as FTP
participant  "Calculation Service" as CS 
dataBase "DB Анализатора файлов" as CSDB 
queue "RabbitMQ" as RMQ

GW --> CS: Запрос перерасчета по модулям
CS++
CS --> AS++: Запрос списка файлов по модулям
AS --> AS: Создать пустой список файлов для возврата в ответе
loop Выборка файлов по модулю из кеша
AS --> C++: Запрос списка файлов по модулю
return: Файлы модуля
AS --> AS: Добавить файлы в список для возврата
end loop
return Список файлов для анализа
CS --> CSDB: Добавление процесса рекалькуляции данных (uuid + спискок файлов)
CS --> GW: Кол-во файлов с uuid рекалькуляции

loop Анализ файлов
CS --> CS: Выбор первого необработанного файла
CS --> CSDB++: Выборка данных анализа из результата последнего анализа файла
return Данные анализа
alt Дата создания файла в последнем анализе эквивалентна дате создания файла
CS --> RMQ: Отправка данных предыдущего анализа (включая uuid текущего процесса)
CS --> CSDB: Удаление файла из списка рекалькуляции
else Не эквивалентна/отсутствует
CS --> FTP++: Выгрузка файла
return Файл
CS --> CS: Анализ файла
CS --> CSDB: Обновление/добавление данных анализа
CS --> RMQ: Отправка данных анализа (включая uuid текущего процесса)
CS --> CSDB: Удаление файла из списка рекалькуляции
CS--
end alt
end loop
@enduml
```

```uml
@startuml
header Система анализа клиентской части
title Процесс сборки и отправки артефактов в SVN
box #LightGreen   
actor "User" as US
boundary "Subversion" as SVN
participant  "Jenkins" as JS 
boundary "FTP-сервер" as FTP

JS --> SVN: Установка периодического опроса
...Спустя некоторое время...
ref over US, SVN: Взаимодействие пользователя с SVN

loop Периодический опрос изменений
JS --> SVN++: Polling-проверка наличия новых коммитов
opt Есть изменения
JS++
JS <--> SVN: Выгрузка коммитов с изменениями
SVN--
JS --> JS: Сборка проекта в артефакт
alt Произошла ошибка при сборке
JS --> FTP: Удалить артефакт
else Сборка завершена
JS --> FTP: Отправить артефакт на FTP (с заменой)
JS--
end opt
end loop
@enduml
```