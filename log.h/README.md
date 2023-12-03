# log.h API 정리


```
void av_log(void *avcl, int level, const char *fmt, ...);

arguments
avcl : 로그 출력 대상
level : 로그 레벨로, define 되어 있는 값 사용
  #define AV_LOG_QUIET    -8
  #define AV_LOG_PANIC     0
  #define AV_LOG_FATAL     8
  #define AV_LOG_ERROR    16
  #define AV_LOG_WARNING  24
  #define AV_LOG_INFO     32
  #define AV_LOG_VERBOSE  40
  #define AV_LOG_DEBUG    48
  #define AV_LOG_TRACE    56
*fmt : 출력 문자열

description
로그 출력 함수
```
