# avformat.h API 정리

```
int avformat_open_input(AVFormatContext **ps, const char *url,
                        const AVInputFormat *fmt, AVDictionary **options);

arguments
ps : 헤더 정보를 저장할 구조체로, NULL로 지정 시 내부적으로 구조체를 할당하여 리턴
url : 동영상을 읽을 파일 경로
fmt : 입력 포맷으로, NULL 지정 시 자동 판별
options : 옵션값의 배열로, 미사용 시 NULL

return
0 : 성공
-1 : 실패
```
