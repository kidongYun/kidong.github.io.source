---
layout: post
title:  "우아한레디스, 디디의 Redis"
date:   2021-03-13 00:0054 +0900
categories: java
---

캐쉬의 구조 2가지 형태

1. Look aside Cache
메인은 DB이고 DB에서 캐쉬로 데이터를 가져와서 저장. 캐쉬에 데이터가 있으면 캐쉬 데이터를 사용하여 성능을 개선하고 없으면 디비에서 가져와서 다시 캐쉬에 저장.
일반적인 구조

2. Write back
캐쉬에 데이터를 특정기간 동안 먼저 저장하고 캐쉬에 있는 데이터들을 디비에 저장.
휘발할 수 있는 가능성 있다. 

Collection
개발의 편의성, 개발의 난이도.

Memcached 보다 redis가 나은 점 -> 레디스는 collection을 제공하고, memcached는 이걸 제공하지 않느다.
컬렉션이 제공되면 개발의 편의성이 높아지고, 생산성이 올라간다.

즉 멤캐시드는 이런걸 제공하지 않기 때문에 다 직접 구현해야한다는 의미.

두 트랜잭션의 레이스 컨디션이 발생하는 문제 -> 멤캐시드는 이런거 세마포어나 뮤텍스 기법으로 해결을 해야하는데 레디스는 기본적으로 ACID의 A가 지원된다. 레이스 컨디션 오류를 기본적으로 잡아준다.

레디스의 컬렉션이 이런걸 지원한다.

레디스를 주로 쓰는경우
여러대의 서버에서 접근해야하는 원격 저장소가 필요할때
EX) 인증 토큰, 랭킹 보드, 유저 API LIMIT, 잡 큐

레디스 컬렉션
String : key-value 구조
List :
Set : 중복 데이터 방지
Sorted set : 기본적으로 set인데 스토어를 줘서 순서를 제공

String
Set <key> <value>
get <key>

mset
mget
여러개를 넣거나 가져올 수 있다

List 타입 : 왼쪽 오른쪽으로 넣거나 뺄 수 있다.
Lpust Rpust Lpop Rpop


데이터 성질에 따라서 컬렉션 구조를 잘 골라야한다.

set
sadd smembers sismember
 smembers 는 데이터를 모두 가져오는 건데 이런거 성능 주의.

sorted set : 이거 많이 사용한다고 한
zadd <key> <score> <value>
zrange <key> <startIndex> <endIndex>
zrange


레디스는 인메모리 저장소 이기 떄문에. 물리적메모리 공간보다 더 클수 없다.
물론 불가하다는건 아닌데 swap이 발생하게 되고 결국 디스크를 활용하게 되서
레디스를 제대로 활용하는 방법이 아니다.
그래서 레디스는 메모리 사용률을 모니터링 해야한다.

메모리 관리.
64기가바이트 메모리를 가진 서버에 인스턴스라는 적을 작은 단위로 사용하는 것이 안전함.
이해가 안되는 군. 뭔가 어렵다.

jemalloc -> java 쪽에서메모리 할당하는 함수같은데. 이거가 4096 이런 단위로 할당함

메모리가 부족할 때는
-> Cache is Cash  캐쉬가돈이다.
보통 70% 정도 사용하고 있다면 더 큰 시스템을 사용하거나, 데이터량을 줄여야하는 선택지를 고려해야함.

Collection 들이 아래 자료구조를 사용.
Hash -> HashTable
Sorted Set -> Skiplist, HashTable
Set -> HashTable

실제 위와 같은 컬렉션을 사용하게 되면 메모리 사용률은 조금 더 나온다. 포인터 등 오버헤드 작업에서 필요한 메모리가 존재 

Ziplist 이거를 사용하면 조금 더 느리지만 메모리는 좀더 절약할 수 있다.
인메모리기 때문에 그냥 선형탐색을 하더라도 빠르다. 즉 메모리 공간 사용률을 줄이고 시간복잡도를 그냥 높게 가져간다는 의미인거 같음.


O(N) 관련 명령어는 주의하다.
Redis는 싱글 스레드.
레디스는 싱글 스레드이기 떄문에 여러 개의 명령을 처리할 수 없다.

싱글 스레드이기 때문에 하나의 명령어가 시간이 오래걸리는걸 사용하면 다른 명령어들이 밀려서 타임아웃이 발생.
그래서 조심히 사용해야 한다.

ex) KEYS, FLUSHALL, FLUSHDB, Delete Collections, Get All Collections

실수 사례 -  Key가 백만개 이상인데 확인을 위해 KEYS 명령을 사용하는 경우.
KEYS 명령은 scan 명령으로 대체 가능.
KEYS 는 한번에 처리, scan은 긴 명령을 짧은 여러번의 명령으로 바꾸기 때문에 타임아웃 발생 가능성 낮아짐.
저 사이사이에 작업들을 함.

Collection의모든 item을 가져와야 할 때.
Collection 작은단위로 나눠서 관리하자.

Springsecurity oauth Redis TokenStore 이슈.

Redis Replication.
Replication 이라는것은 A서버의 데이터를 B서버에 변경사항도 동일하게 복제가 가능하다.

Async Replication
-> Replication Lag이 발생할 수 있다.
마스터에 데이터가 변경되면 슬레이브에 변경내용을 콜. 근데 이 상황에 렉이 발생하면 미매칭 될 수도 있음.
master-slave 용어에서 original-replica.

Redis Replication 설정 과정.

redis.conf 권장설정
Maxclient 설정 50000 -> 접속 가능한 고객 수
RDB/AOF 설정 off -> 성능개선 마스터는 꺼두고 레플리카에만 켜둔다. 필요하다면
특정 commands disable
Keys
AWS의 ElasticCache 이미 하고 있음.

Redis 데이터 분산 방법.
Application
	- Consistent Hashing
		서버0,서버1, 

원래 서버 로드밸런싱할때 모듈러 연산을 하나?? -> 리밸런싱에 취약하다.

서버가 증축되었을 때 데이터가 리밸런스가 50% 이상일어나고, 자주 일어나면 좋지 않은 현상.
반대로 서버 하나가 장애가 일어나면 다시 리밸런스가 일어나야함.
이런 형태가 서버가 장애에 취약한 방식.
이걸 해결하기 위한 Consistent Hashing 방식.

Consistent Hashing 방식
서버가 모듈러 연산이 아니고 해쉬 코드를 가진다. ex) 10000, 20000, 30000
해쉬코드 값이 서버랑 비교해서 가장 크고 가장 가까운 곳으로 가게끔 한다.
더 큰 코드가 없으면 원형구조로 가장 해쉬코드가 작은 작은 서버로 간다.
서버가 추가되거나 제거될때 
이런식으로 하면 기존의 데이터들의 리밸런스가 영향이 있는 데이터만 하고 다른 데이터들은 수정될 필요가 없다.

Sharding 방식
데이터를 어떻게 나눌 것인가.
데이터를 어떻게 찾을 것인가.

하나의 데이터를 모든 서버에서 찾아야 하면?

상황마다 샤딩 전략이 달라짐.

가장 간단한 방법 Range 적용.
하나의 샤딩이 1~1000 Key를 가지고 두번째는 2~2000 이런식으로.

이렇게 되면 ID가 몰리면 하나의 서버에 데이터가 집중되서 특정 서버는 놀게되는 현상이 발생.
다른 데이터들을 다른 서버로 옮길 수가 없고. 불균등하다.

서버를 확장하는것은 쉽다 단순히 키를 증가시키면 되기 때문에.


두번째 방법 modular 적용.
2배씩 늘리는 이유는 데이터 이주가 심플해진다. 가는 곳이 정해지기 떄문.
서버가 2배로 늘어나면 가야할 서버가 0 -> 2, 1 -> 3 이렇게 간다
먼 느낌인지 알듯.
문제가 32개의 서버에서 64개로 늘리기는 쉽지 않다.

서버 한대씩 늘어나는 것은 리밸런싱이 많이 일어남.

Indexed
해당 키가 어디에 저장되어야 할 관리 서버가 따로 존재. 그때그떄 서버들의 상태를 이서버를 확인
이 인덱스 서버가 죽으면 관리를 못하는 단점.

Redis Cluster
client - primary #1 - Secondary #1
			primary #2 - Secondary #2
			primary #3 - Secondary #3

Slot 단위의 데이터 관리. -> 메모리 사용량이 더 많음.
라이브러리 구현이 필요.


Redis Failover

Coorinator - Zookeeper, etcd, consul?

VIP 기반, DNS기반

API Server 가 특정 VIP, DNS 로레디스 서버를 호출하고. 레디스 서버는 기본적으로 #1 이 이 DNS, VIP를 가리키고 있는데 장애가 발생하면. Health-checker 이거가 #2로 변경한다.
API Server 가 추가적인 구현이 필요없다.

DNS 기반은 DNS Cache TTL을 확인해봐야함.
만약 이 유효기간이 길면 장애처리가 이 기간동안에는 안될 수 있음.

Moitoring Factor
Redis Info를 통한 정보
RSS (Physical Memory를 얼마나 사용하고 있는가.) 이게 젤 중요
Used Memory 
Connection 수 -> 레디스는 싱글 스레드라서 커넥션 맺꼬 끊은거를 계속 하면 성능이 떨어진다.
초당 처리 요청 수

Monitor 명령을 통해 사용되고 있는 명령들의 모음을 볼 수 있음.
이것도 성능에 영향이 있음으로 쓸때는 짧게 써야함.

기본적으로 레디스는 좋으나 메모리가 빡빡하게 사용할 경우 어렵다.

Redis as Cache 로 바라본다면 그래도 문제가 적게 발생.
이때에는 디비에 부하가 어느정도 증가하는지를 확인해야함.

Redis as Persistent Store 로 본다면 좀더 주의해야 한다.
메모리를 절대 빡빡하게 사용하면 안됨.
RDB/AOF가필요하다면 Secondary에서만 구동.
이 방법으로 쓰는거면 돈을 많이 투자할 수 밖에 없다.

——————————— 디디의레디스.

레디스의 개요.

remote dictionary server

여기서 dictionary 는 python 에서 나오는데 map구조.

In memory data structure.

다양한 자료구조를 제공한다. -> Collection.

Cache : 나중의 요청에 대한 미리 저장해두고 나중에 빠르게 사용하는 것.

메모리의 계층 구조.

디스크 -> 메인메모리 -> 캐쉬 -> 레지스터

캐시(SRAM) vs 메인메로리(DRAM) ?? 구현 관점에서 궁금.

메인메모리는 휘발성.

원리 디비 개념은 디스크에 저장하고 가져오는 개념이였는데 메모리도 용량이 커지면서
메모리에서 그냥 데이터 저장하고 캐쉬 형태로 사용하는 디비가 나왔는데 이게 레디스

컬렉션. (자료구조) 멤캐시드는 이걸 제공하지 않는다.

자바랑 비교

String : Map
List : LinkedList
Sorted Set : TreeSet

자바랑 어떻게 다를 까.

왜 자바의 컬렉션 프레임워크를 안 쓸까.

서버가 여러대인 경우. 일관성문제 발생.
특정 서버에는 있고 다른 서버에서 없다.

Multi-Threading 환경에서 레이스 컨디션 발생.
근데 레디스는 애초에 싱글 스레드인데.
레디스 자료구조는 임계구역에 대해서 기본적으로 동기화 처리를 제공한다.
그렇기 때문에 컬렉션 프레임워크를 안쓰는 거구나.

여러 서버에서 공유를 해야하고, 임계구역에 대한 조치가 컬렉션 프레임워크랑 레디스의 컬렉션이 다른 점.

레디스가 근데 무슨 언어로 구현되어있지?

레디스가 싱글 스레드 방식으로 동작하는 이유.
IO 작업이 많기 때문에 -> IO 쪽은 리소스가 애초에 적기 때문에 스레드가 많아도 병목현상이 발생하고 실제적으로 성능을 끌어올리기 어렵다.

싱글 스레드이기 때문에 시간 복잡도가 O(N) 부터 주의해서 사용해야함. 왜냐하면 다른 작업들이 밀리게 되고 타임아웃이 발생하게 됨.

메모리 관리.
메모리 파편화 -> 실제로 더 많이 사용하는 걸로 보임. 파편화된 부분도 운영체제는 사용하는 메모리라고 인지.
가상 메모리의 swap -> 메인메모리가 가득차게되면 이게 필수적. 디스크를 접근하는거기 때문에 지연시간이 필연적.
Replication -> 데이터 복제하는 기능. 슬레이브 영역에 저장하는 방법.
복제하는 방법 Fork() ->

Redis Persistant -> 레디스를 캐쉬의 목적이 아닌 RDB 목적으로 사용하는 방법.
