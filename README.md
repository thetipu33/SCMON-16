#SCMON-16: Transaction Monitoring
##Overview
The purpose of the task is to track fraudulous transactions.
##File List
```
.: 

.environment

.parameters

Job_Mail.kjb

Job_Main_Transaction_Monitoring.kjb

Job_POC.kjb

Job_Set_Variables.kjb

Job_Write_To_Log.kjb

Transformations

MailFiles
```
```
.: Transformations

Trans_Get_Metata.ktr

Trans_Load_Mail.ktr

Trans_Load_Metadata_Injection.ktr

Trans_Load_Query.ktr

Trans_Make_CSV_File.ktr

Trans_Set_Current_Timestamp.ktr

Trans_Set_Variables.ktr

Trans_Template.ktr

Trans_Write_To_Log.ktr

Trans_Write_To_Log_Meta.ktr
```
##Installation and Configuration
###Connect to Server
To deploy SCMON-16 connect to desired server at port 5433
* bitesting.simpledcard.com
* bi.simpledcard.com

###Create Schema
```
create schema transactionmonitoring;
```
###Create Table
```
CREATE TABLE transactionmonitoring.transactionmonitoringrules
(
  id serial NOT NULL,
  sql text,
  owner character varying(250),
  frequency interval,
  action character varying(250),
  email_address character varying(250),
  email_body text,
  description text,
  next_review_date date,
  create_date timestamp with time zone,
  update_date timestamp with time zone,
  last_run_date timestamp with time zone
)
WITH (
  OIDS=FALSE
);
```
###Change .parameters file
To configure mappings, change the parameters to put the server and mail credentials
####use
**use** is used to specify which configuration you want to use. If you want to use the configuration change the use value to 1 and rest to 0. Make sure that there should always be 1 configuration to be used.
