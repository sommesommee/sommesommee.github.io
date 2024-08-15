---
layout: post
title: "폐쇄망 Redhat 에서 Nginx 설치"
author: "Sommesommee"
tags: devops
comments: false
excerpt_separator: <!--more-->
sticky: false
hidden: false

---

> 이 글은 Redhat OS 환경 상에서  패키지 관리자를 사용하지 않고 Nginx 를 설치하는 과정을 남긴 포스팅 입니다.
>

<br/>

<br/>

<!--more-->

## Nginx 다운로드

폐쇄망 환경상에서는 일반적인 인터넷망과 달리 패키지 관리자를 활용한 설치는 진행할 수 없고 wget 통한 다운로드도 불가능 합니다.

저는 인터넷망에 접근 가능한 로컬 PC 에서 구글링 통해 아래 Nginx 공식 홈페이지에서 제공하는 다운로드 링크에 접근하였고 현 시점에서 최신 Stable version 인 v1.24.0 을 다운 받았습니다.

[https://nginx.org/en/download.htmll](https://nginx.org/en/download.html)

제 로컬 PC 에서 다운받은 파일명은 `nginx-1.24.0.tar` 입니다.

이제 이 파일을 인터넷망에서 폐쇄망으로 옮길 수 있는 사내 정식 절차를 통해 폐쇄망으로 이관해 줍니다.

저의 경우 망분리 솔루션을 통한 승인 절차 통해 다운받은 파일을 폐쇄망에 접근 가능한 로컬 PC 로 이관 하였습니다.

다음으로 폐쇄망에 넙근 가능한 로컬 PC 에서  다운받은 파일을 설치가 필요한 서버에 SFTP (저는 Filezilla 를 주로 사용합니다.) 로 전송 하였습니다.

마지막으로 다운받은 파일의 압축을 해제해 줍니다.

<br/>

```bash
$ tar -xvf path/to/nginx-1.24.0.tar
$ ls
nginx-1.24.0  nginx-1.24.0.tar.gz
```

<br/>

<br/>

## 서드 파티 모듈 headers-more-nginx-module 다운로드

위와 동일한 방식으로 저는 추가 서드 파티 모듈도 다운받아 보고자 합니다.

제가 다운받고자 하는 추가 서드 파티 모듈은 headers-more-nginx-module 입니다.

"headers-more-nginx-module"을 사용하면 Nginx 의 설정을 통해 서버 정보나 서버 버전 정보를 응답 헤더에서 제거하거나 수정할 수 있습니다. 이를 통해 공격자에게 서버 정보를 노출하지 않고 보다 안전한 서버 운영이 가능합니다.

해당 모듈의 공식 github 과 버전별 다운로드 링크는 아래와 같으며, 현 시점에서 최신 버전인 v0.37 을 다운 받았습니다.

[https://github.com/openresty/headers-more-nginx-module](https://github.com/openresty/headers-more-nginx-module)

[https://github.com/openresty/headers-more-nginx-module/tags](https://github.com/openresty/headers-more-nginx-module/tags)

제 로컬 PC 에서 다운받은 파일명은 `headers-more-nginx-module-0.37.tar` 입니다.

다시 동일한 과정을 거쳐 압축 해제까지 진행해 줍니다.

<br/>

 ```bash
 $ tar -xvf path/to/headers-more-nginx-module-0.37.tar
 $ ls
 headers-more-nginx-module-0.37  headers-more-nginx-module-0.37.tar  nginx-1.24.0  nginx-1.24.0.tar.gz
 ```

<br/>

<br/>

## Nginx 의 설치 ( configure & make & install )

이제 Nginx 를 빌드하고 설치해 보도록 하겠습니다.

Nginx 를 빌드하기 전에 configure 명령어를 사용하여 빌드 옵션을 설정합니다. 

이때 --add-module 옵션을 사용하여 headers-more-nginx-module 의 경로를 지정해주어야 합니다.

또한 저는 /path/to 경로에 Nginx 를 설치할 것이기 때문에 —prefix ${설치경로} 옵션을 사용하여 설치 경로를 지정하겠습니다.

이를 통해 시스템 전역에 영향을 미치지 않고 특정 디렉토리에 소프트웨어를 설치할 수 있습니다.

<br/>

```bash
$ cd path/to/nginx-1.24.0
$ ./configure --add-module=/path/to/headers-more-nginx-module-0.37 --prefix=/path/to/install
```

<br/>

다음으로 configure 명령어를 실행한 후에는 make 명령어를 사용하여 Nginx 를 빌드합니다.

<br/>

```
make
```

<br/>

마지막으로 make install 명령어를 사용하여 빌드된 Nginx 를 시스템에 설치합니다.

```bash
$ make install
$ cd /path/to/install
$ ls
conf  html  logs  sbin
```

최종적으로 prefix 옵션으로 지정한 경로에  Nginx 의 홈폴더가 구성되었음을 확인할 수 있습니다.

<br/>

<br/>

## Nginx 의 설정 및 실행

이제 Nginx 설치 후 존재하는 기본 nginx.conf 설정에서  headers-more-nginx-module  의 server 정보 비노출 설정만 추가해 준 후 실행하여 정상적으로 서드 파티 모듈 기반으로 싱행이 되는지 확인해 보겠습니다.

우선 nginx.conf 설정 파일 위치를 탐색해 봅니다.

<br/>

```bash
$ cd /path/to/install/conf
$ ls
fastcgi.conf          fastcgi_params.default  mime.types          nginx.conf.default   uwsgi_params
fastcgi.conf.default  koi-utf                 mime.types.default  scgi_params          uwsgi_params.default
fastcgi_params        koi-win                 nginx.conf          scgi_params.default  win-utf
```

<br/>

이제 nginx.conf 설정 파일 상에 headers-more-nginx-module 관련 설정을 추가하여 서버 정보를 응답 헤더상에서 제외해 보겠습니다.

<br/>

```bash
...
http {
		...
    more_set_headers 'Server: None';
    ...
    server {
    	listen	8080;
    	...
    }
}
...
```

<br/>

이제 서비스를 실행해보겠습니다.

<br/>

```bash
$ cd /path/to/install/bin
$ ./nginx
```

마지막으로 정상적으로 실행되었는지 확인하려면 브라우저 상에서  `http://(대상 서버 IP):8080` 으로 이동하여 index.html 인 Welcome to nginx! 가 보이는지 확인해 봅니다.

그리고 응답 header 상에서 서버 정보가 위에서 설정한 None 으로 표시되고 있는지 확인해 봅니다.

![대체 텍스트](https://github.com/sommesommee/sommesommee.github.io/blob/master/_images/image_20240330.png?raw=true)

<br/>

<br/>

<br/>

<br/>
