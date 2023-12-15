# packet.h API 정리


## 함수

- [av_packet_unref](#av_packet_unref)
- [av_packet_alloc](#av_packet_alloc)


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

#### 예제 코드
```
int main() {
    // 패킷 변수 선
    AVPacket pkt;
    
    // 패킷을 사용한 후 초기화 및 메모리 해제
    av_packet_unref(&pkt);
    
    return 0;
}
```

<br>

## av_packet_alloc

#### 함수 원형
```
AVPacket *av_packet_alloc(void);
```

#### 함수 설명
```
AVPacket을 할당하고 해당 필드를 기본값으로 설정하는 함수
```

#### 리턴
```
AVPacket : 성공
NULL : 실패
```

#### 동작 방법
```
1. AVPacket을 할당 받을 AVPacket 구조체 포인터 변수 선언
2. av_packet_alloc 함수를 이용하여 packet 초기화
```

#### 예외 사항
```
- 메모리 할당 후 av_packet_free 함수를 이용하여 메모리 해제 필요
```

#### 예제 코드
```
int main() {
    // 패킷 변수 선언
    AVPacket *pkt;
    
    // 패킷 초기화
    pkt = av_packet_alloc();

    // 메모리 해제
    av_packet_free(&pkt);
    
    return 0;
}
```

<br>

## av_packet_free

#### 함수 원형
```
void av_packet_free(AVPacket **pkt);
```

#### 함수 설명
```
av_packet_alloc 함수를 이용하여 할당한 메모리를 해제하는 함수
```

#### 매개 변수
```
pkt : 메모리 해제할 AVPacket 구조체 포인터 변수
```

#### 예제 코드
```
int main() {
    // 패킷 변수 선언
    AVPacket *pkt;
    
    // 패킷 초기화
    pkt = av_packet_alloc();

    // 메모리 해제
    av_packet_free(&pkt);
    
    return 0;
}
```
