---
layout: post
title: "MAC M3 에서 Ruby & Jekyll 설치"
author: "Sommesommee"
tags: devops mac
comments: false
excerpt_separator: <!--more-->
sticky: false
hidden: false
---

> 이 글은 저의 개인 Mac M3 환경 상에서 ruby 를 설치하는 과정을 남긴 포스팅 입니다.
>

<br/>

<br/>

<!--more-->

## 첫번째 시도 : Homebrew 를 사용하여 Ruby & Jekyll 설치

Homebrew 를 활용하여 Ruby 의 설치는 매우 쉽게 가능 합니다.

Homebrew 를 통한 Ruby 설치를 위해 아래와 같이 명령어를 입력 하였습니다.

<br/>

```bash
## 이 명령은 최신 버전의 Ruby 를 설치합니다. 

brew install ruby
```

<br/>

성공적으로 루비가 설치되고 나는 설치된 Ruby 와 함께 설치된 RubyGems 라는 Ruby 패키지 관리 시스템을 활용하여 jekyll & bundler 설치를 시도하였습니다.

Jekyll은 Ruby 기반으로 Markdown 이나 HTML 과 같은 간단한 마크업 언어로 작성된 내용을 사용하여 웹사이트를 생성할 수 있습니다.

bundler는 Ruby 프로젝트에서 필요한 모든 gem 을 관리하고 프로젝트에 필요한 정확한 버전의 gem 을 설치하는 데 사용됩니다.

<br/>

```bash
## 이 명령은 최신 버전의 jekyll 와 bundler 을 설치합니다.

gem install jekyll bundler
```

<br/>

하지만 위 과정에서 정상적으로 설치가 되지 않았습니다.

설치되지 않은 사유는  아래 에러 메세지와 같은 openssl 관련 패키지 이슈 였습니다.

<br/>

```bash
% gem install jekyll bundler
ERROR:  While executing gem ... (Gem::Exception)
    OpenSSL is not available. Install OpenSSL and rebuild Ruby or use non-HTTPS sources (Gem::Exception)
	/opt/homebrew/Cell
	
	...(이하 생략)...
```

아무래도 Ruby 를 Homebrew 로 설치하였을 때 Ruby 의 컴파일 과정 중 알 수 없는 이유에 의해 시스템 환경설정이나 Ruby 빌드 구성 등의 문제로 정상적으로 Ruby 가 빌드되지 않은 듯 합니다.

<br/>

<br/>

## 두번째 시도 : RVM (Ruby Version Manager) 을 사용하여 Ruby 설치

저는 위 문제를 해결하기 위해 구글링  시도 중 RVM 을 활용해 설치하는 방법을 찾았고 아래 링크의 글을 참고하여 Homebrew 가 아닌 RVM 을 활용한 Ruby 설치를 시도하기로 결정 하였습니다.

[https://github.com/rvm/rvm/issues/5365](https://github.com/rvm/rvm/issues/5365)

아래 명령어를 실행하여 RVM 을 설치합니다.

<br/>

```
\curl -sSL https://get.rvm.io | bash -s stable
```

<br/>

설치가 완료되면 RVM 을 초기화해야 합니다.

아래 명령어를 실행하여 터미널에서 RVM 을 사용할 수 있도록 환경을 설정합니다.

<br/>

```
source ~/.rvm/scripts/rvm
```

<br/>

이제 RVM 이 성공적으로 설치되었고 활성화되었습니다.

터미널을 다시 시작하여 RVM 이 제대로 작동하는지 확인할 수 있습니다.

아래 명령어를 실행하여 RVM 이 제대로 설치되었는지 확인합니다.

<br/>

```bash
rvm --version
```

<br/>

RVM 이 설치가 완료되면 위 Github 에서 소개한 아래와 같은 명령어를 통해 Ruby 3.2.2 버전의 설치가 가능합니다.

<br/>

```bash
rvm reinstall 3.2.2 --with-openssl-dir=$(brew --prefix openssl) --with-readline-dir=$(brew --prefix readline) --with-libyaml-dir=$(brew --prefix libyaml) --disable-dtrace --disable-docs
```

Ruby 설치가 완료되면 아래와 같은 명령어를 통해 Ruby 가 정상적으로 설치되었는지 확인 가능합니다.

<br/>

```bash
ruby --version
```

<br/>

이제 다시 jekyll & bundle 설치를 시도합니다.

<br/>

```bash
## 이 명령은 최신 버전의 jekyll 와 bundler 을 설치합니다.

gem install jekyll bundler
```

<br/>

설치가 완료된 후 이제 정상적으로  jekyll 사용이 가능합니다.

<br/>

```bash
## Bundler를 사용하여 프로젝트의 종속성을 관리하고, 이 종속성에 포함된 Jekyll 을 실행하여 로컬 웹서버를 시작하는 명령입니다.

bundle exec jekyll serve
```

<br/>

두번째 방법과 첫번째 방법을 비교해본다면 아마도 Ruby 의 빌드 과정 중 필요한 기타 패키지의 경로를 참조하지 못하지 않았을까 싶습니다.

관련하여 구글링을 좀더 진행해보았고 아래 포스팅과 같이 저와 유사한 방식으로 해결한 사례를 찾아볼 수 있었습니다.

<br/>

https://leesh90.github.io/environment/2021/04/03/openssl-install/

<br/>

<br/>

<br/>

<br/>

## 맺음말

위 포스팅 내용과 같이 저는 RVM 통하여 Jekyll 설치 이슈를 해결 하였지만 Homebrew 는 안되고 RVM 은 된다의 문제는 아닐 것입니다.

아마도  `--with-openssl-dir=$(brew --prefix openssl)` 와 같은 설정을 통해 "Ruby 컴파일 시 올바른 라이브러리를 참조하도록 옵션을 설정했기 때문이 아닐까"가 저의 예상 입니다.

Jekell 공식 홈페이지에서는 아래 링크와 같이 Chruby 를 통한 Ruby 와 Jekyll 의 설치를 권장하는 만큼 왠만하면 저처럼 버전관리자를 활용하여 설치하시는 것을 가급적이면 추천 드립니다.

[https://jekyllrb.com/docs/installation/macos/](https://jekyllrb.com/docs/installation/macos/)

그리고 아직 Homebrew 에 대한 이해도가 낮은 만큼 좀 더 이 부분에 대해 경험해보며 무엇이 문제인지 고민해보도록 하겠으며, 새로 이해한 내용이 있다면 이 글을 업데이트하거나 연재글로 링크를 걸 수 있도록 하겠습니다.

그리고 위 시행착오 과정에서 제가 경험했던 과정을 누군가는 좀더 수월하게 경험하였으면 합니다.
