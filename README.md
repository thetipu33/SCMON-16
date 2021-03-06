#SCMON-16: Transaction Monitoring
##Overview
The purpose of the task is to track fraudulous transactions.
##Structure
Main directory of SCMON-16 contains the Job files. The entry point of the mapping is "Job_Main_Transaction_Monitoring.kjb". It also contains 2 more directories **Transformations** and **Attachments**.
####File List
#####SCMON-16
```
.:  

.environment

.parameters

Job_Mail.kjb

Job_Main_Transaction_Monitoring.kjb

Job_POC.kjb

Job_Set_Variables.kjb

Job_Write_To_Log.kjb
```
#####Transformations
```
.: 

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
#####Attachments
This directory stores the csv attachments which are to be sent to users by mail.

##Installation and Configuration
###Connect to Server
To deploy SCMON-16 connect to desired server at port 5433
* bitesting.simpledcard.com
* bi.simpledcard.com
First connect to VPN. After that make ssh tunnel using **putty** on windows. For more information [Visit this](http://www.postgresonline.com/journal/archives/38-PuTTY-for-SSH-Tunneling-to-PostgreSQL-Server.html).

For linux base operating systems change in the following command with proper credentials
```
ssh -L 65432:localhost:5432 username@server.com
```
For more information [Visit this](https://www.postgresql.org/docs/9.1/static/ssh-tunnels.html)

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
It is used to specify which configuration you want to use. If you want to use the configuration change the use value to 1 and rest to 0. Make sure that there should always be 1 configuration to be used.
####livedb_host
It is the replica of the production server listing on port 5432. specify its host name.
####live_db_database
It is the database "simpled_cards".
####live_port
It is the server port 5432.
####livedb_user
It is the username to connect to database.
####livedb_pwd
It is the password.
####mgtdb_host
It is the management server listing on port 5433.
####mgtdb_db_database
It is the database "simpled_card_reporting".
####mgtdb_port
It is the server port 5433.
####mgtdb_user
It is the username to connect to database.
####mgtdb_pwd
It is the password.
####smtp_server
smpt server that is used to send mails. Currently we are using smtp server of **Gmail** smtp.gmail.com.
To configure it you need an authenticated gmail account. Login to your gmail account by creating a new gmail account or use existing one.

[Visit this link](https://www.google.com/settings/security/lesssecureapps) and click on the **Turn on** radio button **Access for less secure apps**. For more information [Read this](https://support.google.com/accounts/answer/6010255?hl=en).
####smpt_port
smtp port for smpt server. 465 is for smtp.gmail.com
####sender_name
Specify the Sender's Name that will appear in the Email.
####sender_email
Specify the email address of gmail account through which you want to send emails.
####sender_pwd
Specify the passwod of gmail account through which you want to send emails.
##Test Case
For test case please execute following sql scripts.
```
INSERT INTO transactionmonitoring.transactionmonitoringrules(
sql, owner, frequency, action, email_address, email_body, description, 
next_review_date, create_date, update_date, last_run_date)
VALUES ('select id, customer_id, customer_name, amount, date, txn_type, 
txn_completion_status, card_holder_name, comments from from_sc.financial_transactions_presentation
where amount > 1000'
, 'pentaho', '1 min', 'write to log', '', '', 'Description_1m', 
CURRENT_TIMESTAMP,CURRENT_TIMESTAMP, CURRENT_TIMESTAMP, CURRENT_TIMESTAMP);

INSERT INTO transactionmonitoring.transactionmonitoringrules(
sql, owner, frequency, action, email_address, email_body, description, 
next_review_date, create_date, update_date, last_run_date)
VALUES ('select id, customer_id, customer_name, amount, date, txn_type,
txn_completion_status, card_holder_name, comments from from_sc.financial_transactions_presentation
where amount > 1000'
, 'pentaho', '1 min', 'mail', 'erik@globalorange.nl', 'Hi body..', 'SCMON-16: Transactions Presentations_1m', 
CURRENT_TIMESTAMP,CURRENT_TIMESTAMP, CURRENT_TIMESTAMP, CURRENT_TIMESTAMP);

INSERT INTO transactionmonitoring.transactionmonitoringrules(
sql, owner, frequency, action, email_address, email_body, description, 
next_review_date, create_date, update_date, last_run_date)
VALUES ('select id, customer_id, customer_name, amount, date, txn_type,
txn_completion_status, card_holder_name, comments from from_sc.financial_transactions_presentation
where amount > 1000'
, 'pentaho', '5 min', 'mail', 'erik@globalorange.nl', 'Hi body..', 'SCMON-16: Transactions Presentations_5m', 
CURRENT_TIMESTAMP,CURRENT_TIMESTAMP, CURRENT_TIMESTAMP, CURRENT_TIMESTAMP);


INSERT INTO transactionmonitoring.transactionmonitoringrules(
sql, owner, frequency, action, email_address, email_body, description, 
next_review_date, create_date, update_date, last_run_date)
VALUES ('select id, customer_id, customer_name, amount, date, txn_type,
txn_completion_status, card_holder_name, comments from from_sc.financial_transactions_presentation
where amount > 1000'
, 'pentaho', '1 day', 'mail', 'erik@globalorange.nl', 'Hi body..', 'SCMON-16: Transactions Presentations_1d', 
CURRENT_TIMESTAMP,CURRENT_TIMESTAMP, CURRENT_TIMESTAMP, CURRENT_TIMESTAMP);

INSERT INTO transactionmonitoring.transactionmonitoringrules(
sql, owner, frequency, action, email_address, email_body, description, 
next_review_date, create_date, update_date, last_run_date)
VALUES ('select id, customer_id, customer_name, amount, date, txn_type,
txn_completion_status, card_holder_name, comments from from_sc.financial_transactions_presentation
where amount > 1000'
, 'pentaho', '1 week', 'mail', 'erik@globalorange.nl', 'Hi body..', 'SCMON-16: Transactions Presentations_1w', 
CURRENT_TIMESTAMP,CURRENT_TIMESTAMP, CURRENT_TIMESTAMP, CURRENT_TIMESTAMP);
```
