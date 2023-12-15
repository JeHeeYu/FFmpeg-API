# Frame.h API 정리

## 함수 
- [av_frame_unref](#av_frame_unref)



## av_frame_unref

#### 함수 원형
```
void av_frame_unref(AVFrame *frame);
```

#### 함수 설명
```
AVFrame 구조체의 내용을 초기화하고 해당 프레임에 할당된 메모리를 해제하는 함수
이 함수를 사용하여 프레임을 재사용하거나 메모리 누수 방지 가능
```

#### 매개 변수
```
frame : 초기화 및 메모리 해제할 AVFrame 구조체에 대한 포인터
```

#### 동작 방법
```
1. AVFrame 구조체에 할당된 내용 초기화
2. AVFrame이 가지고 있는 데이터, 사이즈, 시간 정보 등이 초기 상태로 돌아감
```

#### 예제 코드
```
#include <libavutil/frame.h>

int main() {
    // 프레임 초기화
    AVFrame *frame = av_frame_alloc();
    
    // 프레임을 사용한 후 초기화 및 메모리 해제
    av_frame_unref(frame);
    
    return 0;
}
```
