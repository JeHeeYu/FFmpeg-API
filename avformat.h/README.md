# avformat.h API 정리

## 함수
- [av_read_frame](#av_read_frame)

## 구조체

```
typedef struct AVFormatContext {
    const AVClass *av_class;
    const struct AVInputFormat *iformat;
    const struct AVOutputFormat *oformat;
    void *priv_data;
    AVIOContext *pb;
    int ctx_flags;
    unsigned int nb_streams;
    AVStream **streams;
    char *url;
    int64_t start_time;
    int64_t duration;
    int64_t bit_rate;
    unsigned int packet_size;
    int max_delay;
    int flags;
#define AVFMT_FLAG_GENPTS       0x0001 ///< Generate missing pts even if it requires parsing future frames.
#define AVFMT_FLAG_IGNIDX       0x0002 ///< Ignore index.
#define AVFMT_FLAG_NONBLOCK     0x0004 ///< Do not block when reading packets from input.
#define AVFMT_FLAG_IGNDTS       0x0008 ///< Ignore DTS on frames that contain both DTS & PTS
#define AVFMT_FLAG_NOFILLIN     0x0010 ///< Do not infer any values from other values, just return what is stored in the container
#define AVFMT_FLAG_NOPARSE      0x0020 ///< Do not use AVParsers, you also must set AVFMT_FLAG_NOFILLIN as the fillin code works on frames and no parsing -> no frames. Also seeking to frames can not work if parsing to find frame boundaries has been disabled
#define AVFMT_FLAG_NOBUFFER     0x0040 ///< Do not buffer frames when possible
#define AVFMT_FLAG_CUSTOM_IO    0x0080 ///< The caller has supplied a custom AVIOContext, don't avio_close() it.
#define AVFMT_FLAG_DISCARD_CORRUPT  0x0100 ///< Discard frames marked corrupted
#define AVFMT_FLAG_FLUSH_PACKETS    0x0200 ///< Flush the AVIOContext every packet.
/**
 * When muxing, try to avoid writing any random/volatile data to the output.
 * This includes any random IDs, real-time timestamps/dates, muxer version, etc.
 *
 * This flag is mainly intended for testing.
 */
#define AVFMT_FLAG_BITEXACT         0x0400
#define AVFMT_FLAG_SORT_DTS    0x10000 ///< try to interleave outputted packets by dts (using this flag can slow demuxing down)
#define AVFMT_FLAG_FAST_SEEK   0x80000 ///< Enable fast, but inaccurate seeks for some formats
#if FF_API_LAVF_SHORTEST
#define AVFMT_FLAG_SHORTEST   0x100000 ///< Stop muxing when the shortest stream stops.
#endif
#define AVFMT_FLAG_AUTO_BSF   0x200000 ///< Add bitstream filters as requested by the muxer
    int64_t probesize;
    int64_t max_analyze_duration;
    const uint8_t *key;
    int keylen;
    unsigned int nb_programs;
    AVProgram **programs;
    enum AVCodecID video_codec_id;
    enum AVCodecID audio_codec_id;
    enum AVCodecID subtitle_codec_id;
    unsigned int max_index_size;
    unsigned int max_picture_buffer;
    unsigned int nb_chapters;
    AVChapter **chapters;
    AVDictionary *metadata;
    int64_t start_time_realtime;
    int fps_probe_size;
    int error_recognition;
    AVIOInterruptCB interrupt_callback;
    int debug;
#define FF_FDEBUG_TS        0x0001
    int64_t max_interleave_delta;
    int strict_std_compliance;
    int event_flags;
#define AVFMT_EVENT_FLAG_METADATA_UPDATED 0x0001
    int max_ts_probe;
    int avoid_negative_ts;
#define AVFMT_AVOID_NEG_TS_AUTO             -1
#define AVFMT_AVOID_NEG_TS_DISABLED          0
#define AVFMT_AVOID_NEG_TS_MAKE_NON_NEGATIVE 1
#define AVFMT_AVOID_NEG_TS_MAKE_ZERO         2
    int ts_id;
    int audio_preload;
    int max_chunk_duration;
    int max_chunk_size;
    int use_wallclock_as_timestamps;
    int avio_flags;
    enum AVDurationEstimationMethod duration_estimation_method;
    int64_t skip_initial_bytes;
    unsigned int correct_ts_overflow;
    int seek2any;
    int flush_packets;
    int probe_score;
    int format_probesize;
    char *codec_whitelist;
    char *format_whitelist;
    int io_repositioned;
    const struct AVCodec *video_codec;
    const struct AVCodec *audio_codec;
    const struct AVCodec *subtitle_codec;
    const struct AVCodec *data_codec;
    int metadata_header_padding;
    void *opaque;
    av_format_control_message control_message_cb;
    int64_t output_ts_offset;
    uint8_t *dump_separator;
    enum AVCodecID data_codec_id;
    char *protocol_whitelist;
    int (*io_open)(struct AVFormatContext *s, AVIOContext **pb, const char *url,
                   int flags, AVDictionary **options);

#if FF_API_AVFORMAT_IO_CLOSE
    void (*io_close)(struct AVFormatContext *s, AVIOContext *pb);
#endif
    char *protocol_blacklist;
    int max_streams;
    int skip_estimate_duration_from_pts;
    int max_probe_packets;
    int (*io_close2)(struct AVFormatContext *s, AVIOContext *pb);
} AVFormatContext;

streams : 스트림의 배열
nb_streams : 동영상에 포함된 스트림의 개수
duration : 재생 시간으로 AV_TIME_BASE 단위
bit_rate 초당 비트레이트
```

<br>

## 함수

```
int avformat_open_input(AVFormatContext **ps, const char *url,
                        const AVInputFormat *fmt, AVDictionary **options)

description
동영상 및 파일을 여는 함수

arguments
ps : 헤더 정보를 저장할 구조체로, NULL로 지정 시 내부적으로 구조체를 할당하여 리턴
url : 파일 경로
fmt : 입력 포맷으로, NULL 지정 시 자동 판별
options : 옵션값의 배열로, 미사용 시 NULL

return
0 : 성공
-1 : 실패
```

<br>

```
int avformat_find_stream_info(AVFormatContext * ic, AVDictionary ** options )

description
동영상의 스트림을 조사하는 함수

arguments
ic : 스트림을 열 파일 핸들
options : 각 스트림에 적용할 옵션이되 미사용 시 NULL 지정

return
0 : 성공
-1 : 실패
```

<br>

```
void avformat_close_input(AVFormatContext **s)

description
읽은 파일의 메모리를 해제하는 함수

arguments
s : 해제할 파일 핸들러
```

<br>

```
void av_dump_format(AVFormatContext *ic, int index, const char *url, int is_output)

description
스트림의 정보를 확인하는 함수

arguments
ic : 파일의 AVFormatContext 핸들러
url : 파일 경로
is_output : 스트림 종류로 재생 시는 입력, 인코딩 시 출력
  0 : 입력용 스트림
  1 : 출력용 스트림
```

<br>

```
int av_find_best_stream(AVFormatContext *ic, enum AVMediaType type, int wanted_stream_nb, int related_stream, const struct AVCodec **decoder_ret, int flags)

description
스트림 중 최적의 스트림을 찾아 함수

arguments
ic : 파일의 AVFormatContext 핸들러
type : 파일 미디어 타입
  AVMEDIA_TYPE_AUDIO
  AVMEDIA_TYPE_VIDEO
  AVMEDIA_TYPE_SUBTITLE
wanted_stream_nb : 찾을 스트림의 인덱스로 원하는 특정 스트림 지정 가능
related_stream : 원하는 스트림과 관련된 스트림으로, 일반적으로 0 또는 -1 설정
decoder_ret : 찾은 스트림에 해당하는 디코더를 저장할 포인터로, 미사용 시 NULL 지정
flags : 추가적인 플래그로, 일반적으로 0 사용
```

<br>

## av_read_frame

#### 함수 원형

```
int av_read_frame(AVFormatContext *s, AVPacket *pkt)
```

#### 함수 설명

```
description
AVFormatContext 구조체에서 다음으로 사용 가능한 프레임을 읽어 오는 함수
```

#### 매개 변수

```
s : 미디어 파일의 형식을 나타내는 컨테이너 구조체로, 이 구조체에는 미디어 파일의 메타데이터 및 스트림 정보 등이 포함되어 있음
pkt : 읽어온 프레임의 데이터를 저장할 패킷 구조체로 패킷에는 읽어온 프렝미의 데이터, 해당 프레임의 타임스탬프, 소스 스트림 인덱스 등이 포함됨
```

#### 리턴

```
return
0 : 성공
-1 :
```

#### 동작 방법

```
1. 함수가 호출되면 현재 포인터가 가리키는 컨테이너(AVFormatContext)에서 다음 사용 가능한 패킷을 찾음
2. 찾은 패킷 데이터를 AVPacket 구조체에 복사
3. 함수 호출 시 다음 프레임으로 이동하며 파일의 끝에 도달하면 AVERROR_EOF 리턴
```

#### 예외 사항

```
- 파일 끝(End of File)에 도달 시 함수는 AVERROR_EOF를 리턴하며 파일을 모두 읽었다는 의미임
- 에러가 발생하면 해당 에러 코드를 리턴하고, 사용자는 av_strerror 함수 등을 사용하여 메시지를 얻을 수 있음
- 함수 호출 후 av_packet_unref 함수를 이용하여 메모리를 해제해야 
```

#### 사용 예제

```
AVFormatContext *formatContext;
AVPacket packet;

// 미디어 파일 열기
if (avformat_open_input(&formatContext, "example.mp4", NULL, NULL) < 0) {
    // 오류 처리
}

// 다음 프레임 가져오기
while (av_read_frame(formatContext, &packet) >= 0) {
    // 패킷 처리 (프레임 데이터, 타임스탬프, 스트림 인덱스 등이 포함됨)
    // ...

    // 처리가 완료되면 패킷 데이터를 해제
    av_packet_unref(&packet);
}

// 미디어 파일 닫기
avformat_close_input(&formatContext);
```
