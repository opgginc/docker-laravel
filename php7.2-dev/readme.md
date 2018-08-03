PHP 7.2 이 구현되어 있는 Dockerfile 입니다. Supervisor 를 통해 cron 이 구현되어 있으며 cron 에서는 laravel scheduler 같은 것들을 넣으실 수 있습니다. 또한 Health Check 가 구현되어 있습니다.

이 도커 이미지는 개발용입니다. 그렇기 때문에 xdebug 가 설치되어 있습니다. 작동 속도가 느릴 수 있으니 참고하시기 바랍니다. 또한 FPM 이나 Apache 를 사용하지 않고, php serv 기능을 이용합니다.

## Cron 에 Laravel Scheduler 넣기
cron 파일을 여신 후 아래의 코드를 넣으면 스케줄러를 실행시키실 수 있습니다.
```
* * * * * root php /var/www/artisan schedule:run >/tmp/cron.last 2>&1
```