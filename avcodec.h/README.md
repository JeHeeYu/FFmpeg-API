# avcodec.h API 정리


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
