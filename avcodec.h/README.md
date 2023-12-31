# avcodec.h API 정리


## 함수
- [avcodec_send_packet ](#avcodec_send_packet)
- [avcodec_receive_frame](#avcodec_receive_frame)

### AVCodecContext *avcodec_alloc_context3(const AVCodec *codec)

```
description
Codec Context를 할당 및 초기화 하는 함수

arguments
codec : 초기화할 AV Codec

return
AVCodecContext : 초기화된 CodecContext 구조체
NULL : 실패
```

<br>

### int avcodec_parameters_to_context(AVCodecContext *codec, const AVCodecParameters *par)

```
description
Codec Parameter의 정보를 CodecContext에 복사하는 함수

arguments
codec : Codec Parameter를 설정할 Codec
par : AVCodecParameters 구조체로부터 가져온 Stream Parameter

return
0 : 성공
-1 : 실패
```

<br>

### int avcodec_open2(AVCodecContext *avctx, const AVCodec *codec, AVDictionary **options)

```
description
Codec을 열어 디코더 또는 인코더를 초기화하는 함수

arguments
avctx : 초기화할 AVCodecContext 구조체
codec : 사용할 Codec에 대한 정보를 담고 있는 AVCodec 구조체
options : 초기화 옵션을 설정할 때 사용하며 미사용 시 NULL 지정

return
0 : 성공
-1 : 실패
```

<br>

### void avcodec_free_context(AVCodecContext **avctx)

```
description
사용 중인 AVCodecContext 구조체의 메모리를 해제하는 함수

arguments
avctx : 메모리 해제할 AVCodecContext의 구조체
```



## avcodec_send_packet


#### 함수 원형

```
int avcodec_send_packet(AVCodecContext *avctx, const AVPacket *avpkt)
```

#### 함수 설명

```
디코더에게 비디오 또는 오디오의 패킷을 제공하는 역할을 함
이 함수를 사용하여 디코더에게 비디오 또는 오디오 스트림의 압축된 데이터를 전송하고 디코딩을 시작함
```

#### 매개 변수

```
avctx : AVCodecContext 구조체에 대한 포인터로 디코더의 설정과 상태를 나타냄
avpkt : AVPacket 구조체에 대한 포인터로 디코더에 전송할 비디오 또는 오디오 패킷을 포함
```

#### 리턴

```
return
0 : 성공
-1 : 실패
```

#### 동작 방법

```
1. avctx 파라미터로 전달된 AVCodecContext 구조체를 통해 디코더의 상태 확인
2. avpkt 파라미터로 전달된 AVPacket 구조체에 대한 패킷을 디코더에게 전송
3. 디코더는 전송된 패킷을 받아 내부 버퍼에 저장하고 디코딩 작업 수행
```

#### 예외 사항

```
- 함수가 실패할 경우 반환된 음수 값에 따라 예외 처리 필
```

#### 사용 예제

```
int main() {
    // FFmpeg 초기화
    av_register_all();
    avcodec_register_all();

    // 디코더를 찾기 위한 AVCodec 및 AVCodecContext 구조체 생성
    AVCodec *codec = avcodec_find_decoder(AV_CODEC_ID_H264);
    if (!codec) {
        fprintf(stderr, "Codec not found\n");
        return -1;
    }

    AVCodecContext *codecContext = avcodec_alloc_context3(codec);
    if (!codecContext) {
        fprintf(stderr, "Could not allocate video codec context\n");
        return -1;
    }

    // 디코더 초기화
    if (avcodec_open2(codecContext, codec, NULL) < 0) {
        fprintf(stderr, "Could not open codec\n");
        return -1;
    }

    // AVPacket 및 AVFrame 구조체 생성
    AVPacket avpkt;
    av_init_packet(&avpkt);
    avpkt.data = NULL;
    avpkt.size = 0;

    AVFrame *frame = av_frame_alloc();
    if (!frame) {
        fprintf(stderr, "Could not allocate video frame\n");
        return -1;
    }

    // 비디오 파일 열기
    const char *filename = "sample.mp4";
    AVFormatContext *formatContext = avformat_alloc_context();
    if (avformat_open_input(&formatContext, filename, NULL, NULL) != 0) {
        fprintf(stderr, "Could not open video file: %s\n", filename);
        return -1;
    }

    // 스트림 정보 가져오기
    if (avformat_find_stream_info(formatContext, NULL) < 0) {
        fprintf(stderr, "Could not find stream information\n");
        return -1;
    }

    // 비디오 스트림 찾기
    int videoStreamIndex = -1;
    for (int i = 0; i < formatContext->nb_streams; i++) {
        if (formatContext->streams[i]->codecpar->codec_type == AVMEDIA_TYPE_VIDEO) {
            videoStreamIndex = i;
            break;
        }
    }

    if (videoStreamIndex == -1) {
        fprintf(stderr, "Could not find video stream in the input file\n");
        return -1;
    }

    // 비디오 스트림 컨텍스트 가져오기
    AVStream *videoStream = formatContext->streams[videoStreamIndex];
    AVCodecParameters *videoCodecParameters = videoStream->codecpar;

    // 비디오 디코더 설정
    if (avcodec_parameters_to_context(codecContext, videoCodecParameters) < 0) {
        fprintf(stderr, "Failed to copy video codec parameters to decoder context\n");
        return -1;
    }

    // 디코더 초기화
    if (avcodec_open2(codecContext, codec, NULL) < 0) {
        fprintf(stderr, "Could not open codec\n");
        return -1;
    }

    // 비디오 프레임 처리 예시 (여기서는 단순히 출력)
    while (av_read_frame(formatContext, &avpkt) >= 0) {
        // 패킷이 비디오 스트림에 속하는지 확인
        if (avpkt.stream_index == videoStreamIndex) {
            // avcodec_send_packet 호출
            int ret = avcodec_send_packet(codecContext, &avpkt);
            if (ret < 0) {
                fprintf(stderr, "Error sending a packet for decoding: %s\n", av_err2str(ret));
                return -1;
            }

            // avcodec_receive_frame 호출 (실제로 디코딩된 프레임을 받음)
            ret = avcodec_receive_frame(codecContext, frame);
            if (ret == AVERROR(EAGAIN) || ret == AVERROR_EOF) {
                // 더 이상 디코딩할 데이터가 없거나, 디코딩이 완료된 경우
                continue;
            } else if (ret < 0) {
                fprintf(stderr, "Error during decoding: %s\n", av_err2str(ret));
                return -1;
            }

            // 여기서 디코딩된 프레임을 사용할 수 있음
            // 예: 화면에 표시하거나 파일에 저장하는 등의 작업 수행

            // 비디오 프레임 처리 예시 (여기서는 단순히 출력)
            printf("Decoded frame: width=%d, height=%d\n", frame->width, frame->height);
        }

        av_packet_unref(&avpkt);
    }

    // 메모리 해제
    av_frame_free(&frame);
    avcodec_close(codecContext);
    avcodec_free_context(&codecContext);
    avformat_close_input(&formatContext);

    return 0;
}
```


<br>



## avcodec_receive_frame

#### 함수 원형

```
int avcodec_receive_frame(AVCodecContext *avctx, AVFrame *frame)
```

#### 함수 설명

```
avcodec_send_packet 함수로 전송한 패킷에 대한 디코딩 결과로 프레임을 받아올 때 사용
```

#### 매개 변수

```
avctx : AVCodecContext 구조체에 대한 포인터로 디코더의 설정과 상태를 나타냄
frame : AVFrame 구조체에 대한 포인터로 디코딩된 프레임이 이 구조체에 저장됨
```

#### 리턴

```
0 : 성공
AVERROR(EAGAIN) : 버퍼 또는 패킷 등 현재 처리 조건이 맞지 않아 이후에 다시 시도하라는 의미
AVERROR_EOF : 마지막 프레임 까지 디코딩 완료
AVERROR(EINVAL) : 함수 호출 시 인자로 전달되는 포인터나 값이 유효하지 않을 경우(디코더가 아닌 인코더를 넣는 등)
AVERROR_INPUT_CHANGED : 프레임의 매개변수가 달라졌을 경우
other : 에러
```

#### 동작 방법

```
1. avctx 파라미터로 전달된 AVCodecContext 구조체를 통해 디코더의 상태 확인
2. frame 파라미터로 전달된 AVFrame 구조체에 디코딩된 프레임 저장
```

#### 예외 사항

```
- 함수 실패 시 예외 처리 필요
- EAGIAN은 코딩이 완료되지 않았을 때 반환되는 값으로 추가적인 패킷 전송 필요
- AVERROR_EOF는 디코딩이 완료되었다는 의미로 더 이상 디코딩할 데이터가 없
```

#### 사용 예제

```
while (/* 여러 개의 AVPacket을 가져올 조건 */) {
    // AVPacket을 가져온다. (예: 파일에서 읽기, 네트워크에서 받기 등)
    // avpkt에는 가져온 패킷의 데이터와 사이즈가 채워진다.

    // avcodec_send_packet 호출
    if (avcodec_send_packet(codecContext, &avpkt) < 0) {
        fprintf(stderr, "Error sending a packet for decoding\n");
        return -1;
    }

    // avcodec_receive_frame 호출
    int ret = avcodec_receive_frame(codecContext, frame);
    if (ret == 0) {
        // 디코딩이 성공한 경우, 여기서 frame 구조체에 디코딩된 프레임 데이터가 저장되어 있음
        // frame을 사용하여 필요한 처리 수행
        printf("Decoded frame: width=%d, height=%d\n", frame->width, frame->height);
    } else if (ret == AVERROR(EAGAIN)) {
        // 더 이상 디코딩할 데이터가 없을 때, 추가적인 패킷을 전송해야 함
        continue;
    } else if (ret == AVERROR_EOF) {
        // 디코딩이 완료되었으며, 더 이상 디코딩할 데이터가 없음
        // 추가적인 처리 또는 종료 작업 수행
        break;
    } else {
        // 디코딩 오류 처리
        fprintf(stderr, "Error receiving a frame: %s\n", av_err2str(ret));
        return -1;
    }
}
```
