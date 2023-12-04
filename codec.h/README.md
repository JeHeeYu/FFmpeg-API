# codec.h API 정리

```
AVCodec *avcodec_find_decoder(enum AVCodecID id)

description
Codec을 찾는 함수

arguments
id : 찾을 Codec의 ID

return
AVCodec : 찾은 Codec의 구조체
NULL : 실패
```
