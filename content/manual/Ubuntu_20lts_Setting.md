#

1. Ubuntu 접속 계정 환경 체크
iWinv 계정 생성 조건
- KR1-Z04 : 조건 SSD, 방화벽, 추가 서비스 확장
- Ubuntu 20.04 LTS(64bit)
- 메모리 : 512MB
- 용량 : 서버 25GB
- 서비스 : vCore.V2
- 방화벽 : On
- 정보 및 SSH 접속 정보값은 메일로 수신되었음 (2020.07.14 16:07)
- 요금제 : 1일 180원 / 월 4,800원

2. Console 페이지에서 [VNC Console 접근] 클릭
- 콘솔 접속 (메일 내용 참조)


## 우분투 패키지 최신화 상태로 하고 의존성 설치 하기

    apt update && apt upgrade
          :
    sudo apt install curl gnupg2 ca-certificates lsb-release

## AP 저장소에 nginX 설치

#### 패키지 관리를 위해 mainline 설치를 함. (하)

    echo "deb http://nginx.org/packages/mainline/ubuntu `lsb_release -cs` nginx" \ | sudo tee /etc/apt/sources.list.d/nginx.list

    처리결과 : deb http://nginx.org/packages/mainline/ubuntu focal nginx 
CGI
[https://s3.ap-northeast-2.amazonaws.com/opentutorials-user-file/module/384/1398.gif]

FastCGI
[https://s3.ap-northeast-2.amazonaws.com/opentutorials-user-file/module/384/1397.gif]

#### 패키지 신뢰성 확인 (하)

    curl -fsSL https://nginx.org/keys/nginx_signing.key | sudo apt-key add -

    처리결과 : OK

#### 신뢰성 확인

    sudo apt-key fingerprint ABF5BD827BD9BF62

    처리결과 : 
        pub   rsa2048 2011-08-19 [SC] [expires: 2024-06-14]
            573B FD6B 3D8F BC64 1079  A6AB ABF5 BD82 7BD9 BF62
        uid           [ unknown] nginx signing key <signing-key@nginx.com>

#### 위와 같이 모두 정상적인 상태라면 nginx 를 설치

    apt-get install update   ## 필자는 이거 안 하고 설치해서 아래 1.18 버전임.

    sudo apt-get install nginx
        중간에 Y or N 선택 -> Y
    
#### 완료 다 되었으면 버전 확인

    nginx -v

    처리결과 : nginx version: nginx/1.18.0 (Ubuntu)    
    ## apt install update 안해서 1.18 버전 흐음... 걍 고


## nginx 작동 및 상태 확인.

#### nginx 서비스 시작

    service nginx start

    처리결과 : 무소식이 희소식
    start : 구동 / stop : 종료 / restart : 재구동

#### 포트 상태 확인 (80 포트 상태 확인)

    netstat -nltp
    
    Active Internet connections (only servers)
    Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
    tcp        0      0 127.0.0.53:53           0.0.0.0:*               LISTEN      24424/systemd-resol 
    tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      24053/sshd: /usr/sb 
    tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN      38925/nginx: master 
    tcp6       0      0 :::22                   :::*                    LISTEN      24053/sshd: /usr/sb 
    tcp6       0      0 :::80                   :::*                    LISTEN      38925/nginx: master 


웹브라우저 주소창에 [ip주소:80] 입력하면 다음고 같이 나오면 성공.

    Welcome to nginx!
    If you see this page, the nginx web server is successfully installed and working. Further configuration is required.

    For online documentation and support please refer to nginx.org.
    Commercial support is available at nginx.com.

    Thank you for using nginx.


## PHP 설치 및 세팅

아파치가 아닌 nginx 를 설치하였기 때문에 PHP-FPM(PHP FastCGI Proceess Manager) 설치함.
nginx 에서 php 파일을 전달받아 실행, 결과 반환하는 독립 프로세스.

    sudo apt install php-fpm

    처리 : Setting up php7.4-fpm (7.4.3-4ubuntu2.2) ...

    sudo apt install php

    After this operation, 86.0 kB of additional disk space will be used.
    Do you want to continue? [Y/n] y

    얼... 7.2 버전 및 그 이하에서는 죽어라 Apache 랑 할거야 하고 지 맘대로 하였음. 그러나 7.4 버전에서는 nginx 와도 궁합 자동으로 맞추겠다고 함.

    * 주의사항 : 7.2 포함 이하 버전에서는 물음에 "Y" 절대 금지. 7.4 이상은 "Y" 만 됨.

PHP 버전 확인
   
    php -v

    HP 7.4.3 (cli) (built: May 26 2020 12:24:22) ( NTS )
    Copyright (c) The PHP Group
    Zend Engine v3.4.0, Copyright (c) Zend Technologies
        with Zend OPcache v7.4.3, Copyright (c), by Zend Technologies

## PHP 모듈 설치

기본 설치 모듈

    php -m                      ## 설치 된 모듈 리스트업

    [PHP Modules]
    calendar
    Core
    ctype
    date
    exif
    FFI
    fileinfo
    filter
    ftp
    gettext
    hash
    iconv
    json
    libxml
    openssl
    pcntl
    pcre
    PDO
    Phar
    posix
    readline
    Reflection
    session
    shmop
    sockets
    sodium
    SPL
    standard
    sysvmsg
    sysvsem
    sysvshm
    tokenizer
    Zend OPcache
    zlib

    [Zend Modules]
    Zend OPcache

추가 설치 모듈 (그누보드, 워드프레스 게시판 대비)

설치 방법 

    sudo apt-get install 모듈 리스트...

설치할 모듈 리스트

    php-mysql       ## MySQL, Maria DB 연동
    php-gd          ## 이미지 편집 처리
    php-mbstring    ## 다국어 처리
    php-curl        ## URL 매개변수 처리
    sendmail        ## 메일 발송
    php-zip         ## 압축 관련 처리
    php-bcmath      ## 수학 연산
    composer        ## 의존성 관리 프로그램
    php-xml         ## DOMDocument 확장
    

#### 파일 찾기

    sudo find / -name php.ini

    처리결과 :
    /etc/php/7.4/cli/php.ini
    /etc/php/7.4/fpm/php.ini

#### 수정할 파일은 fpm 폴더에 있는 것

    vi /etc/php/7.4/fpm/php.ini


## php.ini 세팅 수정
    - date.timezon = Asia/Seoul         ## 타임 기준 서울 시간
    - memory_limit = 128M               ## 메모리 용량 Fatal 에러 나면 조정할 것.
    - post_max_size = 20M               ## 생성되는 게시물 하나의 용량 첨부파일 포함
    - upload_max_filesize = 10M         ## 업로드 파일 용량
    - max_file_uploads = 20             ## 한번에 업로드할 수 있는 파일의 수.
    

수정 다 했으면 재부팅해서 php.ini 적용

    sudo systemctl restart php7.4-fpm.service

    만약 php7.4-fpm.service 가 틀리다고 에러 뜨면 
    {systemctl list-units --type=service}  를 통해 알아낼 것.


## 서버 블록 설정 php 연동
    nginx 서버블록과 php 파일을 연결하는 과정
    도메인이 할당되지 않음 : /etc/nginx/sites-enabled/default 

파일 수정 (/etc/nginx/sites-enabled/default)

    # Add index.php to the list if you are using PHP
        index index.php index.html index.htm index.nginx-debian.html;

## MySQL 설치

    sudo apt-get install mysql-client mysql-server

    sudo apt-get install php-mysql

