# docker-laravel
Laravel 프로젝트를 도커에서 실행시키기 쉽게 합니다. OP.GG 에서 필요한 모듈들이 미리 세팅되어 있습니다.

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
