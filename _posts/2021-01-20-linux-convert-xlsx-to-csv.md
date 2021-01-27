---
title: 'CommandLine에서 xlsx 파일을 csv 파일로 변환하기'
date: 2021-01-27 15:44:00 +0900
categories: Linux
tags:
  - linux
  - macos
  - gnumeric
  - xlsx
  - csv
---

gnumeric 설치
### LINUX
```
sudo apt-get install -y gnumeric
```

### MACOS
```
brew install gnumeric
```

### Convert XLSX to CSV
```
/bin/bash
ssconvert xslx.xslx xslx.csv
```