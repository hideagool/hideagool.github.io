<img src="https://user-images.githubusercontent.com/23450738/169300058-50567d70-2c0e-4c0b-b36a-5396e0168dc5.png" width="100" >  

# Fluentd란
<img src="https://user-images.githubusercontent.com/23450738/169296036-7f5bafdc-0813-4ec7-826a-79908152fb75.png" width="300" >  

간단히 말하면 **로그 수집기** 라고 할수 있다.  
## 왜 필요 한가
서버가 한대인 경우 로그를 확인 하기 위해서는 한대의 서버만 확인하면 된다.  
  
하지만 서버가 여러대 라면 어떨까?  
스케쥴러를 사용해서 주기마다 로그를 모아서 확인하는 방법은 있다.  
  
그러면 클라우드 위에 있는 서버는 어떨까?  
도커나 쿠버네티스 혹은 오토스케일링되는 EC2 서버인경우 서버(컨테이너)가 내려가면 로그는 전부 유실된다.  
그럼 실시간으로 로그를 수집해야하는 상황이 생기는데 이때 필요한 것이 **로그 수집기** 이다.  

# 설치하기
[참고바람](https://docs.fluentd.org/installation)

# 설정 및 사용

### Input
#### source: 모든 데이터의 출처
```
<source>
  @type forward
  port 24224
</source>
```
@type은 필수 값이며 
* tail : tail -f의 출력값
* forward : tcp에서 받은 값 ( 클라이언트 용)
* udp : udp에서 받은 값
* tcp : tcp에서 받은 값
* unix : 소켓통신에서 받은 값
* http : http 통신에서 받은 값
* syslog : 시스템 로그에서 받은값
* exec : 실행 결과 값
* sample : 샘플 값
* windows_eventlog : windows의 이벤트 로그 값  
의 값을 갖는다.  

***
##### 주로 사용하는 플러그인

###### @type tail
웹서버 에서 주로 사용하는 건 tail 플러그인 이다.  
아래는 아파치 서버 로그 예제  
```
<source>
  @type tail
  path /var/log/httpd-access.log
  pos_file /var/log/td-agent/httpd-access.log.pos
  tag apache.access
  <parse>
    @type apache2
  </parse>
</source>
```
[공식문서](https://docs.fluentd.org/input/tail)
  
###### @type forward
다른 fluentd 서버에서 전송된 로그를 받을때에 주로 사용된다.(ex: 여러 서버의 로그를 한곳에서 전송 받을때)
```
<source>
  @type forward
  port 24224
</source>
```

### Filter
### Outfut
#### match: 무엇을 할지 알려주기  
```
<match pattern>
  @type forward
  send_timeout 60s
  recover_wait 10s
  hard_timeout 60s

  <server>
    name myserver1
    host 192.168.1.3
    port 24224
    weight 60
  </server>
</match>
```

@type은 필수 값이며 
* copy
* null
* roundrobin
* stdout
* exec_filter
* forward
* mongo
* exec
* file
* s3
* webhdfs  
의 값을 갖는다.  
추가적으로 엘라스틱 서치 플러그인 설치를 통해 연동 가능하다.  


