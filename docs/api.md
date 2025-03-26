# API приложений через gRPC

## Сервис расчёта статистики

### Calculation Service


### Report Service
 - REST JIRA
 - REST Mail

## Сервис данных по миграции объекто БД

<table style="background-color:lightgrey;">
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

### Artifact info Service

### Calculation Service