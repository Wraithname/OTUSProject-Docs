# Диаграммы последовательности сервиса получения данных по процессу миграции объектов БД

## **`USSDTODB-SEQD1`**

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