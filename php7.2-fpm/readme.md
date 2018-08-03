PHP 7.2 FPM 이 구현되어 있는 Dockerfile 입니다. Supervisor 를 통해 cron 이 구현되어 있으며 cron 에서는 laravel scheduler 같은 것들을 넣으실 수 있습니다. 또한 Health Check 가 구현되어 있습니다.

FPM이기 때문에 nginx 나 apache 로 별도의 웹서버를 구성하셔야합니다.

## 1. 이 패키지의 파일들을 수정해서 사용하는 방법
### Cron 에 Laravel Scheduler 넣기
cron 파일을 여신 후 아래의 코드를 넣으면 스케줄러를 실행시키실 수 있습니다.
```
* * * * * root php /var/www/artisan schedule:run >/tmp/cron.last 2>&1
```

## 2. `FROM opgg/php:7.2-dev` 와 같은 방식으로 사용할 경우

### Supervisor 에 설정 추가하기
```dockerfile
ADD ./supervisor.conf /etc/supervisor/conf.d/
```
원하는 설정을 꾸민 후, /etc/supervisor/conf.d 폴더에 넣으면 됩니다.

### Cron 추가
```dockerfile
ADD ./cron /etc/cron.d/
```
원하는 설정을 꾸민 후, /etc/cron.d 폴더에 넣으면 됩니다.

### PHP 설정 변경
```dockerfile
ADD custom-conf.d/ /usr/local/etc/php/conf.d/
ADD custom-php.ini /usr/local/etc/php/php.ini
```
원하는 설정을 꾸민 후, 위와 같은 위치에 파일을 넣으시면 됩니다. `conf.d`는 파일명 순서로 실행이 되기 때문에, override 하고 싶은 것들은 z- 로 시작하게 하시면 늦게 실행됩니다. 