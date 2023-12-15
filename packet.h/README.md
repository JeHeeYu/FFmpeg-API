# packet.h API 정리


## 함수

- [av_packet_unref](#av_packet_unref )



## av_packet_unref 

#### 함수 원형

```
void av_packet_unref(AVPacket *pkt);
```

#### 함수 설명

```
AVPacket 구조체의 내용을 초기화하고 해당 패킷에 할당된 메모리를 해제하는 함수
이 함수를 사용하여 패킷을 재사용하거나 메모리 누수 방지 가능
```

#### 매개 변수

```
pkt : 초기화 및 메모리 해제할 AVPacket 구조체에 대한 포인터
```

#### 동작 방법

```
1. AVPacket 구조체에 할당된 내용 초기화
2. AVPacket이 가지고 있는 데이터, 사이즈, 시간 정보 등이 초기 상태로 돌아감
```

<br>

