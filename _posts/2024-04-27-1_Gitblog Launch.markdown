---
layout: post
title:  "깃허브 블로그 개설"
date:   2024-04-27 16:28:30 +0900
categories: github github-blog ssh
---
깃허브(Github) 계정을 만들며 글들을 정리하기 위해 깃허브 블로그(Github blog)를 만들기로 했다.  
그 과정에서 나와 같은 시행착오를 겪을 수 있는 사람들을 위해, 또 언젠가 새로운 계정을 사용할 날을 위해 정리하고자 한다.  

특히 혹시 사용하던 깃허브 계정이 있지만 업무상의 이유로 새로운 계정을 만들어서 *두 개 이상의 계정* 을 사용해야 하게 되는 경우, 겪을 수 있는 어려움에 도움이 되고자 이런 부분을 정리한다.
<br />
# 1. 깃허브 블로그를 위한 Repository 만들기

계정 프로필 > Repositories > New  

*반드시 !* Repository 이름으로 ***본인의 username***.github.io 를 입력한다. _(e.g., itsjulianjeong.github.io)_  

추가로 *Add a README file*도 체크한다.  

정상적으로 생성된 레파지토리를 확인했다면 주소창에 ***username.github.io*** 를 입력하여 들어가서 개설이 됐는지 확인한다.
<br />
# 2. Remote - Local 
## 2.1. git clone  

이제 생성된 레파지토리에서 로컬로 clone한다.

Repositories > ***username.github.io*** > Code 클릭 > HTTPS 주소 복사 > 터미널을 열고 원하는 경로로 이동한 후 아래와 같이 입력한다.

    git clone 복사한 HTTPS 주소

clone을 수행한 후 해당 경로를 찾아가면 1번에서 생성한 README 파일이 정상적으로 생성됐음을 확인 가능하다.  

## 2.2. index.html  
정상적으로 생성됐다면 이제 clone한 폴더로 이동 후 index.html 파일을 생성한다.

    cd username.github.io
    echo "Hello World" > index.html

## 2.3. add, commit, push  
이제 터미널에 다음 명령어들을 입력하여 원격으로 Push 해준다.

    git add -all
    git commit -m "First Commit"
    git push -u origin main

### 하지만 push를 하려는 순간 만약 이전에 사용하던 계정이 있어서 에러가 발생한다면 반드시 거쳐야 하는 선행 과정이 존재한다.  

<!-- 혼동이 올 수 있기에 앞으로 다음과 같이 언급하겠다.
이전 username/이메일

    aaa / aaa@gmail.com 

변경할 username/이메일

    bbb / bbb@gmail.com
-->

혼동이 오지 않도록 이전에 사용하던 계정을 A계정, 변경하려는(새로운) 계정을 B계정 이라고 칭하겠다.

#### 2.3.1. 현재 연결되어있는 계정의 username과 email 확인

    git config user.name
    git config user.email

이전에 사용하던 계정(A)의 username과 email이 나왔다면, 이를 사용하려는 계정(B)의 username과 email로 변경해야 한다.  

#### 2.3.2. 새로운 계정(B)으로 변경

    git config --global user.name 변경할 계정의 이름
    git config --global user.email 변경할 계정의 이메일
    git config --global user.password 변경할 계정의 토큰

변경 되었는지 확인하려면 2.3.1에서의 명령어를 다시 입력하여 확인한다.

#### 2.3.3. ssh key 값 생성
git bash나 터미널을 실행한 후 ssh 폴더로 이동한다.

    cd ~/.ssh
 
 이후 ssh 인증키를 생성한다.

     ssh-keygen -t rsa -C "A계정 이메일" -f "A계정 이름"
이때 원래는 `"A계정 이름"` 부분에 본인이 알아볼 수 있는 id를 사용하면 된다. 하지만 구분하기 용이하게 A계정의 이름을 사용했다.
이후 Enter passphrase 라는 문구가 출력되면 본인이 사용할 비밀번호를 입력하면 된다.

#### 2.3.4. 생성 확인

    ls -al

위 명령어를 입력하여 `A계정 이름`파일과 `A계정 이름.pub`파일이 생성됨을 확인하면 ssh 키가 생성된 것이다.
 *.pub는 공개키를 뜻한다. 붙지 않은 파일은 개인키*

이제 똑같은 방법으로 B계정도 만든다.

#### 2.3.5. ssh-agent
ssh-agent가 암호를 한번 입력하면 기억하여 ssh를 사용할 때 마다 암호를 다시 묻지 않아 번거로움을 줄여준다.

    eval "$(ssh-agent -s)"

#### 2.3.6. 만든 ssh키 추가

    ssh-add ~/.ssh/A계정 이름
    ssh-add ~/.ssh/B계정 이름

#### 2.3.7. github에 ssh를 사용할 수 있도록 ssh key 추가
A 계정 로그인 > Settings > SSH and GPG keys > New SSH key 클릭
Title에는 등록할 ssh 키의 제목 *(e.g. personal key)* 을 입력하고 Key 부분을 앞서 생성한 ssh pub키(공개키)를 복사해야 한다.

    code ~/.ssh/A계정 이름.pub  // 반드시 .pub 붙이기
    
명령어를 입력하면 본인의 경우는 vscode에서 ssh-rsa.pub 전체를 확인할 수 있었다. 이를 복사하여 Key 부분에 붙여 넣어주면 A계정에 ssh keys가 추가된다.

두 번째 계정도 동일하게 진행한다.

#### 2.3.8. 설정을 위한 config 파일 생성

    code config

명령어를 입력하면 다시 vscode가 열리며 config파일에 아래 내용을 작성해야 한다.

    # Account 1 (A계정 이름)
    Host github.com-A계정 이름
	    HostName github.com
	    User git
	    IdentityFile ~/.ssh/A계정 이름
    
    # Account 2 (B계정 이름)
    Host github.com-B계정 이름
	    HostName github.com
	    User git
	    IdentityFile ~/.ssh/B계정 이름

#### 2.3.9. ssh 키 연결 확인

    ssh -T git@github.com-A계정 이름

처음 github에 연결하면 경고문이 나온다면 `yes`를 입력하면 된다.

    Hi A계정 이름! You've successfully authenticated, but Github does not provide shell access.

라는 메세지가 출력되면 연결이 된 것이다.
물론 B계정도 동일하게 진행한다.

#### 2.3.10. ssh로 clone 받기
앞서 HTTPS로 clone을 받았지만 이제는 ssh로 clone을 받아야한다.
Repositories > ***username.github.io*** > Code 클릭 > SSH > 주소 복사
이제 다시 원하는 주소로 이동한 후 git clone을 *형식에 맞춰서* 진행한다.

    git clone git@github.com-계정 이름:계정 이름/계정이름.github.io.git
이렇게 clone까지 완료했으니 다시 2.3.의 과정으로 돌아가면 된다.
<br />
# 3. Blog
## 3.0. Ruby 설치  
본격적으로 github blog를 위해서 [Ruby](https://rubyinstaller.org/downloads/)와 Jekyll이 필요하다.
*(Jekyll을 사용하기 위해서 Ruby가 먼저 필요하다.)*
Ruby는 다운로드 페이지의 우측 **WHICH VERSION TO DOWNLOAD?** 부분의 recommend 하는 버전을 참고하는데, Jekyll이 32bit기 때문에, x64가 아닌 **x86 버전**으로 다운받으면 된다.
Ruby의 설치가 끝났다면

    ruby -v

를 입력해 버전을 확인하면 끝이다.

## 3.1. Jekyll + bundler 설치 
이제 로컬에 Jekyll과 bundler를 설치한다.

    gem install jekyll bundler

## 3.2. Jekyll 사이트 생성 + bundle 설치   
이제 Jekyll을 생성하기 위해서 clone한 폴더 경로로 이동한 다음 다음 명령어를 입력한다.

    jekyll new ./

그 과정에서 Conflict 에러가 발생한다면

    jekyll new ./ -f
-f 를 추가하여 입력하면 된다.

    New jekyll site installed in ...

 이 나온다면 성공적으로 설치된 것이다.
 이후 bundle까지 설치해준다.

    bundle install 

## 3.4. Jekyll 로컬 서버에 띄우기  
이제 마지막으로 로컬 서버로 실행해보면

    bundle exec jekyll serve

주소 http://127.1.0.0.1:4000/ 를 주소창에 입력하면 본인의 github blog의 첫 모습을 볼 수 있다.

## 3.5. push  
이제 username.github.io 에도 적용하기 위해 push해준다.

    git add .
    git commit -m "커밋메세지"
    git push

이렇게 하면 성공적으로 깃허브 블로그를 열 수 있다.  
사실 블로그 만드는 것은 상당히 쉽지만 이번 글을 쓰는 거의 주된 내용은 나와 같이 계정 2개 이상의 사용자들이 겪는 고통을 조금이라도 줄였으면 좋겠다는 마음에 작성해봤다.  
블로그 꾸미기는 다음 기회에~!!

<br />
참고한 글  
Special Thanks to...  
https://velog.io/@godqhrals/git-push%ED%96%88%EC%9D%84-%EB%95%8C-github%EC%97%90%EC%84%9C-%EB%82%B4-%EA%B3%84%EC%A0%95%EA%B3%BC-%EC%A0%9C%EB%8C%80%EB%A1%9C-%EC%97%B0%EB%8F%99%EC%9D%B4-%EC%95%88-%EB%90%9C-%EA%B2%BD%EC%9A%B0  
<br />
https://devpro.kr/posts/Github-%EB%B8%94%EB%A1%9C%EA%B7%B8-%EB%A7%8C%EB%93%A4%EA%B8%B0-(2)/  
https://zeddios.tistory.com/1222  
https://seo0yoon.tistory.com/222  
https://stackoverflow.com/questions/12940626/github-error-message-permission-denied-publickey/43882051#43882051  
https://heytech.tistory.com/478  
https://shanepark.tistory.com/284  
https://velog.io/@mywonhyuni/Git-%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%EC%A0%80%EC%9E%A5%EC%86%8C%EB%A7%88%EB%8B%A4-%EB%8B%A4%EB%A5%B8-%EA%B3%84%EC%A0%95-%EC%A0%95%EB%B3%B4-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0  
https://nemomemo.tistory.com/83  
https://heytech.tistory.com/289  



[Github][git-hub]  
[G-mail][g-mail]

[git-hub]: https://github.com/itsjulianjeong
[g-mail]: its.julianjeong@gmail.com