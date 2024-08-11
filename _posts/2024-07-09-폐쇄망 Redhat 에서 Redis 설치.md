---
layout: post
title: "폐쇄망 Redhat 에서 Redis 설치"
author: "Sommes
ommee"
tags: devops
comments: false
excerpt_separator: <!--more-->
sticky: false
hidden: false

---

> 이 글은 Redhat OS 환경 상에서  패키지 관리자를 사용하지 않고 Redis 를 설치하는 과정을 남긴 포스팅 입니다.
>

<br/>

<br/>

<!--more-->

## Redis 다운로드 및 내부망 이관

이 포스팅은 이전 Nginx 설치와 동일하게 패키지 관리자를 활용한 설치가 불가능한 폐쇄망 환경에서 진행하였습니다.

저는 인터넷망에 접근 가능한 로컬 PC 에서 구글링 통해 아래 Redis 공식 홈페이지에서 제공하는 다운로드 링크에 접근하였고 설치할 OS와 호환되는 Redis 5.0.9 을 다운 받았습니다.

[http://download.redis.io/releases](http://download.redis.io/releases)

제 로컬 PC에서 다운받은 파일명은 `redis-5.0.9.tar.gz` 입니다.

![image_20240709_001.png](..%2F_images%2Fimage_20240709_001.png)

이제 이 파일을 인터넷망에서 폐쇄망으로 옮길 수 있는 사내 정실 절차를 밟아 폐쇄망으로 접근 가능한 로컬 PC 로 이관해 줍니다.

다음으로 폐쇄망에 접근 가능한 로컬 PC 에서 다운받은 파일을 설치가 필요한 서버에 SFTP 로 전송 합니다.

마지막으로 다운받은 파일의 압축을 해제해 주면 아래와 같이 `redis-5.0.9` 디렉토리가 새로 생성 됩니다.

```bash
$ tar -xvf path/to/redis-5.0.9.tar.gz
$ ls
redis-5.0.9  redis-5.0.9.tar.gz
```

![image_20240709_002.png](..%2F_images%2Fimage_20240709_002.png)

<br/>
<br/>

## Redis 설치 ( configure & make & install )

이제 다운로드 후 내부망으로 이관한 Redis 를 빌드하고 설치해 보도록 하겠습니다.

먼저 make 명령어를 사용하여 Redis 를 빌드합니다.

```bash
$ cd path/to/redis-5.0.9
$ make
( make 진행중... )
```



빌드가 완료되면 make install 통해 설치를 진행합니다.

이때 configure 명령어를 사용하여 빌드 옵션을 설정합니다.

저는 /websvc/build_temp 경로에 Redis 를 설치할 것이기 때문에 —PREFIX ${설치경로} 옵션을 사용하여 설치 경로를 지정하겠습니다.

이를 통해 시스템 전역에 영향을 미치지 않고 특정 디렉토리에 소프트웨어를 설치할 수 있습니다.

<br/>
<br/>
**Note:**
아래와 같은 방법은 항상 적용되진 않습니다. 프로젝트에 따라 어떠한 경우에는 make 단계 전 `./configure --add-module=/path/to/... --prefix=/path/to/install)`  와 같은 방식으로 설치 경로를 지정 합니다. 이는 빌드하고자 하는 프로젝트의 빌드 세스템마다 다릅니다.
<br/>
<br/>

```bash
$ make -PREFIX=/websvc/redis-5.0.9 install
( make install 진행중... )
```

![image_20240709_001.png](..%2F_images%2Fimage_20240709_003.png)

이제 PREFIX 로 지정한 프로젝트 설치경로로 이동해보면 설치가 완료된 파일이 확인됩니다.

![image_20240709_001.png](..%2F_images%2Fimage_20240709_004.png)

최종적으로 prefix 옵션으로 지정한 경로에  Redis 의 홈폴더가 구성되었음을 확인할 수 있습니다.

<br/>
<br/>

## Redis 의 실행

이제 설치된 Redis 를 실행해 보겠습니다.

참고로 Redis 의 기본 포트는 `6379` 입니다. 따라서 해당 포트와 겹치는 서비스가 실행하려고하는 서버 또는 로컬 PC 상에 존재하지 않아야 합니다.

//실행 후 ps -ef|grep 한 화면...

이후 포스팅에서는 Redis.conf 중심으로 주요한 설정을 편집해보겠습니다.

<br/>
<br/>
