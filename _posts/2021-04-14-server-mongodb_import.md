---
title: "MongoDB: export .json 파일을 import 하는 방법"
date: 2021-04-14 09:39:00 +0900
categories: 서버
---

```
mongoimport --host host --port port --username username --password 'password' --authenticationDatabase admin --db databasename --collection collection --type json --file /tmp/file.json --jsonArray
```
