---
layout: post
title: "Redis 의 설정 파일 redis.conf 주요 옵션 소개"
author: "Sommesommee"
tags: devops redis
comments: false
excerpt_separator: <!--more-->
sticky: false
hidden: false

---

> redis.conf 는 Redis 서버의 동작 방식을 구성하는 설정 파일입니다. 
>
> 이 파일은 Redis 서버가 시작될 때 읽혀져 다양한 설정 옵션을 통해 Redis의 동작을 제어합니다. 
>
> 아래 포스팅을 통해 제가 사용한 redis.conf 파일의 주요 옵션을 소개하고자 합니다.

<br/>

<br/>

<!--more-->

## redis.conf 파일 템플릿 획득

redis.conf 파일의 편집은 현재 사용중인 redis 버전에 맞는 redis.conf 파일의 템플릿 파일을 확보하고 해당 템플릿 파일을 통해 커스텀을 진행하는 것을 추천 드립니다.

템플릿상엔 이미 모든 설정과 그 설정과 관련된 설명이 주석으로 매우 잘 남아있다보니 주석 내용과 구글링 또는 생산형 ai 의 답변을 비교 검증하며 redis.conf 파일의 편집을 좀더 편하게 진행할 수 있습니다.

redis.conf 파일의 다운로드는 아래 링크 통해 버전에 맞는 redis 패키지 압축파일을 다운받은 후 압축을 풀면 존재합니다.

제 로컬 PC에서 다운받은 파일명은 `redis-5.0.9.tar.gz` 입니다.

<br/>

[http://download.redis.io/releases](http://download.redis.io/releases)

<br/>

<br/>

## redis.conf 파일을 적용하는 법

 상세한 설정을 소개하기 전 redis.conf 파일을 설정을 적용하려 redis 를 실행하는 방법부터 소개하겠습니다.

Redis 서버를 `redis.conf` 파일과 함께 실행하려면 redis 실행 시 ./redis-server 실행과 함께 redis.conf 의 경로를 명시적으로 지정해주어야 합니다.

<br/>

```bash
$ /path/to/redis-server /path/to/redis.conf
( redis 실행 진행중... )
```

<br/>

이제 redis.conf 파일을 적용하는 법을 설명해드렸으니 다음으로 redis.conf 에서 제가 사용한 주요 설정을 소개하겠습니다.

(참고로 저의 경우 단순 캐싱 용으로만 사용할 예정이다보니 `appendonly` 와 같은 백업 관련 설정은 사용하지 않았습니다.)

<br/>

<br/>

## daemonize  : 백그라운드 실행 여부 설정

redis.conf 파일의 `daemonize` 설정은 Redis 서버가 데몬(백그라운드) 모드로 실행될지 여부를 결정하는 중요한 옵션입니다.

`daemonize yes`로 설정하면, Redis 서버는 백그라운드에서 실행됩니다. 이 때 Redis는 시스템의 다른 작업과 독립적으로 실행되고 터미널 세션에 종속되지 않습니다. 즉, 서버가 시작된 후 터미널을 닫아도 Redis는 계속 실행됩니다. 

`daemonize no`로 설정하면, Redis 서버는 포그라운드에서 실행됩니다. 이 때 Redis는 현재 터미널 세션에서 실행되고, 터미널에 로그 메시지를 출력합니다. 이 모드에서는 터미널을 닫거나 `Ctrl+C`를 눌러 세션을 종료하면 Redis 서버도 종료됩니다.

위 설정은 redis.conf 파일 상 아래와 같이 존재합니다.

<br/>

![image_20240815_001.png](https://raw.githubusercontent.com/sommesommee/sommesommee.github.io/master/_images/image_20240815_001.png)

<br/>

<br/>

## logfile : 로그 경로 설정

redis.conf 파일의 `logfile` 설정은 Redis 서버의로그를 기록할 파일 경로를 설정합니다.

아쉽게도 날짜별로 로그파일을 쪼개는 등의 롤링 기능은 제공하지 않습니다.

위 설정은 redis.conf 파일 상 아래와 같이 존재합니다. (아래와 같이 빈 경로인 경우 로그가 남지 않습니다.)

<br/>

![image_20240815_010.png](https://raw.githubusercontent.com/sommesommee/sommesommee.github.io/master/_images/image_20240815_010.png)

<br/>

<br/>

## loglevel : 로그 레벨 설정

redis.conf 파일의 `loglevel` 설정은 Redis 서버의 로그 레벨을 설정합니다.

설정할 수 있는 로그 레벨 옵션은 아래와 같습니다.

`debug` 는 디버그 정보를 포함한 가장 디테일한 로그를 남깁니다.

`verbose` 는 기본 로그 수준과 더불어 더 디테일한 로그를 남깁니다.

`notice` 는 기본 로그 수준의 로그만 남깁니다.

`warning` 은 오류 및 경고 수준의 로그만 남깁니다.

위 설정은 redis.conf 파일 상 아래와 같이 존재합니다.

<br/>

![image_20240815_011.png](https://raw.githubusercontent.com/sommesommee/sommesommee.github.io/master/_images/image_20240815_011.png)

<br/>

<br/>

## tcp-backlog : 대기 연결 요청 최대 수 설정

redis.conf 파일의 `tcp-backlog` 설정은 Redis 서버가 수용할 수 있는 대기 중인 연결 요청의 최대 수를 설정합니다.

 위 설정은 redis.conf 파일 상 아래와 같이 존재합니다.

<br/>

![image_20240815_012.png](https://raw.githubusercontent.com/sommesommee/sommesommee.github.io/master/_images/image_20240815_012.png)

<br/>

<br/>

## timeout : 클라이언트 타임아웃 설정

redis.conf 파일의 `timeout` 설정은 Redis 서버가 지정된 시간이 지나도록 클라이언트가 아무 작업을 하지 않으면 해당 연결을 끊을 수 있도록 설정합니다.

 위 설정은 redis.conf 파일 상 아래와 같이 존재합니다.

<br/>

![image_20240815_013.png](https://raw.githubusercontent.com/sommesommee/sommesommee.github.io/master/_images/image_20240815_013.png)

<br/>

<br/>

## port : 네트워크 서비스 포트 설정

redis.conf 파일의 `port` 설정은 Redis 서버가 클라이언트 연결을 수신할 네트워크 포트를 지정하는 옵션입니다. 이 포트 설정을 통해 클라이언트가 Redis 서버와 통신할 수 있는 네트워크 포트를 지정할 수 있습니다.

Redis 는 기본적으로 `6379` 포트를 사용합니다. 만약 포트 충돌 등의 이유로 기본 포트를 변경해야 한다면 redis.conf 파일의 `port` 설정 변경을 통해 진행 가능합니다.

위 설정은 redis.conf 파일 상 아래와 같이 존재합니다.

<br/>

![image_20240815_002.png](https://raw.githubusercontent.com/sommesommee/sommesommee.github.io/master/_images/image_20240815_002.png)

<br/>

<br/>

## protected-mode : 외부 네트워크 접근 여부 설정

redis-conf 파일의 `protected-mode` 설정은 Redis 서버의 외부 네트워크 접근 여부를 설정할 수 있습니다.

Yes 로 설정하게되면 Redis는 `requirepass` 또는 `bind` 옵션에 따라 외부 네트워크 접근 시 인증을 요구하거나 바인딩된 IP만 접근을 허용합니다.

위 설정은 redis.conf 파일 상 아래와 같이 존재합니다.

<br/>

![image_20240815_008.png](https://raw.githubusercontent.com/sommesommee/sommesommee.github.io/master/_images/image_20240815_008.png)

<br/>

<br/>

## requirepass : 인증 패스워드 설정

redis-conf 파일의 `requirepass`설정은 Redis 서버의 외부 네트워크 접근 시 패스워드 인증 여부를 설정할 수 있습니다.

패스워드 인증까지 굳이 필요하지 않다면 주석처리하여도 무방합니다.

위 설정은 redis.conf 파일 상 아래와 같이 존재합니다.

<br/>

![image_20240815_009.png](https://raw.githubusercontent.com/sommesommee/sommesommee.github.io/master/_images/image_20240815_009.png)

<br/>

<br/>

## bind : 허용 IP 설정

redis.conf 파일의 `bind` 설정은 Redis 서버가 어떤 IP 주소의 접속을 허용할지를 설정할 수 있습니다.

만약 `127.0.0.1` 로 설정하였다면 외부에서의 접근이 불가능하고 로컬에서의 접근만 가능합니다.

이 설정은 `protected-mode` 와 관계없이 적용 됩니다.

위 설정은 redis.conf 파일 상 아래와 같이 존재합니다.

<br/>

![image_20240815_007.png](https://raw.githubusercontent.com/sommesommee/sommesommee.github.io/master/_images/image_20240815_007.png)

<br/>

<br/>

## maxmemory : 최대 메모리 용량 설정

redis.conf 파일의 `maxmemory` 설정은 Redis 서버가 사용할 수 있는 최대 메모리 용량을 지정하는 옵션입니다. Redis를 실행하는 서버의 메모리 상태에 따라 적절한 설정이 필요합니다.

참고로 Redis 서버가 이 한도를 초과하게 되면 `maxmemory-policy`  설정에 따라 새로운 데이터를 저장하지 못하게 되거나 설정된 메모리 정책에 따라 기존 데이터를 삭제하는 방식으로 메모리 관리를 하게 됩니다.

만약 maxmemory 를 설정하지 않는다면 Redis 서버의 메모리 제한은 없어지게 되며, 물리적인 메모리가 허용하는 한도 내에서 계속 메모리를 사용할 수 있게 됩니다.

위 설정은 redis.conf 파일 상 아래와 같이 존재합니다. (byte 단위가 기본값 이지만 KB, MBm GB 와 같은 단위 설정도 지원합니다.)

<br/>

![image_20240815_003.png](https://raw.githubusercontent.com/sommesommee/sommesommee.github.io/master/_images/image_20240815_003.png)

<br/>

<br/>

## maxmemory-policy : 최대 메모리 도달 시 정책 설정

redis.conf 파일의 `maxmemory-policy` 설정은 Redis 서버가 설정된 메모리 옵션인 `maxmemory` 한도에 도달하였을 때 어떤 방식으로 메모리를 관리할지 결정하는 중요한 옵션입니다.

정책유형은 아래와 같습니다.

`noevication` 는 메모리 한도에 도달하면 모든 쓰기 요청은 거부합니다. 읽기 요청은 계속 처리 됩니다.

`allkeys-lr` 는 메모리 한도에 도달하면 모든 키를 대상으로 가장 오랫동한 사용되지 않은 키를 삭제합니다.

`volatile-lru` 는 메모리 한도에 도달하면 TTL(Time-To-Live) 설정된 키들 중 가장 오랫동안 사용되지 않은 키를 삭제합니다.

`allkeys-random` 는 메모리 한도에 도달하면 모든 키들 중에서 무작위로 선택된 키를 삭제합니다.

`volatile-random` 는 메모리 한도에 도달하면 TTL(Time-To-Live) 설정된 키들 중 무작위로 선택된 키를 삭제합니다.

`volatile-ttl` 는 TTL(Time-To-Live) 설정된 키들 중 TTL이 가장 적게 남은 키를 우선적으로 삭제합니다.

`volatile-lfu` 는 TTL(Time-To-Live) 설정된 키들 중 사용 빈도가 가장 낮은 키를 삭제합니다.

`allkeys-lfu` 모든 키들 중에서 사용 빈도가 가장 낮은 키를 삭제합니다.

위 설정은 redis.conf 파일 상 아래와 같이 존재합니다. (저의 경우 단순 캐싱 목적으로 Redis 서버를 사용할 예정이므로 `volatile-ttl` 설정을 사용하였습니다.)

<br/>

![image_20240815_004.png](https://raw.githubusercontent.com/sommesommee/sommesommee.github.io/master/_images/image_20240815_004.png)

<br/>

<br/>

## cluster-enabled : 클러스터 모드 활성화 여부 설정

Redis 서버는 고가용성과 확장성을 위해 클러스터 모드를 제공하고 있습니다. Redis 서버의 클러스터 모드는 데이터를 여러 Redis 노드에 분산하여 저장하고 노드 간에 자동으로 데이터 복제를 관리하는 기능을 제공합니다.

redis.conf 파일의 `cluster-enabled` 설정은 클러스터 모드 활성화 여부를 설정합니다. (자세한 클러스터 모드 설정 등은 별도 포스팅 통해 소개하겠습니다.)

위 설정은 redis.conf 파일 상 아래와 같이 존재합니다.

<br/>

![image_20240815_005.png](https://raw.githubusercontent.com/sommesommee/sommesommee.github.io/master/_images/image_20240815_005.png)

<br/>

<br/>

## cluster-node-timeout : 클러스터간 타임아웃 설정

redis.conf 파일의 `cluster-node-timeout` 설정은 클러스터된 노드 간의 통신 및 장애 감지에 사용되는 시간제한을 설정합니다. 이 설정은 클러스터 내에서 특정 노드가 설정된 시간 동안 응답하지 않으면 해당 노드가 다운되었다고 판단하고 복구 절차를 시작하는 기준이 됩니다.

위 설정은 redis.conf 파일 상 아래와 같이 존재합니다. (자세한 클러스터 모드 설정 등은 별도 포스팅 통해 소개하겠습니다.)

<br/>

![image_20240815_006.png](https://raw.githubusercontent.com/sommesommee/sommesommee.github.io/master/_images/image_20240815_006.png)

<br/>

<br/>

<br/>

<br/>

## 맺음말

redis.conf 파일의 주요 옵션들을 포스팅을 작성하며 살펴보았습니다.

이 외에도 많은 설정이 있으며 꼭 사용하지 않더라도 redis.conf 템플릿 파일을 천천히 훑어본다면 Redis가 제공하는 다양할 설정들을 이해하고 넘어가는 것만으로도 언젠가 필요할 때 떠올릴 수 있으니 충분하지 않을까 싶네요.

다음으로는 실제 클러스터 환경을 구성하는 방법에 대해 포스팅을 하고자 합니다.

<br/>
<br/>
