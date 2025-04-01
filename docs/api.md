# API приложений через gRPC

## Сервис расчёта статистики
Сервис использует вызовы gRPC от сервисов сбора статистики по объектам БД и артефактам.

## Сервис данных по миграции объектов БД

<table style="border:1px solid black;background-color:lightgrey;">
<tr>
   <td>
   <pre>
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

rpc GetProjects(google.protobuf.Empty) returns (stream Project) {}
rpc AddProjects(stream Project) returns (Response) {}
rpc UpdateProjects(stream Project) returns (Response) {}
rpc DeleteProjects(stream Project) returns (Response) {}

rpc GetModules(google.protobuf.Empty) returns (stream Module) {}
rpc AddModule(stream Module) returns (Response) {}
rpc UpdateModules(stream Module) returns (Response) {}
rpc DeleteModules(stream Module) returns (Response) {}

rpc AddUsers(stream User) returns (Response) {}
rpc UpdateUsers(stream User) returns (Response) {}
rpc DeleteUsers(stream User) returns (Response) {}

rpc AddLinkDBOWithUser(stream Project_User_Con) returns (Response) {}
rpc DeleteLinkDBOWithUser(stream Project_User_Con) returns (Response) {}

rpc AddLinkModule(DB_Object_Module_link) returns (Response) {}
rpc DeleteLinkModule(DB_Object_Module_link) returns (Response) {}

rpc CalculateAll(google.protobuf.Empty) returns (Calculate_Response) {}
rpc CalculateModules(stream DB_Object_Module_link) returns (Calculate_Response) {}

rpc getDBObjects(google.protobuf.Empty) returns (DB_ObjectList_Response_on_Calculation) {}
rpc MigrateTable(DB_Object) returns (Response) {}
}

message Project {
    string project_uuid=1;
    string project_name=2;
    string project_description=3;
    string project_jira_url=4;
}

message Module {
    string module_uuid=1;
    string module_name=2;
    string module_description=3;
    string project_uuid=4;
}

message User {
    uint64 user_id=1;
    string user_email=2;
    string user_token=3;
    string user_position=4;
}

message Project_User_Con{
    User user_id=1;
    Module module_uuid=2;
    Project project_uuid=3;
}

message DB_Object{
    string dbo_uuid=1;
    string dbo_owner=2;
    string dbo_type=3;
    string dbo_user=4;
    string dbo_days=5;
    string dbo_comment=6;
}

message Migration_Status{
    uint64 status_id=1;
    string status_name=2;
}

message DB_Object_Module_link{
    DB_Object object=1;
    Module module=2;
}

message DB_Object_Hist{
    uint64 id=1;
    DB_Objects dbo_uuid=2;
    date exec_date=3;
    string version=4;
    DB_Object_Hist_Type type_id=5;
    User user_id=6;
    Migration_Status st_id=7;
    uint64 dbo_percent=8;
}

message DB_Object_Hist_Type{
    uint64 id=1;
    string name=2;
}

message Jira_Task{
    string task_id=1;
    string task_status=2;
    date task_date_create=3;
    DB_Objects object_uuid=4;
    Project project_uuid=5;
    string task_time_pass=6;
}

message Response{
    uint64 code=1;
    string description=2;
}

message DB_ObjectList_Response{
    uint64 code=1;
    string description=2;
    date exec_date=3;
    uint64 object_count=4;
}

message DB_ObjectList_Response_on_Calculation{
    uint64 code=1;
    string description=2;
    DB_Object object=3;
    DB_Object_Hist last_hist=4;
    Jira_Task time_pass=5;
    date last_date_exec=6;
}

message Calculate_Response{
    uint64 code=1;
    string description=2;
    date exec_date=3;
}
</pre>
   </td>
  </tr>
</table>


## Сервис данных по процессу миграци JAR

<table style="border:1px solid black;background-color:lightgrey;">
<tr>
   <td>
   <pre>
syntax = "proto3";

import "google/protobuf/empty.proto";
import "date.proto";
import "datetime.proto";

option java_multiple_files = true;
option java_package = "ru.inversion.migration.artifact";
option java_outer_classname = "ArtifactMigration";
option objc_class_prefix = "ATR";

option java_generate_equals_and_hash = true;
option optimize_for = LITE_RUNTIME;

package artifact_migration;

// Определение gRPC-сервиса
service ArtifactMigrationService {

  //ArtifactsInfoService
  //Создание модуля - server - ArtifactsInfoService, client - gateway
  rpc AddModule(ModuleMessage) returns (google.protobuf.Empty) {}
  //Удаление модуля - server - ArtifactsInfoService, client - gateway
  rpc DeleteModule(ModuleMessage) returns (google.protobuf.Empty) {}
  //Изменение наименования модуля - server - ArtifactsInfoService, client - gateway
  rpc EditModuleName(ModuleMessage) returns (google.protobuf.Empty) {}
  //Привязка файлов к модулю (только для filetype = present) - server - ArtifactsInfoService, client - gateway
  rpc LinkFile(ModuleFiles) returns (google.protobuf.Empty) {}
  //Отвязка файлов от модуля  (только для filetype = present) - server - ArtifactsInfoService, client - gateway
  rpc UnLinkFile(ModuleFiles) returns (google.protobuf.Empty) {}
  //Удаление файла, например если он более не будет в svn для filetype = missing - server - ArtifactsInfoService, client - gateway
  rpc DeleteFiles(FilesMessage) returns (google.protobuf.Empty) {}

  //Списки файлов
  //Запрос списка непривязанных файлов (Исключительно файлы) - server - ArtifactsInfoService, client - gateway
  rpc UnAttachedFiles(google.protobuf.Empty) returns (stream FilesMessage) { }
  //Запрос списка модулей с файлами (Модуль + файлы) - server - ArtifactsInfoService, client - gateway
  rpc AttachedFiles(google.protobuf.Empty) returns (stream ModulesFilesMessage) { }
  //Запрос списка привязанных и непривязанных модулей с файлами (Привязанные и непривязанные)
  //server - ArtifactsInfoService, client - gateway, calculation service (2)
  rpc AllFiles(google.protobuf.Empty) returns (stream ModulesFilesWithFilesMessage) { }


  //Список файлов по модулям (на вход - список модулей, на выход - список файлов)
  //server - ArtifactsInfoService, client - calculation service
  rpc AllFilesByModules(stream ModulesMessage) returns (stream FilesMessage) { } //add state
  //End of ArtifactsInfoService


  //Запрос на полный перерасчет - server - CalculationService, client - gateway
  rpc RecalculateAll(RecalculationAllMessage) returns (google.protobuf.Empty) {}
  //Запрос на перерасчет по модулям - server - CalculationService, client - gateway
  rpc RecalculateByModules(RecalculationByModulesMessage) returns (google.protobuf.Empty) {}

  //Инф-ия о выгрузке - server - gateway, client - CalculationService
  rpc PreparedFilesCalculation(PreparedRecalculationMessage) returns (google.protobuf.Empty) {}
}

message Module {
  string module_uuid=1;
  string module_name=2;
  string module_description=3;
  string project_uuid=4;
}

message File {
  int64 uuid = 1;
  string name = 2;
  string path = 3;
  google.type.DateTime date_creation = 4;
  ArtifactType artifact_type = 5;
  string module_uuid = 6;
  string project_uuid = 7;
}

enum ArtifactType {
  PRESENT = 0;
  MISSING = 1;
}

message ModuleMessage {
  Module module = 1;
}

message ModulesMessage {
  repeated Module module = 1;
}

message ModuleFiles {
  Module module = 2;
  repeated File file = 3;
}

message ModulesFilesMessage {
  repeated ModuleFiles module_file_list = 1;
}

message ModulesFilesWithFilesMessage {
  repeated ModuleFiles module_file_list = 1;
  repeated File unattached_files = 2;
}

message FilesMessage {
  repeated File files = 1;
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
</pre>
   </td>
  </tr>
</table>