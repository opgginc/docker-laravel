Laravel 프로젝트를 도커에서 쉽게 실행시키기 위한 Dockerfile 을 모아둡니다. OP.GG 에서 필요한 모듈들이 미리 세팅되어 있습니다. PHP 도커 파일은 사용하기 전에 항상 원하는 모듈들을 설치하는 과정을 겪어야하는데, PHP 마이너 버전이 올라갈 때 마다 각 모듈들의 설치 방법이 조금씩 상이합니다. 그 작업을 회피하기 위하여 기본 Dockerfile 들을 모아둡니다.

## 사용 방법
이 Repository 를 pull 받으신 후 원하는 폴더에 들어가서 도커를 실행시키실 수 있습니다. 빌드를 먼저 한 뒤에 원하는 명령어를 실행시키면 됩니다.

### 빌드하기

```bash
$ cd php7.2-dev
$ docker build -t myphp .
```

### 한줄 명령어 실행
```bash
$ docker run -it --rm -v ${PWD}:/var/www -w /var/www myphp php 파일명.php
```

### PHP 웹서버 실행
```bash
$ docker run -d -p 89:80 -v ${PWD}:/var/www -w /var/www myphp
$ curl -vs http://localhost:89
```
포트 변경이 필요할 경우 `89:80`를 수정하면 됩니다.
