# 분석

## 해당 논문에서의 실험 방법
* 1단계: `OOO-trace.py`를 실행. (로그파일을 저장)
* 2단계: `XXX-parse.py`를 실행. (로그파일을 그래프로 시각화)

---
### OOO-trace.py의 실행 방법 예시
```bash
$ python3 deepspeed-opt-13b-io-trace-block.py --fs YOUR_FILE_SYSTEM --bs BATCH_SIZE
$ 예) python3 deepspeed-opt-13b-io-trace-parse.py --fs ext4 --bs 1
```
BATCH_SIZE는 LLM 모델이 몇 번의 입력을 처리하는지에 대한 매게변수 입니다.

---
### OOO-trace.py를 실행해며며 생기는 로그 파일
[[로그 파일들의 링크]](https://github.com/2daeeun/cheops25-IO-characterization-of-LLM-model-kv-cache-offloading-nvme/tree/main/results/figure5-6-kv-offloading-flexgen/flexgen-kv-offload-opt-6.7b-bs-64-ext4-trace)
* **flexgen-opt-6.7b-kv-trace-ext4-bs-64-log.txt** 
    - 파싱 명령어의 로그 파일
* **opt-6.7b-kv-offload-bs-64-ext4.txt** [뭔지 잘 모름]
    - 파싱 명령어의 로그 파일(?)
* **opt-6.7b-kv-offload-bs-64-ext4-bpftrace-block.txt** [★ 필요]
    - block I/O의 로그 파일
* **opt-6.7b-kv-offload-bs-64-ext4-gpu.txt** 
    - GPU 파싱 로그 파일

---
### opt-6.7b-kv-offload-bs-64-ext4-bpftrace-block.txt
`OOO-trace.py` 파일에 있는는 해당 코드가 I/O 로그파일을 생성한다.

```bash
sudo bpftrace ../bpftrace-scripts/bio-bite-size.bt > {results_file_bpftrace} 2>&1'
```

---
### `bio-bite-size.bt`(bprface trace)에 대한 설명
```bash
// check major and minor: ls -l /dev/nvme0n1

tracepoint:block:block_rq_issue
/args->dev == ((259 << 20) | 4)/ // Match major=259, minor=4
{
    printf("%llu, %s, %d, %llu, %d\n", nsecs, args->rwbs, args->bytes, args->sector, args->nr_sector);
}

/* 출력 예시
45502109053447, W, 4096, 170104688, 8
45502109071121, W, 4096, 170104864, 8
45502109074148, W, 4096, 170105280, 8
45502109076588, W, 4096, 170105744, 8
    │           │    │       │      │
    │           │    │       │      └──── args->nr_sector:  요청된 섹터의의 수
    │           │    │       └─────────── args->sector:     I/O 요청이 시작되는 논리 섹터 번호
    │           │    └─────────────────── args->bytes:      요청된 데이터 전송 바이트 수
    │           └──────────────────────── args->rwbs:       I/O 요청의 종류 (R: 읽기, W: 쓰기)
    └──────────────────────────────────── nsecs:            이벤트가 발생한 시간(나노초 단위)
*/
```

[[출력예시 파일 보기]]()

<details>
<summary>check major and minor 확인 방법 [접기/펼치기]</summary>
```bash
$ ls -l /dev/sda
$ brw-rw---- 1 root disk 8, 0  3월 30일  19:42 /dev/sda
$ /args->dev == ((8 << 20) | 0)/
```
</details>






