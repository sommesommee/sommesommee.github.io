---
layout: post
title: "MAC M3 에서 Ruby & Jekyll 설치"
author: "Sommesommee"
tags: mac
comments: false
excerpt_separator: <!--more-->
sticky: false
hidden: false
---

> 이 글은 저의 개인 Mac Pro M3 환경 상에서 ruby 를 설치하는 과정을 남긴 포스팅 입니다.
>
> 처음 Mac 환경에 Ruby 를 설치하게 된 계기는 Jekyll 을 사용한 Github 블로그를 운영하기 위함이였습니다.
>
> Jekyll은 Markdown, HTML, CSS 및 Liquid 템플릿 엔진과 같은 간단한 마크업 언어를 사용하여 웹사이트를 만들 수 있게 해줍니다.
>
> Jekyll 은 Ruby 언어를 기반으로 개발된 오픈소스 프로젝트이기하 때문에 Jekyll 을 사용하려면 반드시 Ruby 의 사전 설치가 필요합니다.
>
> 처음엔 단순히 "Homebrew 로 간단히 설치해야지" 라고 생각했었는데 생각보다 그 셋팅 과정이 쉽진 않았습니다.
>
> 때문에 제가 설치해온 경험을 아래와 같이 포스팅하여 작성하오니 누군가에겐 이 글이 도움이 되었으면 합니다.

<br/>

<br/>

<!--more-->

## 첫번째 시도 : Homebrew 를 사용하여 Ruby & Jekyll 설치

Homebrew 를 활용하여 Ruby 의 설치는 매우 쉽게 가능 합니다.

Homebrew 를 통한 Ruby 설치를 위해 아래와 같이 명령어를 입력 하였습니다.

```bash
## 이 명령은 최신 버전의 Ruby를 설치합니다. 

brew install ruby
```

성공적으로 루비가 설치되고 나는 설치된 Ruby 와 함께 설치된 RubyGems라는 Ruby 패키지 관리 시스템을 활용하여 jekyll & bundler 설치를 시도하였습니다.

Jekyll은 Ruby 기반으로 Markdown이나 HTML과 같은 간단한 마크업 언어로 작성된 내용을 사용하여 웹사이트를 생성할 수 있습니다.

bundler는 Ruby 프로젝트에서 필요한 모든 gem을 관리하고 프로젝트에 필요한 정확한 버전의 gem을 설치하는 데 사용됩니다.

```bash
## 이 명령은 최신 버전의 jekyll를 설치합니다.

gem install jekyll

## 이 명령은 최신 버전의 bundler를 설치합니다.
gem install bundler
```

하지만 위 과정에서 정상적으로 설치가 되지 않았습니다.

설치되지 않은 사유는 현재 포스팅을 작성하기 전 남기지 못하였으나 아래 에러메세지와 같은 openssl 관련 패키지 이슈였습니다.

```bash
% gem install jekyll bundler
ERROR:  While executing gem ... (Gem::Exception)
    OpenSSL is not available. Install OpenSSL and rebuild Ruby or use non-HTTPS sources (Gem::Exception)
	/opt/homebrew/Cell
	
	...(이하 생략)...
```

저는 위 문제를 해결하기 위해 여러차례 시행착오를 거쳤습니다.

구글링  시도 중 rvm 을 활용해 설치하는 방법을 찾았고 이 글을 참고하여 Homebrew 가 아닌 RVM 을 활용한 Ruby 설치를 시도하기로 결정하였습니다.

참조 링크 : [https://github.com/rvm/rvm/issues/5365](https://github.com/rvm/rvm/issues/5365)

<br/>

<br/>

## 두번째 시도 : RVM (Ruby Version Manager) 을 사용하여 Ruby 설치

아래 명령어를 실행하여 RVM을 설치합니다.

```
\curl -sSL https://get.rvm.io | bash -s stable
```

설치가 완료되면 RVM을 초기화해야 합니다.

아래 명령어를 실행하여 터미널에서 RVM을 사용할 수 있도록 환경을 설정합니다.

```
source ~/.rvm/scripts/rvm
```

이제 RVM이 성공적으로 설치되었고 활성화되었습니다.

터미널을 다시 시작하여 RVM이 제대로 작동하는지 확인할 수 있습니다.

아래 명령어를 실행하여 RVM이 제대로 설치되었는지 확인합니다.

```bash
rvm --version
```

rvm이 설치가 완료되면 아래와 같은 명령어를 통해 Ruby 3.2.2 버전의 설치가 가능합니다.

```bash
rvm reinstall 3.2.2 --with-openssl-dir=$(brew --prefix openssl) --with-readline-dir=$(brew --prefix readline) --with-libyaml-dir=$(brew --prefix libyaml) --disable-dtrace --disable-docs
```

Ruby 설치가 완료되면 아래와 같은 명령어를 통해 루비가 정상적으로 설치되었는지 확인 가능합니다.

```bash
ruby --version
```

이제 다시 jekyll & bundle 설치를 시도합니다.

```bash
## 이 명령은 최신 버전의 jekyll를 설치합니다.

gem install jekyll

## 이 명령은 최신 버전의 bundler를 설치합니다.
gem install bundler
```

설치가 완료된 후 이제 정상적으로  jekyll 사용이 가능합니다.

```bash
## Bundler를 사용하여 프로젝트의 종속성을 관리하고, 이 종속성에 포함된 Jekyll을 실행하여 로컬 웹서버를 시작하는 명령입니다.

bundle exec jekyll serve
```

<br/>

<br/>

<br/>

<br/>

## 맺음말

그동안 윈도우 기반으로 주로 개발을 진행하였다보니 처음 Mac 에 Java 를 설치할 때 헤맨 기억처럼 Ruby 를 설치하는 과정 또한 쉽지 않았습니다.

위 포스팅과 같이 해결은 하였지만 명확하게 왜 해결되었다에 대해선 명쾌하게 해석되진 않으나 유추해볼만한 것은 `--with-openssl-dir=$(brew --prefix openssl)` 와 같이 "Ruby 패키지 빌드 시 올바른 라이브러리를 참조하도록 옵션을 설정했기 때문이 아닐까"가 저의 예상이며, 이 부분은 좀 더 검증해볼 필요가 있습니다.

위 시행착오 과정에서 제가 경험했던 과정을 누군가는 좀더 수월하게 경험하였으면 합니다.
