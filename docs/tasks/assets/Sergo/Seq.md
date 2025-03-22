# Диаграммы последовательности сервиса получения данных по процессу миграции объектов БД

## **`USSCMS-SEQD1`**
```uml
@startuml
header Система расчета статистики
title Создание модуля
actor Пользователь as USER
box #LightGreen  
participant "WEB FORM" as WEB
boundary GATEWAY as GATEWAY
participant  "Calculation Service" as CS
dataBase "DB" as DB
'создание модуля'
USER->WEB++: Создание модуля
USER->WEB: Данные модуля
USER->WEB: Добавление объектов в модуль
WEB->GATEWAY++: Запрос списка объектов
GATEWAY->CS++: Запрос списка объектов
alt Список объектов присутствует в базе данных
CS-->DB++: Получение списка объектов
DB->CS: Список объектов
DB--
CS->GATEWAY: Список объектов
else Список объектов отсутствует в базе данных
CS->CS: Формирование запроса на получение списка объектов
CS->GATEWAY: Запрос получения списка объектов
GATEWAY->GATEWAY: Отправка запроса в сервисы
GATEWAY->GATEWAY: Получение списка объектов от сервисов
GATEWAY->CS: Список объектов
CS->DB: Сохранение списка объектов
CS->GATEWAY: Список объектов
end alt
GATEWAY->WEB: Список объектов
USER->WEB: Выбор объектов
WEB->GATEWAY: Данные модуля
WEB--
GATEWAY->CS: Данные модуля
GATEWAY--
CS->DB: Сохранение данных модуля
CS--
@enduml
```
## **`USSCMS-SEQD2`**
```uml
@startuml
header Система расчета статистики
title Создание задачи на миграцию объектов БД
actor Пользователь as USER
box #LightGreen  
participant "WEB FORM" as WEB
boundary GATEWAY as GATEWAY
boundary jira as JIRA
participant  "Calculation Service" as CS
dataBase "DB" as DB
'создание задачи на миграцию объекта'
USER->WEB++: Открытие формы "Объекты БД"
WEB->GATEWAY++: Запрос данных объектов БД
GATEWAY->CS++: Запрос данных объектов БД
CS->DB++: Получение данных объектов БД
DB->CS: Данные объектов БД
DB--
CS->GATEWAY: Данные объектов БД
GATEWAY->WEB: Данные объектов БД

USER->WEB: Нажатие кнопки "Создать задачу"
WEB->GATEWAY: Запрос списка проектов
GATEWAY->CS: Запрос списка проектов
CS->DB++: Получение списка проектов
DB->CS: Список проектов
DB--
CS->GATEWAY: Список проектов
GATEWAY->WEB: Список проектов

USER->WEB: Выбор проекта
WEB->GATEWAY: Запрос списка модулей
GATEWAY->CS: Запрос списка модулей
CS->DB++: Получение списка списка модулей
DB->CS: Список модулей
DB--
CS->GATEWAY: Список модулей
GATEWAY->WEB: Список модулей

USER->WEB: Выбор модуля
WEB->GATEWAY: Запрос списка пользователей
GATEWAY->CS: Запрос списка пользователей
CS->CS: Формирование запроса на получение списка пользователей
CS->GATEWAY: Запрос списка пользователей
GATEWAY->JIRA++: Запрос списка пользователей
JIRA->GATEWAY: Список пользователей
JIRA--
GATEWAY->CS: Список пользователей
CS->GATEWAY: Список пользователей
GATEWAY->WEB: Список пользователей

USER->WEB: Выбор исполнителя
USER->WEB: Выбор тестировщика
USER->WEB: Нажата кнопка "ОК"

WEB->GATEWAY: Отправка данных задачи
WEB--
GATEWAY->CS: Отправка данных задачи
CS->DB: Сохранение данных задачи
CS->CS: Формирование запроса на регистрацию задачи
CS->GATEWAY: Запрос на регистрацию задачи
GATEWAY->JIRA++: Запрос на регистрацию задачи
JIRA->JIRA: Создание задачи
JIRA->GATEWAY: Данные созданной задачи задачи
JIRA--
GATEWAY->CS: Данные созданной задачи задачи
GATEWAY--
CS->DB: Обновление данных задачи
CS--
@enduml
```
## **`USSCMS-SEQD3`**
```uml
@startuml
header Система расчета статистики
title Создание задачи на миграцию артефактов
actor Пользователь as USER
box #LightGreen  
participant "WEB FORM" as WEB
boundary GATEWAY as GATEWAY
boundary jira as JIRA
participant  "Calculation Service" as CS
dataBase "DB" as DB
USER->WEB++: Открытие формы "Артефакты"
WEB->GATEWAY++: Запрос данных по артефактам
GATEWAY->CS++: Запрос данных по артефактам
CS->DB++: Получение данных по артефактам
DB->CS: Данные по артефактам
DB--
CS->GATEWAY: Данные по артефактам
GATEWAY->WEB: Данные по артефактам

USER->WEB: Нажатие кнопки "Создать задачу"
WEB->GATEWAY: Запрос списка проектов
GATEWAY->CS: Запрос списка проектов
CS->DB++: Получение списка проектов
DB->CS: Список проектов
DB--
CS->GATEWAY: Список проектов
GATEWAY->WEB: Список проектов

USER->WEB: Выбор проекта
WEB->GATEWAY: Запрос списка модулей
GATEWAY->CS: Запрос списка модулей
CS->DB++: Получение списка списка модулей
DB->CS: Список модулей
DB--
CS->GATEWAY: Список модулей
GATEWAY->WEB: Список модулей

USER->WEB: Выбор модуля
WEB->GATEWAY: Запрос списка пользователей
GATEWAY->CS: Запрос списка пользователей
CS->CS: Формирование запроса на получение списка пользователей
CS->GATEWAY: Запрос списка пользователей
GATEWAY->JIRA++: Запрос списка пользователей
JIRA->GATEWAY: Список пользователей
JIRA--
GATEWAY->CS: Список пользователей
CS->GATEWAY: Список пользователей
GATEWAY->WEB: Список пользователей

USER->WEB: Выбор исполнителя
USER->WEB: Выбор тестировщика
USER->WEB: Нажата кнопка "ОК"

WEB->GATEWAY: Отправка данных задачи
WEB--
GATEWAY->CS: Отправка данных задачи
CS->DB: Сохранение данных задачи
CS->CS: Формирование запроса на регистрацию задачи
CS->GATEWAY: Запрос на регистрацию задачи
GATEWAY->JIRA++: Запрос на регистрацию задачи
JIRA->JIRA: Создание задачи
JIRA->GATEWAY: Данные созданной задачи задачи
JIRA--
GATEWAY->CS: Данные созданной задачи задачи
GATEWAY--
CS->DB: Обновление данных задачи
CS--
@enduml
```
## **`USSCMS-SEQD4`**
```uml
@startuml
header Система расчета статистики
title Получение списка модулей
actor Пользователь as USER
box #LightGreen  
participant "WEB FORM" as WEB
boundary GATEWAY as GATEWAY
participant  "Calculation Service" as CS
dataBase "DB" as DB
USER->WEB++: Выбор проекта
WEB->GATEWAY++: Запрос списка модулей
GATEWAY->CS++: Запрос списка модулей
alt Список модулей присутствует в базе данных
CS-->DB++: Получение списка модулей
DB->CS: Список модулей
DB--
CS->GATEWAY: Список модулей
GATEWAY->WEB: Список модулей
else Список модулей отсутствует в базе данных
CS-->DB++: Получение списка модулей
DB->CS: Пустой список модулей
DB--
CS->GATEWAY: Пустой список модулей
CS--
GATEWAY->WEB: Пустой список модулей
GATEWAY--
WEB--
end alt
@enduml
```
## **`USSCMS-SEQD5`**
```uml
@startuml
header Система расчета статистики
title Получение списка проектов
actor Пользователь as USER
box #LightGreen  
participant "WEB FORM" as WEB
boundary GATEWAY as GATEWAY
boundary jira as JIRA
participant  "Calculation Service" as CS
dataBase "DB" as DB
USER->WEB++: Открытие формы проекты
WEB->GATEWAY++: Запрос списка проектов
GATEWAY->CS++: Запрос списка проектов
CS->CS: Формирование запроса на получение списка проектов
CS->GATEWAY: Отправка запроса на получение списка проектов
GATEWAY->JIRA++: Отправка запроса на получение списка проектов
JIRA->GATEWAY: Список проектов
JIRA--
GATEWAY->CS: Отправка списка проектов
CS->DB: Сохранение списка проектов
CS->GATEWAY: Список проектов
CS--
GATEWAY->WEB: Список проектов
GATEWAY--
@enduml
```
## **`USSCMS-SEQD6`**
```uml
@startuml
header Система расчета статистики
title Получение общей статистики по системе
actor Пользователь as USER
box #LightGreen  
participant "WEB FORM" as WEB
boundary GATEWAY as GATEWAY
participant  "Calculation Service" as CS
dataBase "DB" as DB
queue RabbitMQ as rabbit
USER->WEB++: Открытие формы статистики
WEB->GATEWAY++: Запрос последней рассчитанной статистики
GATEWAY->CS++: Запрос последней рассчитанной статистики
CS->DB++: Получение последней рассчитанной статистики

alt Статистика присутствует в базе данных
DB->CS: Ранее рассчитанная статистика по системе
DB--
else Статистики отсутствует в базе данных
CS->GATEWAY: Сообщение об отсутствии статистики
GATEWAY->WEB: Сообщение об отсутствии статистики
USER->WEB: Запрос расчета статистики
WEB->GATEWAY: Запрос расчета статистики
GATEWAY->CS: Запрос расчета статистики
CS->CS: Формирование запроса на расчет статистики в сервисы
CS->GATEWAY: Запрос на расчета статистики
loop Получение статистики по объектам
rabbit->CS: Статистика от сервисов
CS->CS: Расчет общей статистики
CS->DB: Сохранение статистики
end loop
end alt
CS->GATEWAY: Статистика по системе
CS--
GATEWAY->WEB: Статистика по системе
GATEWAY--
@enduml
```
## **`USSCMS-SEQD7`**
```uml
@startuml
header Система расчета статистики
title Формирование отчета статистики миграции по объектам БД
actor Пользователь as USER
box #LightGreen  
participant "WEB FORM" as WEB
boundary GATEWAY as GATEWAY
participant  "Calculation Service" as CS
dataBase "DB" as DB
queue RabbitMQ as rabbit
USER->WEB++: Открытие формы "Объекты БД"
WEB->GATEWAY++: Запрос данных объектов БД
GATEWAY->CS++: Запрос данных объектов БД
CS->DB++: Получение данных объектов БД
DB->CS: Данные объектов БД
DB--
CS->GATEWAY: Данные объектов БД
GATEWAY->WEB: Данные объектов БД

USER->WEB: Нажатие кнопки "Сформировать отчет"
WEB->GATEWAY: Запрос расчета статистики
GATEWAY->CS: Запрос расчета статистики
CS->CS: Формирование запроса на расчет статистики в сервисы расчета статистики объектов БД
CS->GATEWAY: Запрос расчета статистики

loop Получение статистики по объектам
CS->rabbit++: Получение статистики от сервиса
rabbit->CS: Статистика от сервиса
rabbit--
CS->CS: Расчет статистики
CS->DB: Сохранение статистика
CS->GATEWAY: Статистика
CS--
GATEWAY->WEB: Статистика
GATEWAY--
WEB--
end loop
@enduml
```
## **`USSCMS-SEQD8`**
```uml
@startuml
header Система расчета статистики
title  Формирование отчета статистики миграции по артефактам
actor Пользователь as USER
box #LightGreen  
participant "WEB FORM" as WEB
boundary GATEWAY as GATEWAY
participant  "Calculation Service" as CS
dataBase "DB" as DB
queue RabbitMQ as rabbit
USER->WEB++: Открытие формы "Артефакты"
WEB->GATEWAY++: Запрос данных по артефактам
GATEWAY->CS++: Запрос данных по артефактам
CS->DB++: Получение данных по артефактам
DB->CS: Данные по артефактам
DB--
CS->GATEWAY: Данные по артефактам
GATEWAY->WEB: Данные по артефактам

USER->WEB: Нажатие кнопки "Сформировать отчет"
WEB->GATEWAY: Запрос расчета статистики
GATEWAY->CS: Запрос расчета статистики
CS->CS: Формирование запроса на расчет статистики в сервисы расчета статистики по артефактам
CS->GATEWAY: Запрос расчета статистики

loop Получение статистики по объектам
CS->rabbit++: Получение статистики от сервиса
rabbit->CS: Статистика от сервиса
rabbit--
CS->CS: Расчет статистики
CS->DB: Сохранение статистика
CS->GATEWAY: Статистика
CS--
GATEWAY->WEB: Статистика
GATEWAY--
WEB--
end loop
@enduml
```
## **`USSCMS-SEQD9`**
```uml
@startuml
header Система расчета статистики
title Обновление структуры объектов БД
actor Пользователь as USER
box #LightGreen  
participant "WEB FORM" as WEB
boundary GATEWAY as GATEWAY
participant  "Calculation Service" as CS
dataBase "DB" as DB
'обновление структуры объектов бд'
USER->WEB++: Открытие формы "Объекты БД"
WEB->GATEWAY++: Запрос данных объектов БД
GATEWAY->CS++: Запрос данных объектов БД
CS->DB++: Получение данных объектов БД
DB->CS: Данные объектов БД
DB--
CS->GATEWAY: Отправка данных объектов БД
GATEWAY->WEB: Данные объектов БД

USER->WEB: Нажатие кнопки "Обновить структуру"
WEB->GATEWAY: Запрос обновления объектов БД
GATEWAY->CS: Запрос обновления объектов БД
CS->CS: Формирование запроса на получение объектов БД
CS->GATEWAY: Запрос получения объектов БД
GATEWAY->GATEWAY: Получение данных объектов БД
GATEWAY->CS: Данные объектов БД
CS->DB: Сохранение новых данных объектов БД
CS->GATEWAY: Данные объектов БД
CS--
GATEWAY->WEB: Данные объектов БД
GATEWAY--
@enduml
```
## **`USSCMS-SEQD10`**
```uml
@startuml
header Система расчета статистики
title Обновление структуры артефактов
actor Пользователь as USER
box #LightGreen  
participant "WEB FORM" as WEB
boundary GATEWAY as GATEWAY
participant  "Calculation Service" as CS
dataBase "DB" as DB
USER->WEB++: Открытие формы "Артефакты"
WEB->GATEWAY++: Запрос данных по артефактам
GATEWAY->CS++: Запрос данных по артефактам
CS->DB++: Получение данных по артефактам
DB->CS: Данные по артефактам
DB--
CS->GATEWAY: Отправка данных по артефактам
GATEWAY->WEB: Данные по артефактам

USER->WEB: Нажатие кнопки "Обновить структуру"
WEB->GATEWAY: Запрос обновления данных по артефактам
GATEWAY->CS: Запрос обновления данных по артефактам
CS->CS: Формирование запроса на получение данных по артефактам
CS->GATEWAY: Запрос получения данных по артефактам
GATEWAY->GATEWAY: Получение данных по артефактам
GATEWAY->CS: Данные по артефактам
CS->DB: Сохранение новых данных по артефактам
CS->GATEWAY: Данные по артефактам
CS--
GATEWAY->WEB: Данные по артефактам
GATEWAY--
@enduml
```