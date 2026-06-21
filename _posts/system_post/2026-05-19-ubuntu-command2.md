---
layout: post
title: "0. Ubuntu Command - 패키지, 디스크, 포트"
categories: programming
tags: Ubuntu
---

# 패키지

- Linux의 다양한 배포판 중 하나
- CentOS나 Fedora과 같은 RedHat계열 배포판은 RPM이라는 패키징 시스템을 사용함
- ubuntu와 같이 데비안 계열 배포판은 dev라는 패키징 시스템을 사용함
- 패키지와 패키지 정보를 저장하고 있는 패키지 저장소라는 개념을 가지고 있음
- 소프트웨어 패치, 추가등 정보를 관리
- 우분투 사용자가 패키지 관리자를 통해 패키지 저장소에 접근하면, 소프트웨어 변경사항을 알려주고 업데이트, 다운로드등을 지원

- ubuntu 패키지 인덱스 정보 업데이트(배포판 버전에 따른 패키지 업데이트 버전 정보 등)
  - `sudo apt-get update`
- 설치된 ubuntu 패키지 업그레이드(**함부로 사용 X**)
  - `sudo apt-get upgrade`
- 패키지 설치
  - `sudo apt-get install 패키지명`
- 패키지 삭제
  - `sudo apt-get remove 패키지명`
- 패키지 삭제 설정파일 포함
  - `sudo apt-get --purge remove 패키지명`

# 디스크

## 용량 확인

```
 # 전체 디스크 용량
  df -h

  # 현재 폴더 용량
  du -sh ~/Documents/food_data/

  # 폴더 안 파일별 용량
  du -sh ~/Documents/food_data/*
```

# 포트

특정 포트를 점유 하고 있는지 확인하는 명령어

```
sudo lsof -i :80
```
