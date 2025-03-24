# API приложений через gRPC

## Сервис расчёта статистики

### Calculation Service
 - CRUD проекта/модуля (Данные по таблицам "Проекты" и "Модули"-> Записал)
 - Запрос перерасчёта по модулям (Передаётся UUID модулей списком-> Ожидай данные расчёта в раббите)
 - Запрос полного перерасчёта (Тыркнуть процесс расчёта по всем -> Ожидай данные расчёта в раббите)
 - Запрос/получение списков проектов/модулей (?)
 - Запрос формирования отчёта (Данные по расчётам -> Отчёт)

### Report Service
 - REST JIRA
 - REST Mail

## Сервис данных по миграции объекто БД

### Сервис списка проектов модулей и привязанных пользователей
 - CRUD проекта/модуля/пользователей (Данные от гейта по CRUD данных по таблицам "Проекты", "Модули", "Привязка пользователей к проекту/модулю -> Записал)
 - Привязка/отвязка объекта к модулю (Данные от гейта на запись UUID модуля, UUID объекта, тип операции -> Записал)
 - Данные по объектам (Данные с другого сервиса "Сервис работы с объектами БД" список объектов БД из таблицы "Объекты БД" -> Записать UUID объектов с типами)
 - Запрос данных по проектам/модулям (Запрос от сервиса "Сервис расчёта статистики" -> Отдать изменения по проектам/модулям/привязанным объектам)
 - Данные по проектам/модулям/пользователям (Отвправка CRUD данных по таблицам "Проекты", "Модули", "Привязка пользователей к проекту/модулю в сервис "Сервис работы с объектами БД" -> Записал)

syntax = "proto3";

import "google/protobuf/empty.proto";

option java_multiple_files = true;
option java_package = "ru.inversion.proto";
option java_outer_classname = "ODBMigration";
option objc_class_prefix = "ATR";

option java_generate_equals_and_hash = true;
option optimize_for = LITE_RUNTIME;

package account_transport;

service ODBMigrationService {

  //Списки объектов БД
  rpc GetObjectsData(ObjectUuidsList) returns (google.protobuf.Empty) {}

  rpc RecalculateAll(RecalculationAllMessage) returns (google.protobuf.Empty) {}
  rpc RecalculateByModules(RecalculationByModulesMessage) returns (google.protobuf.Empty) {}

  //Project
  rpc GetProject(ProjectMessage) returns (google.protobuf.Empty) {}
  rpc AddProject(ProjectMessage) returns (google.protobuf.Empty) {}
  rpc DeleteProject(ProjectMessage) returns (google.protobuf.Empty) {}
  rpc EditProjectMessage(ProjectMessage) returns (google.protobuf.Empty) {}

  //Module
  rpc AddModule(ModuleMessage) returns (google.protobuf.Empty) {}
  rpc DeleteModule(ModuleMessage) returns (google.protobuf.Empty) {}
  rpc EditModuleName(ModuleMessage) returns (google.protobuf.Empty) {}

  //User
  rpc AddUser(UserMessage) returns (google.protobuf.Empty) {}
  rpc EditUser(UserMessage) returns (google.protobuf.Empty) {}
  rpc DeleteUser(UserMessage) returns (google.protobuf.Empty) {}
  
  //Link
  rpc LinkObjects(ModuleObjects) returns (google.protobuf.Empty) {}
  rpc UnLinkObjects(ModuleObjects) returns (google.protobuf.Empty) {}

  //DBObject's List
  rpc GetObjects (ObjectUuidsList)  returns (PreparedRecalculationMessage) {}
}

message UserMessage {
  User user = 1;
}

message ModuleMessage {
  Module module = 1;
}

message ProjectMessage {
  Project project = 1;
}

message ModulesMessage {
  repeated Module module = 1;
}


message RecalculationByModulesMessage {
  string uuid = 1;
  repeated Module modules = 2;
}

message RecalculationAllMessage {
  string uuid = 1;
}

message PreparedRecalculationMessage {
  string uuid = 1;
  uint64 count = 2;
}


message Module {
  string id = 1;
  string name = 2;
}

message Project {
  string id = 1;
  string name = 2;
  string description = 3;
  string url_jira = 4;
}

message ModuleObjects {
  string project_uuid = 1;
  Module module = 2;
  DbObjectsList dbObjectsList = 3;
}

message ModuleObjectsList{
  repeated ModuleObjects project_uuid=1;
}

message ObjectUuid {
  string uuid = 1;
}

message ObjectUuidsList {
  repeated ObjectUuid object_uuid = 1;
}

message User {
  uint64 id = 1;
  string email = 2;
  string token = 3;
}

message DbObjectsList {
  repeated DbObjects dbObjectsList = 1;
}

message DbObjects {
  string id = 1;
  string owner = 2;
  string type = 3;
  int64 user = 4;
  int32 days = 5;
  string comment = 6;
}

### Сервис расчёта статистики
 - Данные по проектам/модулям (Ответ на "Запрос данных по проектам/модулям" от "Сервис списка проектов модулей и привязанных пользователей")
 - Запрос перерасчёта (Запрос от гейта -> Ожидай данные расчёта в раббите) [Взимодейтсвие с Серегой]
 - Запрос перерасчёта по модулям (Передаётся UUID модулей списком-> Ожидай данные расчёта в раббите) [Взимодейтсвие с Серегой]
 - Запрос данных по объектам (Опросить "Сервис работы с объектами БД" по изменениям в объектах)

### Сервис работы с объектами БД
 - Данные по объектам (Ответ на "Запрос данных по объектам" от "Сервис расчёта статистики" -> Данные по объектам)
 - Запрос списков объектов БД (Запрос от гейта -> Ожидай данные расчёта в раббите)
 - Запрос на миграцию таблицы (UUID объекта по типу таблица-> Выполнено)
 - Запрос на создание задачи в JIRA (Запрос от гейта -> Задача на миграцию с JIRA)

## Сервис данных по процессу миграци JAR

### Artifact info Service

### Calculation Service