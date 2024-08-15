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

<br/>

[http://download.redis.io/releases](http://download.redis.io/releases)

<br/>

제 로컬 PC에서 다운받은 파일명은 `redis-5.0.9.tar.gz` 입니다.

<br/>

![image_20240709_001.png](https://raw.githubusercontent.com/sommesommee/sommesommee.github.io/master/_images/image_20240709_001.png)

<br/>

이제 이 파일을 인터넷망에서 폐쇄망으로 옮길 수 있는 사내 정실 절차를 밟아 폐쇄망으로 접근 가능한 로컬 PC 로 이관해 준 뒤 다시 설치가 필요한 서버에 SFTP 어플리케이션 또는 SCP 명령어로 전송 합니다.

마지막으로 다운받은 파일의 압축을 해제해 주면 아래와 같이 `redis-5.0.9` 디렉토리가 새로 생성 됩니다.

<br/>

```bash
$ tar -xvf path/to/redis-5.0.9.tar.gz
$ ls
redis-5.0.9  redis-5.0.9.tar.gz
```

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


---

**Note:**
아래와 같은 방법은 항상 적용되진 않습니다. 프로젝트에 따라 어떠한 경우에는 make 단계 전 `./configure --add-module=/path/to/... --prefix=/path/to/install)`  와 같은 방식으로 설치 경로를 지정 합니다. 이는 빌드하고자 하는 프로젝트의 빌드 시스템마다 다릅니다.

---

<br/>


```bash
$ make -PREFIX=/websvc/redis-5.0.9 install
( make install 진행중... )
```

<br/>

이제 PREFIX 로 지정한 프로젝트 설치경로로 이동해보면 설치가 완료된 파일이 확인됩니다.

<br/>

```bash
$ cd /websvc/redis-5.0.9/bin
$ ls
redis-benchmark	redis-check-aof	redis-check-rdb	redis-cli	redis-sentinel	redis-server
```

<br/>

최종적으로 prefix 옵션으로 지정한 경로의 bin 폴더에  Redis 의 홈폴더가 구성되었음을 확인할 수 있습니다.

<br/>
<br/>

## Redis 의 실행

이제 설치된 Redis 를 별도의 설정과정없이 그대로 실행 보겠습니다.

참고로 Redis 의 기본 포트는 `6379` 이므로 기존 서비스 중 6379 포트를 사용하는 서비스가 없어야 합니다.

<br/>

```bash
$ ./redis-server
( redis 실행 진행중... )
```

<br/>

![image_20240709_002.png](https://raw.githubusercontent.com/sommesommee/sommesommee.github.io/master/_images/image_20240709_002.png)

<br/>

위와같이 정상적으로 레디스 실행이 완료된 것을 확인할 수 있습니다.

현재 실행한 Redis는 redis.conf 상 `daemonize` 옵션을 설정하지 않았으므로 기본값인 no 로 실행되어 쉘이 종료되면 Redis 도 종료 됩니다.

<br/>

![image_20240709_003.png](https://raw.githubusercontent.com/sommesommee/sommesommee.github.io/master/_images/image_20240709_003.png)

</br>

이후 포스팅에서는 redis.conf 중심으로 편집하며 제가 사용한 주요 설정을 소개하겠습니다.

<br/>
<br/>
