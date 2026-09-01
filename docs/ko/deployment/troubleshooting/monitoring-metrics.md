# 모니터링 지표 참조

[← ShimoDocs Suite 배포 문서](../README.md)

이 문서는 모니터링 시스템에서 일반적으로 사용되는 지표를 정리하며, 노드, containerd 컨테이너, Kubernetes 클러스터, 미들웨어 및 애플리케이션 서비스를 다루며, 일상 점검, 용량 평가 및 문제 해결을 위한 통합 참조를 제공합니다. 

지표 이름은 Prometheus에서 수집한 실제 exporter 지표를 기반으로 합니다. exporter 버전에 따라 약간의 차이가 있을 수 있으며, 실제 문제 해결 시 최종 참조는 온라인 쿼리 결과를 기준으로 해야 합니다. 

## 범위 

| 분류 | 대상 객체 | 
| --- | --- | 
| 노드 모니터링 | Linux 호스트, 시스템 자원, 디스크, 네트워크, 프로세스 | 
| 컨테이너 모니터링 | containerd에서 실행 중인 컨테이너, Pod 컨테이너 자원 | 
| Kubernetes 클러스터 | 노드, Pod, 배포(Deployment), StatefulSet, 작업(Job), PVC, APIServer | 
| MySQL | MySQL 인스턴스, 연결, 쿼리, 캐시, 잠금, 네트워크 | 
| MongoDB | MongoDB 인스턴스, 연결, 연산, 메모리, 네트워크, 복제 버퍼 | 
| Redis | Redis 인스턴스, 클라이언트, 명령어, 메모리, 키스페이스, 적중률 | 
| Kafka | 브로커, 토픽, 파티션, 컨슈머 그룹, 지연, 복제본 | 
| MinIO | 클러스터 노드, 디스크, 버킷 S3 요청, 객체 용량 | 
| Elasticsearch | 클러스터 상태, 노드, 샤드, 인덱스 JVM, 스레드 풀, 네트워크 |
| 애플리케이션 서비스 | 일반 서버, 클라이언트 호출, 협업 편집, RS 서비스, 런타임 |

## 메트릭 읽기 규칙

| 메트릭 유형 | 읽기 방법 | 일반적인 PromQL 문법 | 설명 |
| --- | --- | --- | --- |
| 카운터 | 시간 창 내에서 성장률이나 증가량을 확인 | `rate(x_total[5m])`, `increase(x_total[5m])` | 요청 수, 오류 수, 바이트 수, IO 시간은 일반적으로 카운터에 속함 |
| 게이지 | 현재 값, 평균, 최대값을 확인 | `avg(x)`, `max(x)`, `sum(x)` | 메모리, 연결 수, 용량, 상태 값은 일반적으로 게이지에 속함 |
| 히스토그램 | 백분위수 지연 시간 확인 | `histogram_quantile(0.95, sum(rate(x_bucket[5m])) by (le))` | 요청 지연, 처리 지연, 큐 지연은 일반적으로 히스토그램 사용 |
| 비율 | 백분율 확인 | `A / B * 100` | 사용률, 오류율, 적중률은 모두 비율형 메트릭 |

임계값에 대해 고정 숫자를 직접 복사하지 않는 것이 권장됨. 메트릭 예: CPU, 메모리, 디스크, 연결 수, QPS, 그리고 지연은 비즈니스 피크, 용량 계획 및 과거 기준선의 맥락에서 평가되어야 합니다. 문서에 나와 있는 비정상 동작은 위험을 신속하게 식별하기 위해 사용되며 최종 경보 임계값과 동일하지 않습니다.

## 1. 노드 서비스 모니터링

노드 모니터링은 호스트가 건강한지, 리소스가 충분한지, 디스크 또는 네트워크 병목이 있는지를 판단하는 데 사용됩니다. 노드 지표는 주로 node-exporter에서 가져오며, 프로세스 수준의 로컬라이제이션을 위해 시스템 프로세스 대시보드와 결합됩니다.

### 1.1 기본 상태

| 모니터링 차원 | 지표 | 지표 의미 | 일반 표준/단위 | 비정상 성능 |
| --- | --- | --- | --- | --- |
| 노드 살아 있음 | `up` | 익스포터 또는 수집 대상에 접근할 수 있는지 여부 | `1` 수집 가능함을 나타냄, `0` 수집 불가함을 나타냄 | 지속적으로 `0` 노드, 네트워크 또는 익스포터에 문제가 있음을 나타냄 |
| 부팅 시간 | `node_boot_time_seconds` | 노드의 마지막 부팅 시간 | 유닉스 타임스탬프 | 부팅 시간 변화는 노드가 재시작되었음을 나타냄 |
| 노드 정보 | `node_uname_info`, `node_os_info` | 운영 체제, 커널 및 배포 정보 | 라벨 정보 | 노드 버전을 확인하는 데 사용되며, 경고 지표로 직접 사용되지 않음 |

문제 해결 제안: 먼저 확인 `up` 그 다음 `node_boot_time_seconds`. 노드가 수집 불가능하고 부팅 시간이 최근에 변경된 경우, 호스트 재부팅, 네트워크 ACL및 node-exporter 프로세스 상태 확인을 우선시하십시오.

### 1.2 CPU 지표

| 모니터링 차원 | 지표 | 지표 의미 | 일반 표준/단위 | 비정상 성능 |
| --- | --- | --- | --- | --- |
| CPU 사용법 | `node_cpu_seconds_total` | 각 누적 시간 CPU 코어가 다른 모드에서 사용하는 전력 | 백분율 | `user` 그리고 `system` 장기적으로 높은 상태가 유지되며, 노드 컴퓨팅 파워가 부족합니다 |
| 유휴 CPU | `node_cpu_seconds_total{mode="idle"}` | CPU 유휴 시간 | 백분율 | 유휴 시간이 지속적으로 낮아 대기열과 지연 시간이 증가할 수 있습니다 |
| IO 대기 | `node_cpu_seconds_total{mode="iowait"}` | 시간 CPU 디스크 IO를 기다리는 중 | 백분율 | iowait의 지속적인 증가는 일반적으로 디스크 또는 저장소 링크 성능 저하를 나타냅니다 |
| 시스템 부하 | `node_load1`, `node_load5`, `node_load15` | 1/5/15분 평균 부하 | 부하 값 | 부하가 지속적으로 코어 수를 초과하면 CPU 작업 대기 큐가 눈에 띄게 발생함을 나타냅니다 |
| CPU 압력 | `node_pressure_cpu_waiting_seconds_total` | 누적 CPU PSI 대기 시간 | 초/초 | CPU 리소스 경쟁이 심각하여, 프로세스가 스케줄링을 위해 대기 중임 CPU 스케줄링 |

일반적인 쿼리:

```promql
100 - avg by (instance) (rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100
```

```promql
avg by (instance) (rate(node_cpu_seconds_total{mode="iowait"}[5m])) * 100
```

조사 제안: 언제 CPU 사용량이 높습니다. 먼저 구분하세요 `user`, `system`, 및 `iowait`. 높음 `user` 은 대부분 비즈니스 계산 압력, 높음 때문입니다 `system` 는 시스템 호출 및 네트워크 패킷 처리와 관련이 있을 수 있으며, 높은 `iowait` 는 디스크 처리량, IOPS, 및 지연 시간을 확인해야 합니다.

### 1.3 메모리 지표

| 모니터링 차원 | 지표 | 지표 의미 | 공통 단위 | 비정상 현상 |
| --- | --- | --- | --- | --- |
| 총 메모리 | `node_memory_MemTotal_bytes` | 노드의 총 물리 메모리 | 바이트 | 사용률 계산에 사용됨 |
| 사용 가능한 메모리 | `node_memory_MemAvailable_bytes` | 시스템이 프로세스에 할당할 수 있는 메모리 | 바이트 / 백분율 | 지속적으로 낮은 사용 가능한 메모리는 트리거되기 쉽습니다 OOM 또는 빈번한 회수 |
| 사용 가능한 메모리 | `node_memory_MemFree_bytes` | 완전히 사용되지 않은 메모리 | 바이트 | 메모리 압력을 판단하기 위해 Linux에서 단독으로 사용할 수 없습니다 |
| 메모리 압력 | `node_pressure_memory_waiting_seconds_total` | 누적 메모리 PSI 대기 시간 | 초/초 | 메모리 회수 또는 할당 대기 증가 |
| OOM 카운트 | `node_vmstat_oom_kill` | 시스템 수 OOM 종료된 프로세스 수 | 카운트/증가 | 증가할 때, 종료된 프로세스와 메모리 피크를 식별 |

일반적인 쿼리:

```promql
(1 - node_memory_MemAvailable_bytes / node_memory_MemTotal_bytes) * 100
```

```promql
increase(node_vmstat_oom_kill[10m])
```

조사 제안: 단순히 `MemFree` 메모리만 확인하지 마십시오. 실제 사용 가능 여부는 `MemAvailable`과 컨테이너 작업 집합 메모리, 프로세스 RSS, 및 OOM 기록을 함께 평가해야 합니다.

### 1.4 디스크 용량 및 인포드

| 모니터링 차원 | 지표 | 지표 의미 | 일반 측정/단위 | 비정상 성능 |
| --- | --- | --- | --- | --- |
| 전체 파일 시스템 | `node_filesystem_size_bytes` | 마운트 지점의 전체 용량 | 바이트 | 디스크 사용률 계산에 사용됨 |
| 사용 가능한 파일 시스템 | `node_filesystem_avail_bytes` | 일반 사용자가 사용할 수 있는 공간 | 바이트 | 사용 가능한 공간 부족은 쓰기 실패를 초래할 수 있음 |
| 무료 파일 시스템 | `node_filesystem_free_bytes` | 파일 시스템의 여유 공간 | 바이트 | 루트 예약 공간 포함; 일반적으로 함께 고려됨 `avail` |
| 읽기 전용 상태 | `node_filesystem_readonly` | 파일 시스템이 읽기 전용인지 여부 | `0/1` | 언제 `1`, 비즈니스 쓰기가 실패함 |
| 총 인오드 | `node_filesystem_files` | 파일 시스템의 총 인오드 수 | 카운트 | 작은 파일 시나리오에서 특별한 주의 필요 |
| 남은 인오드 | `node_filesystem_files_free` | 남은 인오드 수 | 개수/백분율 | 인오드가 소진되면 디스크 공간이 남아 있어도 파일을 생성할 수 없음 |

일반적인 쿼리:

```promql
(1 - node_filesystem_avail_bytes{fstype!~"tmpfs|overlay"} / node_filesystem_size_bytes{fstype!~"tmpfs|overlay"}) * 100
```

```promql
(1 - node_filesystem_files_free / node_filesystem_files) * 100
```

조사 제안: 디스크 용량 경고는 마운트 포인트별로 확인해야 하며, 특히 데이터 디스크, 로그 디스크 및 컨테이너 런타임 디렉터리에 대해 확인해야 합니다. 높은 inode 사용량은 일반적으로 많은 수의 작은 파일, 로그 조각 또는 정리되지 않은 임시 파일에서 발생합니다.

### 1.5 디스크 IOPS, 처리량 및 지연

| 모니터링 차원 | 지표 | 지표 의미 | 일반 측정/단위 | 비정상 성능 |
| --- | --- | --- | --- | --- |
| 읽기 IOPS | `node_disk_reads_completed_total` | 완료된 디스크 읽기 요청 수 | 횟수/초 | 읽기 IOPS 장치 한계에 가까워지면 읽기 지연 증가 |
| 쓰기 IOPS | `node_disk_writes_completed_total` | 완료된 디스크 쓰기 요청 수 | 횟수/초 | 쓰기 백로그, 로그 또는 데이터베이스 커밋 지연 |
| 읽기 처리량 | `node_disk_read_bytes_total` | 디스크에서 읽은 누적 바이트 | 바이트/초 | 높은 처리량과 높은 iowait는 스토리지 사용이 바쁜 상태를 나타냄 |
| 쓰기 처리량 | `node_disk_written_bytes_total` | 디스크에 기록된 누적 바이트 | 바이트/초 | 장기적으로 높은 쓰기 처리량은 데이터베이스 및 객체 저장소에 영향을 줄 수 있음 |
| 읽기 시간 | `node_disk_read_time_seconds_total` | 읽기 요청의 누적 시간 | 초/초 | 읽기 지연 증가 |
| 쓰기 시간 | `node_disk_write_time_seconds_total` | 쓰기 요청의 누적 시간 | 초/초 | 쓰기 지연 증가 |
| IO 바쁨 | `node_disk_io_time_seconds_total` | 디스크가 IO를 처리하는 누적 시간 | 비율 | 부하가 거의 가득 찼을 때, 애플리케이션은 IO를 기다립니다 |
| 가중 IO 시간 | `node_disk_io_time_weighted_seconds_total` | 큐 길이를 고려한 IO 시간 | 초/초 | 큐 적체는 심각한 장치 큐잉을 나타냅니다 |
| IO 압력 | `node_pressure_io_waiting_seconds_total` | 누적 IO PSI 대기 시간 | 초/초 | 프로세스가 IO를 기다리는 시간이 길어집니다 |

일반 쿼리:

```promql
rate(node_disk_reads_completed_total[5m])
```

```promql
rate(node_disk_writes_completed_total[5m])
```

```promql
rate(node_disk_read_bytes_total[5m])
```

```promql
rate(node_disk_written_bytes_total[5m])
```

```promql
rate(node_disk_io_time_seconds_total[5m]) * 100
```

```promql
rate(node_disk_read_time_seconds_total[5m]) / rate(node_disk_reads_completed_total[5m])
```

```promql
rate(node_disk_write_time_seconds_total[5m]) / rate(node_disk_writes_completed_total[5m])
```

조사 제안: 문제를 확인할 때 디스크 용량만 보지 마십시오. 용량이 정상이어도, IOPS처리량, IO 바쁨, iowait가 동시에 증가하면 비즈니스 성능이 느려질 수 있습니다. 데이터베이스와 같은 무거운 IO 서비스는 Kafka, 및 MinIO 쓰기 지연 시간과 큐에 집중해야 합니다.

### 1.6 네트워크 지표

| 모니터링 차원 | 지표 | 지표 의미 | 공통 단위 | 이상 징후 |
| --- | --- | --- | --- | --- |
| 수신 트래픽 | `node_network_receive_bytes_total` | 네트워크 카드가 수신한 누적 바이트 | 바이트/초 | 요청 폭주 또는 데이터 동기화로 인한 수신 트래픽 급증 가능성 |
| 발신 트래픽 | `node_network_transmit_bytes_total` | 네트워크 카드가 전송한 누적 바이트 | 바이트/초 | 다운로드, 백업 또는 복제로 인한 발신 트래픽 급증 가능성 |
| 수신 오류 | `node_network_receive_errs_total` | 수신된 오류 패킷 누적 수 | 건수/초 | 네트워크 카드, 링크 또는 드라이버 문제 |
| 송신 오류 | `node_network_transmit_errs_total` | 송신 오류 패킷 누적 수 | 건수/초 | 링크 문제 또는 네트워크 카드 큐 문제 |
| 수신 패킷 손실 | `node_network_receive_drop_total` | 수신 폐기된 패킷 누적 수 | 건수/초 | 커널 큐 또는 네트워크 카드가 따라가지 못함 |
| 송신 패킷 손실 | `node_network_transmit_drop_total` | 송신 패킷 손실 누적 값 | 회/초 | 송신 혼잡 또는 NIC 큐 압력 |

일반 쿼리:

```promql
rate(node_network_receive_bytes_total{device!~"lo|veth.*|cni.*"}[5m])
```

```promql
rate(node_network_transmit_bytes_total{device!~"lo|veth.*|cni.*"}[5m])
```

```promql
rate(node_network_receive_drop_total[5m]) + rate(node_network_transmit_drop_total[5m])
```

조사 제안: 네트워크 이상 현상의 경우 트래픽, 오류 패킷 및 패킷 손실을 함께 살펴보세요. 단순히 트래픽만 높다고 해서 꼭 문제가 있는 것은 아니며, 오류 패킷이나 패킷 손실이 동반된 높은 트래픽이 있을 경우 링크 또는 호스트 네트워크 스택 문제일 가능성이 높습니다.

### 1.7 TCP파일 핸들 및 시스템 스트레스

| 모니터링 차원 | 지표 | 지표 의미 | 일반 단위 / 측정 | 비정상 동작 |
| --- | --- | --- | --- | --- |
| 현재 TCP 연결 | `node_netstat_Tcp_CurrEstab` | 현재 확립된 연결 수 TCP 연결 | 개수 | 연결 수가 갑자기 증가하면 트래픽 피크 또는 연결 누출을 나타낼 수 있습니다 |
| TIME_WAIT | `node_sockstat_TCP_tw` | 수 TIME_WAIT 연결 | 개수 | 너무 많은 단기 연결은 포트를 소모하거나 커널 압력을 증가시킬 수 있습니다 |
| TCP 할당됨 | `node_sockstat_TCP_alloc` | 할당된 수 TCP 소켓 | 개수 | 소켓 수가 지속적으로 증가하면 연결 해제 여부를 조사해야 합니다 |
| TCP 사용 중 | `node_sockstat_TCP_inuse` | 수 TCP 사용 중인 소켓 | 개수 | 연결 압력 증가 |
| TCP 고아 | `node_sockstat_TCP_orphan` | 고아 소켓 수 | 개수 | 비정상적 증가가 비정상 연결 종료와 관련될 수 있습니다 |
| 사용된 파일 핸들 | `node_filefd_allocated` | 시스템에서 할당한 파일 핸들 수 | 개 | 너무 높으면 새로운 연결 및 파일 열기에 영향을 줄 수 있습니다 |
| 파일 핸들 제한 | `node_filefd_maximum` | 시스템 파일 핸들 제한 | 개 | 핸들 사용률 계산에 사용 |

일반적인 문의: 

```promql
node_filefd_allocated / node_filefd_maximum * 100
```

조사 권장 사항: 파일 핸들과 TCP 연결은 일반적으로 함께 고려됩니다. 서버 연결 수가 급증할 때 시스템 핸들이 한계에 가까우면, 애플리케이션이 수락 실패, 파일 열기 실패 또는 의존 연결 실패를 경험할 수 있습니다.

### 1.8 프로세스 모니터링

| 모니터링 차원 | 지표 | 지표 의미 | 일반 측정/단위 | 비정상 동작 |
| --- | --- | --- | --- | --- |
| 프로세스 CPU | `process_cpu_seconds_total` | 총 CPU 프로세스 시간 | 초/초 | 장기간 높은 CPU 단일 프로세스에 의한 사용 |
| 물리 메모리 | `process_resident_memory_bytes` | 프로세스 RSS 메모리 | 바이트 | 지속적인 증가 RSS 메모리 누수를 나타낼 수 있습니다 |
| 가상 메모리 | `process_virtual_memory_bytes` | 프로세스 가상 메모리 | 바이트 | 비정상적인 증가를 평가할 때 함께 고려해야 함 RSS |
| 열린 핸들 | `process_open_fds` | 프로세스의 열린 파일 핸들 수 | 개수 | 지속적인 증가는 핸들 누수를 나타낼 수 있음 |
| 최대 핸들 | `process_max_fds` | 프로세스가 열 수 있는 최대 파일 핸들 수 | 개수 | 프로세스 핸들 사용률 계산에 사용됨 |
| 프로세스 시작 시간 | `process_start_time_seconds` | 프로세스 시작 시간 | 유닉스 타임스탬프 | 시작 시간의 변화는 프로세스 재시작을 나타냄 |

조사 권장 사항: 프로세스 메트릭은 노드 수준 문제의 특정 서비스를 확인하는 데 사용됨. 노드 CPU 가 높을 때, 프로세스를 확인 CPU; 노드 메모리 압력이 높을 때, 확인 RSS; 노드 핸들이 높을 때, 확인 `process_open_fds`. 

## 2. containerd 컨테이너 모니터링

컨테이너 모니터링은 주로 kubelet/cAdvisor에서 가져오며, containerd가 관리하는 컨테이너의 리소스 사용률을 반영함. 문서에서는 계속해서 Prometheus 메트릭의 명명을 사용하지만, 실제 운영 중의 기본 컨테이너 런타임은 containerd임. `container_*`  

### 2.1 컨테이너 CPU

| 모니터링 차원 | 지표 | 지표 의미 | 공통 범위/단위 | 비정상 성능 |
| --- | --- | --- | --- | --- |
| CPU 사용법 | `container_cpu_usage_seconds_total` | 총 CPU 컨테이너 사용 시간 | 코어/초 | 장기간 사용률이 한계에 근접하여 비즈니스 지연 가능성 |
| CPU 제한 시간 | `container_cpu_cfs_throttled_seconds_total` | 총 시간 CPU 에 의해 제한됨 CFS | 초/초 | 중요 CPU 중요한 제한은 한계가 너무 낮거나 부하가 너무 높음을 나타냄 |
| CPU 할당량 | `container_spec_cpu_quota` | 컨테이너 CPU 쿼터 | 쿼터 값 | 식별에 사용됨 CPU 제한이 설정되어 있는지 |

일반적인 문의: 

```promql
sum by (namespace, pod, container) (rate(container_cpu_usage_seconds_total{container!="",image!=""}[5m]))
```

```promql
sum by (namespace, pod, container) (rate(container_cpu_cfs_throttled_seconds_total{container!="",image!=""}[5m]))
```

조사 권장 사항: 높은 컨테이너 CPU 반드시 스케일링이 필요한 것은 아님. 먼저 제한되고 있는지 확인하고, 그 다음 Pod의 요청/제한이 너무 낮은지 확인하며, 마지막으로 서비스 요청 지연을 고려하여 실제로 비즈니스에 영향을 주는지 판단함.

### 2.2 컨테이너 메모리

| 모니터링 차원 | 지표 | 지표 의미 | 공통 단위 | 비정상 동작 |
| --- | --- | --- | --- | --- |
| RSS 메모리 | `container_memory_rss` | 컨테이너 익명 페이지 및 RSS 메모리 | 바이트 | 지속적인 성장은 실제 프로세스 메모리 압력에 더 가까움 |
| 사용된 메모리 | `container_memory_usage_bytes` | 총 컨테이너 메모리 사용량 | 바이트 | 캐시 포함, 단독으로 누수를 판단할 수 없음 |
| 워킹 세트 메모리 | `container_memory_working_set_bytes` | 컨테이너의 활성 워킹 세트 메모리 | 바이트 | 한계에 접근하면 OOMKilled 발생 가능 |
| 메모리 제한 | `container_spec_memory_limit_bytes` | 컨테이너 메모리 제한 | 바이트 | 메모리 사용률 계산에 사용됨 |

일반 쿼리:

```promql
container_memory_working_set_bytes{container!="",image!=""} / container_spec_memory_limit_bytes{container!="",image!=""} * 100
```

조사 제안: 비즈니스 컨테이너의 메모리 위험에 대해 작업 집합(working set)을 우선적으로 확인하고, RSS. `usage_bytes` 주로 페이지 캐시에 영향을 받으므로 용량 관찰에는 적합하지만 단독 판단 기준으로는 적합하지 않습니다. OOM 

### 2.3 컨테이너 디스크 및 임시 저장소

| 모니터링 차원 | 지표 | 지표 의미 | 일반 측정/단위 | 비정상 성능 |
| --- | --- | --- | --- | --- |
| 읽기 처리량 | `container_fs_reads_bytes_total` | 컨테이너가 디스크로부터 읽은 누적 바이트 | 바이트/초 | 읽기 트래픽의 급격한 증가가 스캔, 가져오기(import), 캐시 원본 가져오기를 나타낼 수 있습니다. |
| 쓰기 처리량 | `container_fs_writes_bytes_total` | 컨테이너가 디스크에 쓴 누적 바이트 | 바이트/초 | 쓰기 피크는 노드 IO 압력을 발생시킬 수 있습니다. |
| 읽기 IOPS | `container_fs_reads_total` | 컨테이너의 읽기 요청 수 | Ops/s | 작은 블록 읽기의 빈도가 높으면 IO 대기 시간이 늘어날 수 있습니다. |
| 쓰기 IOPS | `container_fs_writes_total` | 컨테이너의 쓰기 요청 수 | Ops/s | 로그나 임시 파일의 과도한 쓰기 |
| 파일시스템 사용량 | `container_fs_usage_bytes` | 컨테이너 파일시스템 사용량 | 바이트 | 임시 파일이나 로그의 누적 |
| 파일 시스템 한계 | `container_fs_limit_bytes` | 컨테이너 파일 시스템 한계 | 바이트 | 한계에 접근하면 쓰기가 실패할 수 있습니다. |

일반적인 쿼리: 

```promql
sum by (namespace, pod, container) (rate(container_fs_reads_bytes_total{container!="",image!=""}[5m]))
```

```promql
sum by (namespace, pod, container) (rate(container_fs_writes_bytes_total{container!="",image!=""}[5m]))
```

조사 제안: 컨테이너 디스크 쓰기가 비정상일 때, 먼저 Pod 로그 볼륨, 임시 파일 디렉터리, 배치 작업을 확인하십시오. 노드 디스크 IO가 높을 경우, 컨테이너 FS 메트릭을 사용하여 어떤 Pod가 쓰기 작업을 하는지 확인할 수 있습니다.

### 2.4 컨테이너 네트워크

| 모니터링 차원 | 지표 | 지표 의미 | 공통 범위/단위 | 비정상 성능 |
| --- | --- | --- | --- | --- |
| 수신 트래픽 | `container_network_receive_bytes_total` | 컨테이너가 수신한 총 바이트 | 바이트/초 | 요청 트래픽 또는 복제 트래픽의 급격한 증가 |
| 송신 트래픽 | `container_network_transmit_bytes_total` | 컨테이너가 보낸 총 바이트 | 바이트/초 | 다운로드, 동기화, 원본 가져오기 또는 내보내기 트래픽 증가 |
| 수신 패킷 손실 | `container_network_receive_packets_dropped_total` | 컨테이너가 수신 중 드롭한 총 패킷 수 | 회/초 | 네트워크 스택 또는 노드 부담으로 인한 패킷 손실 |
| 송신 패킷 손실 | `container_network_transmit_packets_dropped_total` | 컨테이너가 전송 중에 버린 전체 패킷 | 회/초 | 출구 혼잡, NIC 대기열, 또는 CNI 문제 |

일반 쿼리:

```promql
sum by (namespace, pod) (rate(container_network_receive_bytes_total[5m]))
```

```promql
sum by (namespace, pod) (rate(container_network_transmit_bytes_total[5m]))
```

조사 제안: 컨테이너 네트워크는 노드와 함께 분석되어야 합니다 NIC 메트릭. Pod 수준에서 패킷 손실이 증가하지만 노드에 이상이 없다면, Pod가 위치한 노드의 CNI, iptables 및 부하를 계속 확인하십시오. 

### 2.5 컨테이너 스레드 및 라이프사이클

| 모니터링 차원 | 지표 | 지표 의미 | 공통 범위/단위 | 비정상 동작 |
| --- | --- | --- | --- | --- |
| 스레드 수 | `container_threads` | 컨테이너 내 스레드 수 | 개수 | 스레드가 지속적으로 증가하면 스레드 누수를 나타낼 수 있습니다 |
| 마지막 확인 | `container_last_seen` | cAdvisor가 컨테이너를 마지막으로 확인한 시간 | 유닉스 타임스탬프 | 오랫동안 업데이트가 없으면 컨테이너가 종료되었거나 수집 이상을 나타낼 수 있습니다 |
| 재시작 횟수 | `kube_pod_container_status_restarts_total` | 컨테이너 재시작 총 횟수 | 카운트/증가 | 잦은 재시작은 충돌, 프로브 실패를 나타냅니다 OOM |
| 대기 이유 | `kube_pod_container_status_waiting_reason` | 컨테이너가 대기 상태인 이유 | 라벨 값 | `CrashLoopBackOff`, `ImagePullBackOff`등, 문제를 해결해야 합니다 |
| 실행 상태 | `kube_pod_container_status_running` | 컨테이너가 실행 중인지 여부 | `0/1` | 주요 컨테이너 없음 `1` 서비스를 사용할 수 없음을 나타냅니다 |

조사 권장 사항: 컨테이너 이상 현상의 경우 먼저 상태 이유를 확인하고, 재시작 횟수와 최근 재시작 시간을 확인하십시오. 재시작이 잦으면 애플리케이션 로그, OOM 이벤트 및 프로브 구성을 사용하여 계속 조사하십시오. 

## 3. Kubernetes 클러스터 모니터링

Kubernetes 모니터링은 클러스터 리소스 사용량, 제어 평면 상태, 워크로드 복제본 상태 및 스토리지 객체 상태를 평가하는 데 사용됩니다. 주요 지표는 kube-state-metrics, kubelet 및 APIServer에서 가져옵니다. 

### 3.1 노드 용량 및 스케줄링 가능한 리소스

| 모니터링 차원 | 지표 | 지표 의미 | 공통 범위/단위 | 비정상 현상 |
| --- | --- | --- | --- | --- |
| 노드 용량 | `kube_node_status_capacity` | 노드의 총 용량 | CPU, 메모리, Pod 수 등 | 용량 계획에 사용 |
| 할당 가능 리소스 | `kube_node_status_allocatable` | 노드의 스케줄링 가능한 리소스 | CPU, 메모리, Pod 수 등 | 스케줄링 가능한 리소스가 부족하면 Pod가 Pending 상태가 됨 |
| 노드 상태 | `kube_node_status_condition` | 노드 Ready, MemoryPressure 등 상태 | `0/1` | Ready 상태 이상 또는 Pressure 발생 시 즉시 주의 필요 |
| 스케줄링 금지 | `kube_node_spec_unschedulable` | 노드 Cordon 여부 | `0/1` | '1'로 설정하면 노드는 새로운 Pod를 스케줄하지 않음 |
| 노드 정보 | `kube_node_info` | 노드 버전, 커널, 컨테이너 런타임 정보 | 태그 정보 | 버전 차이 문제 해결에 사용 |

문제 해결 제안: Pod가 Pending일 때 먼저 할당 가능 자원과 요청을 확인하고, 노드가 'unschedulable'인지 확인한 후, 마지막으로 노드 상태가 리소스 압박을 겪고 있는지 확인합니다. 

### 3.2 Pod 상태 

| 모니터링 차원 | 지표 | 지표 의미 | 일반적인 구멍/단위 | 비정상 동작 |
| --- | --- | --- | --- | --- |
| 파드 정보 | `kube_pod_info` | 파드 네임스페이스, 노드 등 정보 | 태그 정보 | 파드 분포를 찾는 데 사용 |
| 파드 단계 | `kube_pod_status_phase` | Pending, Running, Succeeded, Failed 상태 등 | `0/1` | Pending/Failed 증가 시 예약 또는 실행 이상을 나타냄 |
| 파드 준비 상태 | `kube_pod_status_ready` | 파드가 준비되었는지 여부 | `0/1` | 준비되지 않으면 서비스 가용성에 영향 |
| 파드 이유 | `kube_pod_status_reason` | 파드 비정상 이유 | 레이블 값 | Evicted, NodeLost 등은 조사 필요 |
| 컨테이너 재시작 | `kube_pod_container_status_restarts_total` | 컨테이너 재시작 횟수 | 회/증가 | 재시작 증가 시 안정성 문제를 나타냄 |
| 컨테이너 대기 | `kube_pod_container_status_waiting` | 컨테이너가 대기 상태인지 여부 | `0/1` | 대기 상태가 지속되면 파드가 정상적으로 서비스를 제공할 수 없음 |
| 대기 이유 | `kube_pod_container_status_waiting_reason` | 대기 상태 이유 | 레이블 값 | 이미지 풀 실패, CrashLoop 등 |
| 컨테이너 종료 | `kube_pod_container_status_terminated` | 컨테이너가 종료되었는지 여부 | `0/1` | 예상치 못한 종료는 재시작 및 로그와 함께 확인 필요 |

일반적인 쿼리:

```promql
sum by (namespace, phase) (kube_pod_status_phase == 1)
```

```promql
increase(kube_pod_container_status_restarts_total[10m])
```

조사 제안: 파드 이상이 있을 때 파드 상태만 보는 것에 그치지 마세요. 준비 상태, 이유, 컨테이너 대기 이유가 구체적인 문제를 더 잘 설명합니다.

### 3.3 리소스 요청 및 제한

| 모니터링 차원 | 지표 | 지표 의미 | 일반 측정/단위 | 이상 행동 |
| --- | --- | --- | --- | --- |
| 요청된 리소스 | `kube_pod_container_resource_requests` | 컨테이너 요청 | CPU, 메모리 | 요청이 너무 높으면 스케줄링에 영향을 주고, 너무 낮으면 안정성에 영향을 줍니다 |
| 리소스 제한 | `kube_pod_container_resource_limits` | 컨테이너 제한 | CPU, 메모리 | 제한이 너무 낮으면 CPU 스로틀링이 발생할 수 있습니다 또는 OOM |
| 노드 할당 가능 | `kube_node_status_allocatable` | 노드에서 스케줄링 가능한 리소스 | CPU, 메모리 | 클러스터 리소스 할당률 계산에 사용됨 |
| 컨테이너 사용 | `container_cpu_usage_seconds_total`, `container_memory_working_set_bytes` | 실제 CPU 및 메모리 사용량 | 코어/초, 바이트 | 요청/제한이 합리적인지 판단하는 데 사용됨 |

일반 쿼리:

```promql
sum(kube_pod_container_resource_requests{resource="cpu"}) / sum(kube_node_status_allocatable{resource="cpu"}) * 100
```

```promql
sum(kube_pod_container_resource_requests{resource="memory"}) / sum(kube_node_status_allocatable{resource="memory"}) * 100
```

조사 제안: 리소스 계획은 '요청 값'과 '실제 사용 값'을 모두 고려해야 합니다. 요청만 보면 비즈니스 압력을 잘못 판단할 수 있고, 사용만 보면 스케줄링 용량을 간과할 수 있습니다.

### 3.4 워크로드 복제

| 모니터링 차원 | 지표 | 지표 의미 | 공통 범위/단위 | 비정상 성능 |
| --- | --- | --- | --- | --- |
| 배포 복제 | `kube_deployment_status_replicas` | 현재 배포 복제 수 | 단위 | 예상된 복제와 일치하지 않음 |
| 업데이트된 복제본 | `kube_deployment_status_replicas_updated` | 복제본 수가 새 버전으로 업데이트됨 | 단위 | 릴리스 중 장기간 증가 없음 |
| 사용 불가 복제본 | `kube_deployment_status_replicas_unavailable` | 사용 불가 복제본 수 | 단위 | 0보다 크면 서비스 용량 감소 |
| StatefulSet 복제본 | `kube_statefulset_status_replicas` | 현재 StatefulSet 복제본 수 | 단위 | 상태 서비스에서 비정상 복제본 |
| StatefulSet 준비 상태 | `kube_statefulset_status_replicas_ready` | 준비된 StatefulSet 복제본 수 | 단위 | 준비된 수가 예상 복제본보다 적으면 서비스가 불완전함 |

조사 권장 사항: 릴리스 이상이 있을 때 확인 `updated` 그리고 `unavailable`StatefulSet 이상 시, 주의할 사항 PVC, Pod 시작 순서, 노드 친화성.

### 3.5 작업 및 배치 작업

| 모니터링 차원 | 지표 | 지표 의미 | 일반 표준/단위 | 비정상 성능 |
| --- | --- | --- | --- | --- |
| 실행 중인 작업 | `kube_job_status_active` | 현재 활성 작업 수 | 카운트 | 장기간 활성인 경우 작업이 멈춤 상태일 수 있음 |
| 실패한 작업 | `kube_job_status_failed` | 작업 실패 수 | 카운트 | 실패 증가 시 작업 로그 확인 필요 |
| 성공한 작업 | `kube_job_status_succeeded` | 성공적으로 완료된 작업 수 | 카운트 | 작업 완료 여부 판단에 사용 |
| 완료 시간 | `kube_job_status_completion_time` | 작업 완료 시간 | 유닉스 타임스탬프 | 완료 시간이 누락되면 작업이 완료되지 않았음을 나타낼 수 있습니다 |

조사 권장 사항: 배치 작업에 이상이 있는 경우 함께 확인하십시오. `active`, `failed`, 및 `succeeded` 실패만 보는 경우 장시간 멈춘 작업을 놓칠 수 있습니다.

### 3.6 PVC 및 스토리지 객체

| 모니터링 차원 | 지표 | 지표 의미 | 일반 표준/단위 | 비정상 성능 |
| --- | --- | --- | --- | --- |
| PVC 상태 | `kube_persistentvolumeclaim_status_phase` | PVC 바운드, 대기 중 및 기타 상태 | `0/1` | 대기 중 상태는 Pod가 스토리지를 마운트하지 못하게 합니다 |
| PVC 요청된 용량 | `kube_persistentvolumeclaim_resource_requests_storage_bytes` | 스토리지가 요청한 용량 PVC | 바이트 | 용량 계획 및 할당량 관리를 위해 사용됨 |

문제 해결 제안: 상태 저장 서비스가 시작되지 않을 때 Pod를 확인하는 것 외에도 PVC 저장이 바인드되었는지, 저장소 클래스가 사용 가능한지, 그리고 기본 저장소 용량이 부족한지 여부입니다.

### 3.7 APIServer, etcd, 그리고 컨트롤 플레인

| 모니터링 차원 | 지표 | 지표 의미 | 공통 칼리버/단위 | 비정상 성능 |
| --- | --- | --- | --- | --- |
| APIServer 요청 수 | `apiserver_request_total` | 누적 APIServer 요청 수 | 요청/초 | 갑작스러운 요청 급증은 컨트롤러, kubectl또는 비즈니스 구성요소에서 발생할 수 있습니다 |
| APIServer 지연 시간 | `apiserver_request_duration_seconds_bucket` | APIServer 요청 지속 시간 버킷 | P50/P95/P99 | 지연 시간이 증가하면 스케줄링, 배포, 컨트롤러 동기화에 영향을 줍니다 |
| etcd 지연 시간 | `etcd_request_duration_seconds_bucket` | etcd 요청 지속 시간 버킷 | P50/P95/P99 | 느린 etcd는 전체 컨트롤 플레인의 속도를 늦출 수 있음 |
| 대기열 대기 | `workqueue_queue_duration_seconds_bucket` | 컨트롤러 대기열 대기 시간 | 백분위 지속 시간 | 대기열 백로그, 리소스 상태 동기화 속도 저하 |
| 대기열 처리 | `workqueue_work_duration_seconds_bucket` | 컨트롤러 처리 시간 | 백분위 지속 시간 | 컨트롤러 처리 속도 저하 |

일반적인 쿼리:

```promql
sum by (verb, resource) (rate(apiserver_request_total[5m]))
```

```promql
histogram_quantile(0.95, sum(rate(apiserver_request_duration_seconds_bucket[5m])) by (le, verb, resource))
```

조사 권장 사항: 컨트롤 플레인 문제는 일반적으로 배포 지연, Pod 상태 업데이트 지연 및 응답 지연으로 나타납니다. kubectl APIServer 지연 시간과 etcd 지연 시간이 동시에 증가하면 등차적으로 etcd, 디스크 IO, 컨트롤 플레인 노드 부하를 우선 점검하십시오.

## 4. MySQL 모니터링

MySQL 모니터링은 인스턴스 가용성, 연결 압력, SQL 요청량, 느린 쿼리, 캐시 적중, 임시 테이블, 락 대기, 파일 핸들, 네트워크 처리량을 관찰하는 데 사용됩니다.

### 4.1 인스턴스 상태 및 요청량

| 모니터링 차원 | 지표 | 지표 의미 | 공통 범위/단위 | 비정상 현상 |
| --- | --- | --- | --- | --- |
| 인스턴스 활성 | `up` | mysql 익스포터를 수집할 수 있는지 여부 | `0/1` | 언제 `0`인스턴스, 네트워크 또는 익스포터가 비정상인지 여부 |
| 가동 시간 | `mysql_global_status_uptime` | MySQL 운영 시간 | 초 | 감소하면 인스턴스 재시작을 나타냄 |
| 총 쿼리 수 | `mysql_global_status_queries` | 누적 쿼리 수 | 회/초 | QPS 급증은 업무 피크 또는 비정상 요청을 나타낼 수 있음 |
| 질문 | `mysql_global_status_questions` | 클라이언트가 시작한 누적 명령 수 | 회/초 | 요청 압력을 평가하기 위해 쿼리와 함께 확인해야 함 |
| 명령 통계 | `mysql_global_status_commands_total` | 다양한 명령의 누적 횟수 | 회/초 | select, insert, update, delete와 같은 명령 구분 가능 |

일반적인 쿼리: 

```promql
rate(mysql_global_status_queries[5m])
```

```promql
sum by (command) (rate(mysql_global_status_commands_total[5m]))
```

조사 제안: 발생 시 QPS 먼저 명령 분포를 확인 `select` 스캔 유형 지표와 함께 증가하면 인덱스와 느린 쿼리에 주의; 쓰기 명령 증가 시 락 대기, 디스크 IO, 호스트 쓰기 지연 지속 모니터링

### 4.2 연결 및 스레드

| 모니터링 차원 | 지표 | 지표 의미 | 공통 칼리버/단위 | 비정상 성능 |
| --- | --- | --- | --- | --- |
| 현재 연결 | `mysql_global_status_threads_connected` | 현재 연결된 스레드 수 | 개수 | 한계 근접 시 새 연결 실패 가능 |
| 활성 스레드 | `mysql_global_status_threads_running` | 현재 실행 중인 스레드 수 | 개수 | 지속적 증가 시 일반적으로 느린 SQL 실행 또는 락 대기 의미 |
| 역사적 최대 연결 수 | `mysql_global_status_max_used_connections` | 이전에 사용된 최대 연결 수 | 개수 | 최대치에 근접_connections는 연결 풀을 평가할 필요가 있음을 나타냅니다 |
| 최대 연결 | `mysql_global_variables_max_connections` | MySQL 최대 연결 구성 | 개수 | 연결 사용률 계산에 사용 |
| 비정상 클라이언트 | `mysql_global_status_aborted_clients` | 클라이언트 비정상 연결 해제 누적 수 | 횟수/초 | 네트워크 문제, 타임아웃 또는 클라이언트 측 예외 |
| 연결 실패 | `mysql_global_status_aborted_connects` | 연결 실패 총 수 | 회/초 | 인증 오류, 연결 제한, 네트워크 이상 등 |

일반 쿼리:

```promql
mysql_global_status_threads_connected / mysql_global_variables_max_connections * 100
```

조사 제안: 연결 수가 많다고 해서 꼭 데이터베이스가 느리다는 의미는 아니며, 잘못 구성된 애플리케이션 연결 풀 때문일 수도 있습니다. `Threads_running` 오래 동안 높게 유지되는 것은 더 우려할 만하며, 일반적으로 SQL 실행 또는 잠금 대기 문제와 관련이 있습니다.

### 4.3 느린 쿼리, 스캔 및 정렬

| 모니터링 차원 | 지표 | 지표 의미 | 일반 측정/단위 | 비정상 동작 |
| --- | --- | --- | --- | --- |
| 느린 쿼리 | `mysql_global_status_slow_queries` | 누적 느린 쿼리 수 | 횟수/초 | 증가는 더 많은 느린 쿼리를 의미합니다 SQL |
| 전체 조인 스캔 | `mysql_global_status_select_full_join` | 인덱스 없는 조인 수 | 횟수/초 | 조인 조건 인덱스가 없을 수 있음을 나타냅니다 |
| 전체 테이블 스캔 | `mysql_global_status_select_scan` | 전체 테이블 스캔 수 | 횟수/초 | 대형 테이블에서의 전체 스캔은 인스턴스를 느리게 만들 수 있습니다 |
| 정렬 병합 | `mysql_global_status_sort_merge_passes` | 정렬 시 여러 번 병합이 필요한 횟수 | 횟수/초 | 정렬 버퍼가 부족하거나 정렬할 데이터가 너무 많음 |

조사 제안: 느린 쿼리가 증가할 때 비즈니스 릴리스 시간 및 변경 기록과 비교 확인 SQL 스캔 및 정렬 지표가 증가하면 보통 느린 로그, 실행 계획, 인덱스 설계를 참조

### 4.4 InnoDB 버퍼 풀

| 모니터링 차원 | 지표 | 지표 의미 | 공통 칼리버/단위 | 비정상 성능 |
| --- | --- | --- | --- | --- |
| 버퍼 풀 크기 | `mysql_global_variables_innodb_buffer_pool_size` | InnoDB 버퍼 풀 구성 크기 | 바이트 | 너무 작으면 디스크 읽기가 증가 |
| 버퍼 풀 페이지 | `mysql_global_status_buffer_pool_pages` | 버퍼 풀 페이지의 다양한 유형 수 | 페이지 | 더티, 프리, 데이터 및 기타 페이지를 모니터링하는 데 사용됨 |
| 페이지 크기 | `mysql_global_status_innodb_page_size` | InnoDB 페이지 크기 | 바이트 | 페이지 수를 용량으로 변환하는 데 사용됨 |

조사 제안: 버퍼 풀 히트율이 낮으면 데이터베이스가 디스크에 더 많이 접근합니다. 노드의 디스크 읽기 처리량, 읽기, iowait과 함께 평가할 필요가 있습니다. IOPS

### 4.5 임시 테이블, 테이블 캐시, 파일 핸들

| 모니터링 차원 | 지표 | 지표 의미 | 공통 칼리버/단위 | 비정상 성능 |
| --- | --- | --- | --- | --- |
| 임시 테이블 | `mysql_global_status_created_tmp_tables` | 생성된 임시 테이블 총 수 | 회/초 | 쿼리 복잡성 증가 |
| 디스크 임시 테이블 | `mysql_global_status_created_tmp_disk_tables` | 생성된 디스크 임시 테이블 총 수 | 횟수/초 | 디스크 IO 압력 증가, SQL 속도가 느려질 수 있음 |
| 임시 파일 | `mysql_global_status_created_tmp_files` | 생성된 임시 파일 총 수 | 횟수/초 | 임시 파일 증가 |
| 테이블 잠금 즉시 | `mysql_global_status_table_locks_immediate` | 테이블 잠금이 즉시 획득된 횟수 | 횟수/초 | 일반 참조 지표 |
| 테이블 잠금 대기 | `mysql_global_status_table_locks_waited` | 테이블 잠금을 대기한 횟수 | 횟수/초 | 잠금 경쟁 증가 |
| 테이블 캐시 적중 | `mysql_global_status_table_open_cache_hits` | 테이블 오픈 캐시 적중 횟수 | 횟수/초 | 적중률이 낮으면 테이블이 자주 열릴 수 있음 |
| 테이블 캐시 미스 | `mysql_global_status_table_open_cache_misses` | 테이블 오픈 캐시 미스 수 | 횟수/초 | 테이블 캐시 평가 필요 |
| 테이블 캐시 오버플로 | `mysql_global_status_table_open_cache_overflows` | 테이블 오픈 캐시 오버플로 수 | 횟수/초 | 부적절한 구성 또는 너무 많은 테이블 |
| 오픈 테이블 | `mysql_global_status_open_tables` | 현재 오픈 테이블 수 | 개 | 캐시 한계에 접근할수록 위험 증가 |
| 테이블 캐시 구성 | `mysql_global_variables_table_open_cache` | 테이블_열기_캐시 구성 값 | 개 | 사용률 계산에 사용됨 |
| 오픈 파일 | `mysql_global_status_open_files` | 현재 오픈 파일 수 | 개 | 영향 가능 SQL 파일 한계에 접근할 때 실행 |
| 파일 제한 | `mysql_global_variables_open_files_limit` | MySQL 파일 핸들 제한 | 개 | 파일 핸들 사용률을 계산하는 데 사용됨 |

문제 해결 제안: 임시 테이블, 잠금 대기, 테이블 캐시 미스는 종종 느린 쿼리와 함께 나타납니다. 디스크 임시 테이블이 증가하면 노드 쓰기 IO, 디스크 지연 시간 및 SQL 정렬/그룹화에 주의하십시오.

### 4.6 네트워크 처리량

| 모니터링 차원 | 지표 | 지표 의미 | 공통 단위 | 비정상 성능 |
| --- | --- | --- | --- | --- |
| 수신 트래픽 | `mysql_global_status_bytes_received` | 누적 MySQL 수신된 바이트 | 바이트/초 | 요청 본문 또는 쓰기 트래픽 증가 |
| 송신 트래픽 | `mysql_global_status_bytes_sent` | 누적 전송 바이트 MySQL | 바이트/초 | 대형 쿼리, 전체 테이블 스캔 및 대량 내보내기는 아웃바운드 트래픽을 증가시킵니다 |

일반적인 쿼리:

```promql
rate(mysql_global_status_bytes_received[5m])
```

```promql
rate(mysql_global_status_bytes_sent[5m])
```

조사 제안: 발생 시 MySQL 아웃바운드 트래픽이 갑자기 증가하면 일반적으로 대형 결과 집합, 내보내기 작업 및 페이지 매김 없는 쿼리에 주의해야 합니다.

## 5. MongoDB 모니터링

MongoDB 모니터링은 인스턴스 상태, 연결 수, 작업량, 쿼리 스캔, 메모리 사용량, 네트워크 처리량 및 복제 버퍼 상태를 관찰하는 데 사용됩니다.

### 5.1 인스턴스 및 연결

| 모니터링 차원 | 지표 | 지표 의미 | 일반 측정/단위 | 비정상 성능 |
| --- | --- | --- | --- | --- |
| 인스턴스 활성 | `up` | Mongo Exporter가 데이터를 수집할 수 있는지 여부 | `0/1` | 만약 `0`, 인스턴스 또는 Exporter에 이상이 있습니다 |
| 가동 시간 | `mongodb_ss_uptime` | MongoDB 운영 시간 | 초 | 작은 값은 인스턴스 재시작을 나타냅니다 |
| 연결 수 | `mongodb_ss_connections` | 현재 연결 관련 통계 | 개수 | 비정상적으로 높은 연결 수는 연결 풀 또는 업무 피크를 나타낼 수 있습니다 |

조사 제안: 연결 수가 증가할 경우, 먼저 업무 피크 여부, 연결 풀 구성 변경 여부, 또는 비정상적인 클라이언트 재연결을 확인하십시오.

### 5.2 운영 및 문서 처리

| 모니터링 차원 | 지표 | 지표 의미 | 일반 측정/단위 | 비정상 성능 |
| --- | --- | --- | --- | --- |
| 운영 수 | `mongodb_ss_opcounters` | 삽입, 조회, 업데이트, 삭제 등의 누적 운영 수 | 회/초 | 특정 유형의 운영이 갑자기 증가하면 업무 접근 패턴의 변화를 나타냅니다 |
| 문서 처리 | `mongodb_ss_metrics_document` | 삽입, 업데이트, 삭제, 반환 등 문서의 누적 수 | 회/초 | 반환 값이 실제로 필요한 것보다 훨씬 높으면 결과 세트가 너무 클 수 있습니다 |
| 인덱스 항목 스캔 | `mongodb_ss_metrics_queryExecutor_scanned` | 쿼리 중 스캔된 인덱스 항목 수 | 회/초 | 과도한 스캔은 잘못된 인덱싱을 나타낼 수 있습니다 |
| 문서 스캔 | `mongodb_ss_metrics_queryExecutor_scannedObjects` | 쿼리 중 스캔된 문서 수 | 회/초 | 문서 스캔이 많다는 것은 쿼리 효율이 낮음을 나타냅니다 |

일반적인 문의: 

```promql
sum by (type) (rate(mongodb_ss_opcounters[5m]))
```

조사 권장 사항: 일반적인 징후는 MongoDB 느린 쿼리는 스캔된/스캔된 객체 증가와 관련이 있습니다. 느린 로그 및 인덱스 히트와 결합하여 분석할 필요가 있습니다.

### 5.3 메모리, 네트워크, 및 디스크

| 모니터링 차원 | 지표 | 지표 의미 | 공통 단위/측정 | 비정상 현상 |
| --- | --- | --- | --- | --- |
| 상주 메모리 | `mongodb_ss_mem_resident` | MongoDB 상주 메모리 | MB 또는 바이트 | 지속적인 증가는 호스트 메모리를 확인해야 합니다 |
| 가상 메모리 | `mongodb_ss_mem_virtual` | MongoDB 가상 메모리 | MB 또는 바이트 | 단독 증가는 반드시 실제 압력을 나타내지는 않습니다 |
| 수신 트래픽 | `mongodb_ss_network_bytesIn` | MongoDB 누적 수신 바이트 | 바이트/초 | 쓰거나 요청 트래픽 증가 |
| 발신 트래픽 | `mongodb_ss_network_bytesOut` | MongoDB 누적 송신 바이트 | 바이트/초 | 큰 쿼리 또는 내보내기 작업으로 인한 송신 트래픽 증가 |
| 호스트 읽기 IO | `node_disk_reads_completed_total` | 읽기 IOPS 해당 노드에서 MongoDB 위치함 | 회/초 | 쿼리 스캔으로 인한 읽기 IO 증가 |
| 호스트 쓰기 IO | `node_disk_writes_completed_total` | 쓰기 IOPS 해당 노드에서 MongoDB 위치함 | 횟수/초 | 쓰기 또는 저널 압력 증가 | 

문제 해결 제안: MongoDB 메모리 및 디스크 성능은 노드의 메모리 및 디스크 IO와 함께 고려해야 합니다. 인스턴스 메트릭을 호스트 디스크 읽기/쓰기와 함께 보면 자체가 느린지 아니면 기본 리소스가 느린지 판단하기가 더 쉽습니다. MongoDB 자체가 느린지 아니면 기본 리소스가 느린지 

### 5.4 복제 버퍼 

| 모니터링 차원 | 지표 | 지표 의미 | 일반 측정/단위 | 비정상 성능 | 
| --- | --- | --- | --- | 
| 복제 버퍼 크기 | `mongodb_ss_metrics_repl_buffer_sizeBytes` | 복제 버퍼 크기 | 바이트 | 버퍼가 계속해서 증가하면 복제 소비가 적시에 이루어지지 않음을 나타냅니다 | 

문제 해결 제안: 비정상적인 복제 버퍼는 일반적으로 슬레이브의 처리 능력, 네트워크 또는 디스크 쓰기와 관련이 있습니다. 복제 지연, 노드 네트워크, 디스크 쓰기 지표와 함께 분석해야 합니다. 

## 6. Redis 모니터링 

Redis 모니터링은 인스턴스 가용성, 연결 수, 명령 처리, 메모리 수준, 키스페이스, 히트율, 제거, 네트워크 처리량 등을 관찰하는 데 사용됩니다. 

### 6.1 인스턴스 및 클라이언트 

| 모니터링 차원 | 지표 | 지표 의미 | 일반 측정/단위 | 비정상 성능 | 
| --- | --- | --- | --- | --- |
| 인스턴스 활성 | `up` | 실행 여부 Redis exporter 수집 가능 | `0/1` | 언제 `0`, 인스턴스 또는 Exporter에 이상이 있습니다 |
| 가동 시간 | `redis_uptime_in_seconds` | Redis 운영 시간 | 초 | 감소는 인스턴스 재시작을 나타냅니다 |
| 클라이언트 연결 | `redis_connected_clients` | 현재 클라이언트 연결 수 | 개수 | 갑작스러운 증가는 연결 풀 문제 또는 재연결 폭주를 나타낼 수 있습니다 |

### 6.2 명령어, 메모리 및 키스페이스

| 모니터링 차원 | 지표 | 지표 의미 | 공통 단위 | 비정상 동작 |
| --- | --- | --- | --- | --- |
| 처리된 명령 | `redis_commands_processed_total` | 총 Redis 처리된 명령 수 | 회/초 | 갑작스러운 QPS 급증은 인스턴스를 증가시킬 수 있습니다 CPU |
| 명령 분류 | `redis_commands_total` | 유형별 총 명령 수 | 회/초 | get, set, del 등 명령 변화 확인 가능 |
| 사용 메모리 | `redis_memory_used_bytes` | 현재 Redis 메모리 사용량 | 바이트 | 최대 메모리에 접근하면 삭제가 발생할 수 있음 |
| 최대 메모리 | `redis_memory_max_bytes` | Redis maxmemory 구성 | 바이트 | 메모리 사용률 계산에 사용됨 |
| 키 수 | `redis_db_keys` | 각 DB의 키 수 | 개수 | 비정상적인 키 증가는 만료 없는 캐시 또는 쓰기 이상을 나타낼 수 있음 |
| 만료 키 | `redis_db_keys_expiring` | 만료가 설정된 키 수 | 개수 | 비율이 낮으면 캐시 수명 주기에 주의 필요 |

일반 쿼리:

```promql
rate(redis_commands_processed_total[5m])
```

```promql
redis_memory_used_bytes / redis_memory_max_bytes * 100
```

### 6.3 히트율, 삭제, 네트워크

| 모니터링 차원 | 지표 | 지표 의미 | 공통 칼리버/단위 | 비정상 성능 |
| --- | --- | --- | --- | --- |
| 히트 수 | `redis_keyspace_hits_total` | 키 히트 총 수 | 회/초 | 미스와 함께 히트율 계산 |
| 미스 수 | `redis_keyspace_misses_total` | 키 미스 총 수 | 회/초 | 미스 증가 시 원본 소스 압력 증가 가능 |
| 만료된 키 | `redis_expired_keys_total` | 만료된 키 총 수 | 회/초 | 만료 스톰은 다음을 유발할 수 있음 CPU 지터 |
| 삭제된 키 | `redis_evicted_keys_total` | 삭제된 키 총 수 | 회/초 | 증가는 메모리 압력 또는 부족한 maxmemory를 나타냄 |
| 수신 트래픽 | `redis_net_input_bytes_total` | 받은 총 바이트 수 Redis | 바이트/초 | 쓰거나 요청 트래픽 증가 |
| 송신 트래픽 | `redis_net_output_bytes_total` | 전송된 총 바이트 수 Redis | 바이트/초 | 큰 값이나 배치 읽기로 인해 발생한 높은 아웃바운드 트래픽 |

일반 쿼리:

```promql
rate(redis_keyspace_hits_total[5m]) / (rate(redis_keyspace_hits_total[5m]) + rate(redis_keyspace_misses_total[5m])) * 100
```

```promql
rate(redis_evicted_keys_total[5m])
```

조사 권장 사항: 대상 Redis, 메모리 및 제거 위험에 집중하세요. 히트율 감소는 백엔드 데이터베이스에 부담을 전가시킵니다. 제거 증가가 나타나면 캐시 용량이나 제거 전략을 평가할 필요가 있습니다.

## 7. Kafka 모니터링

Kafka 모니터링은 브로커 수, 토픽/파티션 상태, 생산 및 소비 오프셋, 컨슈머 그룹 지연, 멤버 수, 복제 상태를 관찰하는 데 사용됩니다.

### 7.1 브로커, 토픽 및 파티션

| 모니터링 차원 | 지표 | 지표 의미 | 공통 칼리버/단위 | 비정상 성능 |
| --- | --- | --- | --- | --- |
| 브로커 수 | `kafka_brokers` | 현재 확인 가능한 브로커 수 | 개 | 숫자가 감소하면 브로커를 사용할 수 없거나 익스포터에 접근할 수 없음을 나타냅니다. |
| 토픽 파티션 | `kafka_topic_partitions` | 토픽의 파티션 수 | 개 | 파티션 변경은 동시성과 소비 능력에 영향을 미칩니다. |
| 현재 파티션 오프셋 | `kafka_topic_partition_current_offset` | 파티션의 최신 오프셋 | 오프셋 / 성장률 | 진행 중인 생산 쓰기 동안 지속적으로 증가해야 합니다. |
| 파티션 최솟값 오프셋 | `kafka_topic_partition_oldest_offset` | 파티션 최솟값 오프셋 | 오프셋 | 보관된 데이터 범위를 관찰하는 데 사용됩니다. |

일반적인 쿼리: 

```promql
sum by (topic) (rate(kafka_topic_partition_current_offset[5m]))
```

조사 제안: 생산 속도가 비정상적일 때 먼저 토픽의 현재 오프셋 증가를 확인하십시오. 비즈니스에서 쓰기가 있다고 확인하지만 오프셋이 증가하지 않으면, 프로듀서 측 오류, 브로커 상태 및 토픽 구성을 확인하십시오.

### 7.2 소비자 그룹 및 지연

| 모니터링 차원 | 지표 | 지표 의미 | 일반 측정/단위 | 비정상 성능 |
| --- | --- | --- | --- | --- |
| 소비 오프셋 | `kafka_consumergroup_current_offset` | 소비자 그룹이 현재 소비한 오프셋 | 오프셋 / 성장률 | 성장이 없으면 소비가 중단되었거나 멈춘 상태임을 나타냅니다 |
| 파티션 지연 | `kafka_consumergroup_lag` | 파티션에서 소비자 그룹의 백로그 | 개수 | 지연 증가 = 소비가 생산을 따라가지 못함 |
| 그룹 총 지연 | `kafka_consumergroup_lag_sum` | 소비자 그룹의 전체 백로그 | 개수 | 총 지연의 지속적 증가 = 비즈니스 지연 확대를 나타냄 |
| 그룹 구성원 | `kafka_consumergroup_members` | 소비자 그룹 내 구성원 수 | 개수 | 구성원 수 감소는 소비 능력 감소로 이어질 수 있음 |

일반 쿼리:

```promql
sum by (consumergroup, topic) (kafka_consumergroup_lag)
```

```promql
sum by (consumergroup, topic) (rate(kafka_consumergroup_current_offset[5m]))
```

문제 해결 제안: 핵심 비즈니스 지표 Kafka 은 지연입니다. 지연이 증가하면 먼저 소비자 멤버 수가 감소했는지 확인하고, 그 다음 소비율이 떨어졌는지 확인하며, 마지막으로 애플리케이션 처리 시간, 하류 종속성, 브로커 IO를 확인하십시오.

### 7.3 레플리카 및 ISR

| 모니터링 차원 | 지표 | 지표 의미 | 일반 측정/단위 | 비정상 성능 |
| --- | --- | --- | --- | --- |
| 레플리카 수 | `kafka_topic_partition_replicas` | 파티션 레플리카 수 | 개수 | 예상보다 적은 레플리카는 신뢰성을 낮춥니다 |
| ISR 복제본 | `kafka_topic_partition_in_sync_replica` | In-Sync 파티션 레플리카 수 | 개수 | 하락은 ISR 지연된 레플리카 또는 브로커 문제를 나타냅니다 |
| 선호 리더 | `kafka_topic_partition_leader_is_preferred` | 리더가 선호 레플리카인지 여부 | `0/1` | 장기적인 불균형은 일부 브로커에 높은 부하를 유발할 수 있습니다 |

문제 해결 제안: 하락은 ISR 일반적인 지연보다 더 큰 신뢰성 위험을 나타냅니다. 브로커 상태, 네트워크, 디스크 쓰기 지연, 레플리카 동기화를 확인하십시오.

## 8. MinIO 객체 저장소 모니터링

MinIO 모니터링은 객체 저장소 클러스터의 가용성, 노드 및 디스크 상태, 버킷 용량, S3 요청, 오류, 트래픽, 프로세스 핸들, 복구 작업 활동을 관찰하는 데 사용됩니다. 

### 8.1 클러스터 노드 및 디스크 

| 모니터링 차원 | 지표 | 지표 의미 | 공통 칼리버/단위 | 비정상 현상 |
| --- | --- | --- | --- | --- |
| 온라인 노드 | `minio_cluster_nodes_online_total` | 온라인 노드 수 MinIO 노드 수 | 개 | 숫자가 감소하면 노드를 사용할 수 없음을 나타냅니다 |
| 오프라인 노드 | `minio_cluster_nodes_offline_total` | 오프라인 수 MinIO 노드 수 | 개 | 0보다 크면 클러스터 가용성에 주의가 필요합니다 |
| 온라인 디스크 | `minio_cluster_disk_online_total` | 온라인 디스크 수 | 개 | 디스크 감소는 중복성과 쓰기 기능에 영향을 미칩니다 |
| 오프라인 디스크 | `minio_cluster_disk_offline_total` | 오프라인 디스크 수 | 개 | 0보다 크면 디스크 또는 마운트 문제 해결이 필요합니다 |
| 사용 가능 용량 | `minio_cluster_capacity_usable_free_bytes` | 클러스터 사용 가능 용량 | 바이트 | 지속적인 감소는 용량 부족 위험을 나타냅니다 |

문제 해결 제안: 오브젝트 스토리지의 경우 먼저 노드와 디스크의 온라인 상태를 확인하십시오. 오프라인 디스크를 단순 수량으로만 평가하지 말고, 제거 코드 중복 전략과 결합하여 위험을 판단해야 합니다. 

### 8.2 버킷 용량 및 오브젝트 수

| 모니터링 차원 | 지표 | 지표 의미 | 공통 칼리버/단위 | 비정상 성능 |
| --- | --- | --- | --- | --- |
| 버킷 용량 | `bucket_usage_size` | 버킷 사용 용량 | 바이트 | 빠른 용량 증가, 확장 평가 필요 |
| 오브젝트 수 | `bucket_objects_count` | 버킷 내 오브젝트 수 | 카운트 | 작은 오브젝트가 많으면 메타데이터 및 스캔 부담 증가 |
| 오브젝트 크기 분포 | `minio_bucket_objects_size_distribution` | 버킷 내 오브젝트 크기 분포 | 버킷별 통계 | 객체 분포의 변화는 저장소 및 요청 성능에 영향을 미칩니다 |

일반 쿼리:

```promql
sum by (bucket) (bucket_usage_size)
```

```promql
sum by (bucket) (bucket_objects_count)
```

조사 권장 사항: 용량 증가는 버킷별로 별도 분석되어야 합니다. 객체 수가 빠르게 증가하지만 용량 증가가 뚜렷하지 않은 경우, 이는 보통 작은 객체의 증가 때문입니다. 라이프사이클 정리와 비즈니스 쓰기 패턴에 주의를 기울여야 합니다.

### 8.3 S3 요청, 오류 및 트래픽

| 모니터링 차원 | 지표 | 지표 의미 | 일반 측정/단위 | 비정상 성능 |
| --- | --- | --- | --- | --- |
| S3 요청 수 | `minio_s3_requests_total` | 누적 요청 수 S3 API 요청 | 회/초 | 요청의 급격한 증가, 비즈니스 피크나 재시도일 수 있음 |
| S3 오류 수 | `minio_s3_requests_errors_total` | 누적 요청 수 S3 API 오류 | 회/초 | 객체 읽기/쓰기 영향을 주는 오류율 증가 |
| 수신 트래픽 | `minio_s3_traffic_received_bytes` | 누적 S3 수신된 바이트 | 바이트/초 | 업로드 트래픽 증가 |
| 전송 트래픽 | `minio_s3_traffic_sent_bytes` | 누적 S3 전송된 바이트 | 바이트/초 | 다운로드 또는 오리진 조회 트래픽 증가 |

일반 쿼리:

```promql
sum by (api) (rate(minio_s3_requests_total[5m]))
```

```promql
sum(rate(minio_s3_requests_errors_total[5m])) / sum(rate(minio_s3_requests_total[5m])) * 100
```

조사 권장 사항: 에러율이 증가하면 먼저 유형별로 분류한 후 해당 버킷, 노드 디스크 상태 및 네트워크 트래픽을 확인합니다. S3  API 

### 8.4 노드 프로세스, 파일 핸들 및 IO

| 모니터링 차원 | 지표 | 지표 의미 | 공통 칼리버/단위 | 비정상 현상 |
| --- | --- | --- | --- | --- |
| 노드 디스크 사용량 | `minio_node_disk_used_bytes` | 노드의 디스크 사용량 MinIO 노드 | 바이트 | 단일 노드 용량 불균형 |
| 열린 파일 핸들 | `minio_node_file_descriptor_open_total` | 프로세스가 연 파일 핸들의 수 MinIO 프로세스 | 카운트 | 시스템 한도에 접근할 때 요청이 실패할 수 있음 |
| 읽기 시스템 호출 | `minio_node_syscall_read_total` | 누적 읽기 시스템 호출 횟수 | 횟수/초 | 읽기 호출의 비정상적 증가 |
| 쓰기 시스템 호출 | `minio_node_syscall_write_total` | 누적 쓰기 시스템 호출 횟수 | 횟수/초 | 쓰기 호출의 비정상적 증가 |
| 프로세스 읽기 바이트 | `minio_node_io_rchar_bytes` | 프로세스가 읽은 누적 바이트 | 바이트/초 | 읽기 부하 증가 |
| 프로세스 쓰기 바이트 | `minio_node_io_wchar_bytes` | 프로세스가 쓴 누적 바이트 | 바이트/초 | 쓰기 부하 증가 |
| 고루틴 수 | `minio_node_go_routine_total` | 연속 증가하는 고루틴 수 MinIO 프로세스 | 카운트 | 지속적 성장은 요청 적체 또는 누수를 나타낼 수 있음 |
| 시작 시간 | `minio_node_process_starttime_seconds` | MinIO 프로세스 시작 시간 | 유닉스 타임스탬프 | 변경 사항은 프로세스 재시작을 나타냄 |

조사 제안: 성능 문제의 경우 MinIO 요청, 노드 디스크, 프로세스 IO 및 고루틴을 함께 고려 S3 요청량만 높다고 해서 반드시 비정상적인 것은 아님; 오류율, IO 지연, 디스크 오프라인 상태가 더 명확한 위험 신호임

### 8.5 복구 및 사용 활동

| 모니터링 차원 | 지표 | 지표 의미 | 일반 표준/단위 | 비정상 동작 |
| --- | --- | --- | --- | --- |
| 복구 활동 | `minio_heal_time_last_activity_nano_seconds` | 마지막 힐 활동 시간 | 나노초 타임스탬프 | 긴 시간 또는 잦은 힐은 디스크 건강 상태에 주의를 기울여야 함 |
| 사용 활동 | `minio_usage_last_activity_nano_seconds` | 마지막 사용 스캔 활동 시간 | 나노초 타임스탬프 | 비정상적인 사용 스캔은 용량 통계 정확도에 영향을 줄 수 있음 |

조사 제안: 노드 또는 디스크의 비정상 복구 후, 힐 활동이 정상적으로 진행되는지 모니터링하여 객체 중복이 장기간 위험 상태로 남지 않도록 함.

## 9. Elasticsearch 모니터링

Elasticsearch 모니터링은 검색 클러스터의 건강, 노드 수, 샤드 분포, 인덱스 읽기/쓰기 작업, 캐시, JVM스레드 풀, 디스크, 네트워크를 관찰하는 데 사용됨. ES 실패는 일반적으로 단일 지표로 결정되지 않으며; 더 흔히는 "샤드 이상   JVM 압력   스레드 풀 거부   디스크 워터마크"가 함께 나타남.

### 9.1 클러스터 상태 및 노드

| 모니터링 차원 | 지표 | 지표 의미 | 일반 측정/단위 | 비정상 동작 |
| --- | --- | --- | --- | --- |
| 클러스터 상태 | `elasticsearch_cluster_health_status` | ES 클러스터 건강 상태 | 상태 값 | 노란색/빨간색은 복제본 또는 기본 샤드 이상을 나타냄 |
| 노드 수 | `elasticsearch_cluster_health_number_of_nodes` | 클러스터 노드 수 | 카운트 | 노드 수 감소는 노드 오프라인을 나타낼 수 있음 |
| 데이터 노드 수 | `elasticsearch_cluster_health_number_of_data_nodes` | 클러스터의 데이터 노드 수 | 카운트 | 데이터 노드 감소는 샤드 용량과 읽기/쓰기 성능에 영향을 미칩니다 |
| 대기 중인 작업 | `elasticsearch_cluster_health_number_of_pending_tasks` | 클러스터의 대기 중인 작업 수 | 카운트 | 지속적인 증가가 보이면 마스터 또는 클러스터 상태 업데이트가 느립니다 |
| 활성 기본 샤드 | `elasticsearch_cluster_health_active_primary_shards` | 활성 기본 샤드 수 | 개 | 감소하면 높은 위험, 인덱스 가용성에 영향을 줄 수 있음 |
| 활성 샤드 | `elasticsearch_cluster_health_active_shards` | 활성 샤드 총 수 | 개 | 감소하면 샤드가 완전히 복구되지 않음을 나타냄 |
| 초기화 중인 샤드 | `elasticsearch_cluster_health_initializing_shards` | 초기화 중인 샤드 수 | 개 | 오래 동안 감소가 없으면 복구가 느리다는 의미 |
| 이동 중인 샤드 | `elasticsearch_cluster_health_relocating_shards` | 이동 중인 샤드 수 | 개 | 너무 많은 이동은 네트워크와 디스크 부담 증가 |
| 할당되지 않은 샤드 | `elasticsearch_cluster_health_unassigned_shards` | 할당되지 않은 샤드 수 | 개 | 0보다 크면 샤드가 노드에 할당되지 않음을 의미 |
| 지연된 할당되지 않은 샤드 | `elasticsearch_cluster_health_delayed_unassigned_shards` | 지연된 할당되지 않은 샤드 수 | 개 | 노드 오프라인 후 재할당 대기 중 |

일반적인 쿼리: 

```promql
elasticsearch_cluster_health_status
```

```promql
elasticsearch_cluster_health_unassigned_shards
```

조사 제안: ES는 먼저 상태와 할당되지 않은 샤드를 확인해야 합니다. 빨간색 상태는 기본 샤드를 우선적으로 처리해야 하며, 노란색은 대부분 할당되지 않은 복제본으로 인해 발생하며, 오랫동안 방치할 수도 없습니다. 

### 9.2 디스크 용량 및 파일 시스템

| 모니터링 차원 | 지표 | 지표 의미 | 일반 측정 / 단위 | 비정상 성능 |
| --- | --- | --- | --- | --- |
| 총 데이터 디스크 | `elasticsearch_filesystem_data_size_bytes` | ES 데이터 디렉토리의 총 용량 | 바이트 | 디스크 사용률 계산에 사용됨 |
| 사용 가능한 데이터 디스크 | `elasticsearch_filesystem_data_available_bytes` | ES 데이터 디렉토리의 사용 가능한 용량 | 바이트 | 사용 가능한 공간 부족은 샤드 마이그레이션 또는 쓰기 제한을 발생시킬 수 있습니다 |

일반적인 쿼리:

```promql
(1 - elasticsearch_filesystem_data_available_bytes / elasticsearch_filesystem_data_size_bytes) * 100
```

조사 제안: ES는 디스크 사용에 매우 민감합니다. 디스크 사용률이 너무 높으면 샤드 마이그레이션, 읽기 전용 인덱스 또는 쓰기 실패가 발생할 수 있습니다. 인덱스 증가, 보존 정책, 노드 디스크 분포를 모니터링할 필요가 있습니다.

### 9.3 문서, 인덱스, 삭제

| 모니터링 차원 | 지표 | 지표 의미 | 공통 단위 | 비정상 동작 |
| --- | --- | --- | --- | --- |
| 문서 수 | `elasticsearch_indices_docs` | 현재 문서 수 | 개수 | 문서가 급격히 연속적으로 증가하면 용량 평가가 필요합니다 |
| 삭제된 문서 | `elasticsearch_indices_docs_deleted` | 삭제된 문서 수 | 개수 | 높은 삭제율은 병합 압력을 유발할 수 있습니다 |
| 인덱스 쓰기 수 | `elasticsearch_indices_indexing_index_total` | 인덱스 작업 누적 수 | 횟수/초 | 쓰기 급증은 CPU디스크 및 새로고침 부담을 증가시킵니다 |
| 인덱스 쓰기 시간 | `elasticsearch_indices_indexing_index_time_seconds_total` | 인덱스 작업의 누적 시간 | 초/초 | 쓰기 시간이 증가하면 쓰기 경로가 느려집니다 |
| 삭제 작업 수 | `elasticsearch_indices_indexing_delete_total` | 삭제 작업의 누적 횟수 | 횟수/초 | 삭제 급증은 세그먼트 병합 부담을 초래할 수 있습니다 |
| 삭제 작업 지속 시간 | `elasticsearch_indices_indexing_delete_time_seconds_total` | 삭제 작업의 누적 지속 시간 | 초/초 | 삭제 지속 시간 증가 |

일반적인 쿼리:

```promql
sum by (cluster) (rate(elasticsearch_indices_indexing_index_total[5m]))
```

```promql
rate(elasticsearch_indices_indexing_index_time_seconds_total[5m]) / rate(elasticsearch_indices_indexing_index_total[5m])
```

문제 해결 권장 사항: 쓰기가 느린 경우 단순히 쓰기만 확인하지 마세요 QPS. 새로고침, 병합, 트랜스로그, 스레드 풀 거부, 디스크 IO도 고려해야 합니다.

### 9.4 쿼리 및 Get 요청

| 모니터링 차원 | 지표 | 지표 의미 | 일반 측정/단위 | 비정상 동작 |
| --- | --- | --- | --- | --- |
| 쿼리 요청 수 | `elasticsearch_indices_search_query_total` | 검색 쿼리 누적 수 | 회/초 | 쿼리 급증 |
| 쿼리 지연 시간 | `elasticsearch_indices_search_query_time_seconds` | 검색 쿼리 누적 시간 | 초/초 | 평균 쿼리 지연 시간 증가 |
| 가져오기 요청 수 | `elasticsearch_indices_search_fetch_total` | 검색 가져오기 단계의 누적 수 | 회/초 | 큰 결과 세트는 가져오기 수를 증가시킬 수 있습니다 |
| 가져오기 지연 시간 | `elasticsearch_indices_search_fetch_time_seconds` | 검색 가져오기 누적 시간 | 초/초 | 느린 조회는 일반적으로 큰 결과 집합, 디스크 또는 네트워크와 관련이 있습니다 |
| 조회 요청 수 | `elasticsearch_indices_get_exists_total`, `elasticsearch_indices_get_missing_total` | 조회 성공 및 실패 누적 횟수 | 회/초 | 실패 증가가 비즈니스가 존재하지 않는 문서에 접근하고 있음을 나타낼 수 있습니다 |
| 조회 시간 | `elasticsearch_indices_get_time_seconds`, `elasticsearch_indices_get_exists_time_seconds`, `elasticsearch_indices_get_missing_time_seconds` | 조회 요청의 누적 시간 | 초/초 | 느린 조회는 읽기 경로에 대한 압력이 증가하고 있음을 나타냅니다 |

일반적인 쿼리: 

```promql
rate(elasticsearch_indices_search_query_time_seconds[5m]) / rate(elasticsearch_indices_search_query_total[5m])
```

```promql
rate(elasticsearch_indices_search_fetch_time_seconds[5m]) / rate(elasticsearch_indices_search_fetch_total[5m])
```

문제 해결 권장 사항: 느린 쿼리는 쿼리와 조회를 구분해야 합니다. 느린 쿼리는 쿼리 조건, 인덱스 구조 및 샤드 압력과 더 관련이 있으며; 느린 조회는 반환 필드가 많거나, 결과 집합이 크거나, 디스크 읽기가 느릴 때 더 일반적입니다.

### 9.5 세그먼트, 병합, 새로 고침 및 트랜스로그

| 모니터링 차원 | 지표 | 지표 의미 | 공통 칼리버/단위 | 비정상 증상 |
| --- | --- | --- | --- | --- |
| 세그먼트 수 | `elasticsearch_indices_segments_count` | 현재 세그먼트 수 | 개수 | 세그먼트가 너무 많으면 쿼리와 메모리에 영향을 줄 수 있습니다 |
| 세그먼트 메모리 | `elasticsearch_indices_segments_memory_bytes` | 세그먼트가 차지하는 메모리 | 바이트 | 연속적인 증가는 압박을 줄 수 있습니다 JVM |
| 병합 수 | `elasticsearch_indices_merges_total` | 병합 작업의 누적 수 | 횟수/초 | 자주 발생하는 병합은 높은 쓰기 또는 삭제 압력을 나타냅니다 |
| 병합 중 문서 수 | `elasticsearch_indices_merges_docs_total` | 병합으로 처리된 문서 누적 수 | 개수/초 | 증가하는 병합 작업 부하 |
| 병합 데이터 용량 | `elasticsearch_indices_merges_total_size_bytes_total` | 병합으로 처리된 데이터 누적 | 바이트/초 | 대형 병합은 디스크 IO를 포화시킬 수 있음 |
| 병합 시간 | `elasticsearch_indices_merges_total_time_seconds_total` | 병합에 소요된 누적 시간 | 초/초 | 느린 병합은 쓰기 및 쿼리 성능에 영향을 줄 수 있음 |
| 갱신 횟수 | `elasticsearch_indices_refresh_total` | 갱신 누적 수 | 회/초 | 자주 발생하는 갱신은 오버헤드를 증가시킴 |
| 갱신 시간 | `elasticsearch_indices_refresh_time_seconds_total` | 갱신 누적 시간 | 초/초 | 느린 갱신은 실시간에 가까운 가시성에 영향을 미침 |
| 플러시 횟수 | `elasticsearch_indices_flush_total` | 플러시 누적 수 | 회/초 | 자주 발생하는 플러시는 트랜스로그와 쓰기 압력과 관련될 수 있음 |
| 플러시 시간 | `elasticsearch_indices_flush_time_seconds` | 플러시 누적 시간 | 초/초 | 느린 플러시는 안정성에 영향을 줄 수 있음 |
| 트랜스로그 작업 | `elasticsearch_indices_translog_operations` | 현재 트랜스로그 작업 수 | 개수 | 지속적인 누적은 플러시에 주의를 필요로 함 |
| 트랜스로그 크기 | `elasticsearch_indices_translog_size_in_bytes` | 현재 트랜스로그 크기 | 바이트 | 과도한 크기는 복구 시간에 영향을 줄 수 있음 |
| 스토어 제한 | `elasticsearch_indices_store_throttle_time_seconds_total` | 인덱스 스토어 제한 누적 시간 | 초/초 | 제한 증가, 디스크에 의해 쓰기 영향을 받음 |

조사 제안: 쓰기 부하가 높을 때, 병합, 새로 고침, 플러시 및 트랜스로그 변경을 함께 고려하십시오. 병합 시간 증가와 저장소 제한은 보통 디스크가 ES에 영향을 주기 시작했음을 나타냅니다.

### 9.6 캐시 및 서킷 브레이커

| 모니터링 차원 | 지표 | 지표의 의미 | 공통 단위/측정 | 비정상 동작 |
| --- | --- | --- | --- | --- |
| 쿼리 캐시 메모리 | `elasticsearch_indices_query_cache_memory_size_bytes` | 쿼리 캐시에서 사용되는 메모리 | 바이트 | 과도한 사용은 쿼리 캐시 축출을 압박할 수 있습니다 JVM |
| 쿼리 캐시 축출 | `elasticsearch_indices_query_cache_evictions` | 누적 쿼리 캐시 축출 수 | 회/초 | 자주 발생하는 축출은 불안정한 캐시를 나타냅니다 |
| 필드데이터 메모리 | `elasticsearch_indices_fielddata_memory_size_bytes` | 필드데이터에서 사용되는 메모리 | 바이트 | 높은 필드데이터 사용은 쉽게 메모리 압박을 유발할 수 있습니다 |
| 필드데이터 축출 | `elasticsearch_indices_fielddata_evictions` | 누적 필드데이터 축출 수 | 회/초 | 높은 쿼리 또는 집계 압박 |
| 필터 캐시 축출 | `elasticsearch_indices_filter_cache_evictions` | 누적 필터 캐시 축출 수 | 회/초 | 자주 발생하는 필터 캐시 무효화 |
| 브레이커 예상 크기 | `elasticsearch_breakers_estimated_size_bytes` | 서킷 브레이커의 예상 메모리 | 바이트 | 제한에 근접하면 쿼리가 거부될 수 있습니다 |
| 브레이커 한도 | `elasticsearch_breakers_limit_size_bytes` | 서킷 브레이커 한도 | 바이트 | 브레이커 사용률 계산에 사용 |
| 브레이커 트리거 | `elasticsearch_breakers_tripped` | 서킷 브레이커가 트리거된 횟수 | 회/증가 | 성장 설명: 메모리 위험으로 인해 차단된 요청 |

일반적인 쿼리: 

```promql
elasticsearch_breakers_estimated_size_bytes / elasticsearch_breakers_limit_size_bytes * 100
```

```promql
increase(elasticsearch_breakers_tripped[10m])
```

조사 권고: 집계 쿼리, 정렬, 스크립트 쿼리는 필드데이터와 브레이커 사용량을 쉽게 증가시킬 수 있습니다. 브레이커가 트리거되면 일반적으로 쿼리 크기를 제한하거나, 인덱스 매핑을 최적화하거나, 쿼리 방법을 조정할 필요가 있습니다.

### 9.7 JVM, CPU, 및 부하

| 모니터링 차원 | 지표 | 지표 의미 | 일반 측정/단위 | 비정상 성능 |
| --- | --- | --- | --- | --- |
| JVM 사용된 메모리 | `elasticsearch_jvm_memory_used_bytes` | 현재 JVM 사용된 메모리 | 바이트 | 계속해서 한도 근처, GC 압력 증가 |
| JVM 최대 메모리 | `elasticsearch_jvm_memory_max_bytes` | 사용 가능한 최대 JVM 메모리 | 바이트 | 계산에 사용됨 JVM 사용량 |
| JVM 커밋된 메모리 | `elasticsearch_jvm_memory_committed_bytes` | JVM 커밋된 메모리 | 바이트 | 관찰 JVM 메모리 할당 |
| JVM 메모리 풀 피크 | `elasticsearch_jvm_memory_pool_peak_used_bytes` | 각 메모리 풀의 최대 사용량 | 바이트 | 올드 제너레이션의 높은 피크는 주의 필요 |
| GC 횟수 | `elasticsearch_jvm_gc_collection_seconds_count` | GC 발생 횟수 | 회/초 | 자주 발생하는 GC, 지연 시간 변동 가능 |
| GC 시간 | `elasticsearch_jvm_gc_collection_seconds_sum` | 총 GC 시간 | 초/초 | 높은 GC 시간은 쿼리 및 쓰기 성능에 영향을 줄 수 있음 |
| 프로세스 CPU | `elasticsearch_process_cpu_percent` | ES 프로세스 CPU 사용량 | 비율 | 장기간 높은 상태 CPU 무거운 쿼리 또는 쓰기 부하를 나타낼 수 있음 |
| 시스템 부하 | `elasticsearch_os_load1`, `elasticsearch_os_load5`, `elasticsearch_os_load15` | 노드 1/5/15분 부하 | 부하 값 | 부하가 더 높음 CPU cores는 명백한 작업 대기열을 나타냅니다 |
| 열린 파일 수 | `elasticsearch_process_open_files_count` | ES 프로세스가 연 파일 수 | 개수 | 시스템 한계에 접근하면 인덱스 파일 접근에 영향을 줄 수 있습니다 |

일반적인 문의: 

```promql
elasticsearch_jvm_memory_used_bytes / elasticsearch_jvm_memory_max_bytes * 100
```

```promql
rate(elasticsearch_jvm_gc_collection_seconds_sum[5m])
```

조사 제안: 더 큰 ES JVM 메모리가 항상 더 좋은 것은 아닙니다. JVM 사용량, GC 시간, 필드 데이터, 쿼리 캐시, 브레이커를 함께 모니터링하여 메모리 압력이 쿼리로 인한 것인지 힙 크기와 데이터 규모의 불일치로 인한 것인지 판단해야 합니다.

### 9.8 스레드 풀 및 네트워크

| 모니터링 차원 | 지표 | 지표 의미 | 일반 측정/단위 | 비정상 성능 |
| --- | --- | --- | --- | --- |
| 활성 스레드 | `elasticsearch_thread_pool_active_count` | 스레드 풀의 활성 스레드 수 | 카운트 | 장기적으로 높은 활성 스레드는 높은 처리 압력을 나타냅니다 |
| 완료된 작업 | `elasticsearch_thread_pool_completed_count` | 스레드 풀이 완료한 작업의 누적 수 | 초당 횟수 | 처리량을 관찰하는 데 사용됨 |
| 거부된 작업 | `elasticsearch_thread_pool_rejected_count` | 스레드 풀이 거부한 작업의 누적 수 | 초당 횟수 | 증가는 스레드 풀이나 큐가 가득 찼음을 나타냅니다 |
| 수신 트래픽 | `elasticsearch_transport_rx_size_bytes_total` | 트랜스포트가 수신한 누적 바이트 수 | 바이트/초 | 노드 간 통신 증가 또는 요청 트래픽 증가 |
| 발신 트래픽 | `elasticsearch_transport_tx_size_bytes_total` | 트랜스포트가 전송한 누적 바이트 수 | 바이트/초 | 샤드 재배치, 쿼리 또는 복제 때문에 트래픽 증가 |

일반적인 문의: 

```promql
sum by (type) (rate(elasticsearch_thread_pool_rejected_count[5m]))
```

```promql
rate(elasticsearch_transport_rx_size_bytes_total[5m]) + rate(elasticsearch_transport_tx_size_bytes_total[5m])
```

조사 제안: 스레드 풀 거부는 매우 직접적인 비즈니스 위험 신호입니다. 쓰기 거부의 경우, bulk/index 스레드 풀을 확인하고; 검색 거부의 경우, search 스레드 풀을 확인한 다음 CPU, JVM및 디스크 IO와 결합하여 병목 현상을 파악하십시오.

## 10. 애플리케이션 서비스 모니터링

애플리케이션 모니터링은 일반적인 서버 측 요청, 클라이언트 측 의존 호출, 런타임 리소스, 협업 편집 비즈니스 링크 및 RS 서비스 작업을 포함합니다. 애플리케이션 지표의 초점은 개별 리소스 임계값이 아니라 요청량, 오류, 지연 시간 및 의존성 상태입니다.

### 10.1 일반 서버 측 지표

| 모니터링 차원 | 지표 | 지표 의미 | 공통 범위/단위 | 비정상 성능 |
| --- | --- | --- | --- | --- |
| 서비스 가동 시간 | `up` | 애플리케이션 익스포터 또는 메트릭 엔드포인트 수집 가능 여부 | `0/1` | `0` 메트릭에 접근할 수 없거나 서비스가 비정상임을 의미합니다 |
| 빌드 정보 | `ego_build_info` | 애플리케이션 빌드 버전, 브랜치 및 기타 정보 | 라벨 정보 | 릴리스 버전 확인에 사용 |
| 시작 횟수 | `ego_server_started_total` | 서버 시작 누적 횟수 | 회/증가 | 증가는 프로세스 재시작 또는 릴리스를 나타냅니다 |
| 서버 요청 | `ego_server_handle_total` | 서버 요청 누적 수 | 회/초 | 요청의 급격한 증가 또는 감소는 비즈니스 맥락과 함께 판단해야 합니다 |
| 서버 측 시간 소모 | `ego_server_handle_seconds_count`, `ego_server_handle_seconds_bucket` | 서버 측 요청 시간 통계 | P50/P95/P99 | 지연 증가가 사용자 경험에 영향을 미칩니다 | 

일반적인 문의: 

```promql
sum by (service, method) (rate(ego_server_handle_total[5m]))
```

```promql
histogram_quantile(0.95, sum(rate(ego_server_handle_seconds_bucket[5m])) by (le, service, method))
```

조사 제안: 서버 측 이상 현상은 먼저 요청량이 변경되었는지 확인하고, 그 다음 오류 및 지연 시간을 확인하세요. 지연 시간이 증가하지만 자원이 높지 않은 경우, 하위 의존 호출과 큐를 계속 검토합니다.

### 10.2 클라이언트 의존 호출

| 모니터링 측면 | 지표 | 지표 의미 | 일반적인 세분화/단위 | 이상 동작 |
| --- | --- | --- | --- | --- |
| 클라이언트 호출량 | `ego_client_handle_total` | 애플리케이션이 클라이언트로서 하위단에 호출하는 횟수 | 회/초 | 하위 호출량의 급격한 증가, 의존 압력을 증폭시킬 수 있음 |
| 클라이언트 지연 시간 | `ego_client_handle_seconds_count`, `ego_client_handle_seconds_bucket` | 하위 호출 지연 시간 통계 | P50/P95/P99 | 느린 하위 서비스는 현재 서비스를 느리게 만들 수 있음 |
| 클라이언트 상태 | `ego_client_stats_gauge` | 클라이언트의 커넥션 풀 또는 상태형 지표 | 현재 값 | 커넥션 풀 고갈, 비정상 유휴 연결 등 |
| Kafka 운영 지연 시간 | `kafka_produce_duration_seconds_bucket` | 애플리케이션이 메시지를 생성하는 데 걸린 시간 Kafka 메시지 | P50/P95/P99 | 브로커 또는 네트워크 문제로 인해 생산 지연 증가 가능 |

일반적인 쿼리:

```promql
histogram_quantile(0.95, sum(rate(ego_client_handle_seconds_bucket[5m])) by (le, service, target, method))
```

조사 제안: 비즈니스 인터페이스가 느릴 경우, 서버 측에서 소요된 시간과 클라이언트 종속성에서 소요된 시간을 비교하세요. 클라이언트 시간이 높으면 해당 하위 서비스, 미들웨어 또는 네트워크를 우선 점검하세요.

### 10.3 런타임 및 프로세스

| 모니터링 차원 | 지표 | 지표 의미 | 일반 표준/단위 | 비정상 현상 |
| --- | --- | --- | --- | --- |
| Go goroutine | `go_goroutines` | Go 프로세스 내 goroutine 수 | 카운트 | 지속적인 증가 시 블로킹 또는 누수를 나타낼 수 있음 |
| Go GC 소요 시간 | `go_gc_duration_seconds` | Go GC 소요 시간 | 초/백분위 | GC 시간이 증가하면 지연에 영향을 줄 수 있음 |
| Go 힙 메모리 | `go_memstats_alloc_bytes`, `go_memstats_heap_inuse_bytes` | Go 힙 할당 및 사용량 | 바이트 | 지속적인 증가 시 메모리 누수 점검 필요 |
| Go 시스템 메모리 | `go_memstats_sys_bytes` | Go 런타임이 시스템에 요청한 메모리 | 바이트 | 함께 관찰 RSS |
| Go 스택 메모리 | `go_memstats_stack_inuse_bytes` | Goroutine 스택 사용량 | 바이트 | goroutine 증가와 함께 증가 |
| Node.js GC 횟수 | `nodejs_gc_duration_seconds_count` | Node.js GC 횟수 | 횟수/초 | 잦은 GC는 힙 압력을 나타낼 수 있음 |
| Node.js GC 소요 시간 | `nodejs_gc_duration_seconds_sum` | Node.js 총 GC 소요 시간 | 초/초 | GC 소요 시간 증가 시 응답에 영향 |
| Node.js 힙 공간 | `nodejs_heap_space_size_used_bytes` | 각 힙 공간의 사용량 Node.js 힙 공간 | 바이트 | 한계에 근접하거나 지속적으로 증가하는 경우 주의 필요 |
| 프로세스 CPU | `process_cpu_seconds_total` | 프로세스 CPU 시간 | 코어/초 | 높음 CPU 사용량 |
| 프로세스 RSS | `process_resident_memory_bytes` | 프로세스 물리적 메모리 | 바이트 | 지속적 RSS 증가 |
| 프로세스 핸들 | `process_open_fds` | 프로세스에서 열린 파일 디스크립터 수 | 개수 | 핸들 누수, 연결 누수 |

조사 제안: Go 실행 시 메트릭 Node.js 주로 애플리케이션 지연과 자원 증가를 설명하는 데 사용됩니다. 애플리케이션이 P95 증가할 때 GC 지속시간도 동시에 증가하면, 메모리 할당과 객체 수명 주기 점검을 우선시하십시오.

### 10.4 협업 편집 서비스

| 모니터링 차원 | 지표 | 지표 의미 | 공통 단위 | 비정상 징후 |
| --- | --- | --- | --- | --- |
| Kafka 컨슈머 지연 | `kafka_consumergroup_lag` | 협업 편집 관련 컨슈머 그룹의 백로그 | 카운트 | 지연 증가 시 이벤트 처리 지연 발생 가능성 |
| 프로세스 지속 시간 | `process_flow_duration_seconds_bucket` | 협업 편집 프로세스의 지속 시간 | P50/P95/P99 | 문서 협업 연결 속도 저하 |
| 프로세스 수 | `process_total` | 처리된 프로세스 총 수 | 초당 횟수 | 처리량의 비정상적 변화 |
| 파일 콘텐츠 크기 | `file_content_size_bytes_bucket` | 파일 콘텐츠 크기 분포 | 버킷별 통계 | 대용량 파일 비율 증가가 처리 시간에 영향을 미칠 수 있음 |
| 체인지셋 지속 시간 | `handle_changeset_cost_seconds_bucket` | 체인지셋 처리에 소요된 시간 | P50/P95/P99 | 편집 동기화 지연 증가 |
| 모독 계산 횟수 | `modocComputeCount` | 모독 계산 수 | 초당 횟수 | 계산량의 비정상적 증가 |
| 서버리스 호출 | `serverless_invocations` | 서버리스 호출 수 | 초당 횟수 | 호출 실패 또는 급증이 링크에 영향을 미칠 수 있음 |

일반 쿼리:

```promql
histogram_quantile(0.95, sum(rate(handle_changeset_cost_seconds_bucket[5m])) by (le))
```

조사 제안: 협업 편집 링크의 경우, Kafka 지연, 처리 시간, 체인지셋 지속 시간 및 파일 크기를 함께 검토해야 함. 대용량 파일 비율이 증가하면 지속 시간 증가가 단일 지점의 오류보다는 정상적인 용량 부담일 수 있음.

### 10.5 RS 서비스

| 모니터링 차원 | 지표 | 지표 의미 | 공통 범위/단위 | 비정상 성능 |
| --- | --- | --- | --- | --- |
| HTTP 요청 수 | `http_requests_total` | 누적 수 HTTP 요청 | 회/초 | 요청의 급격한 증가 또는 감소 |
| HTTP 지속 시간 | `http_requests_duration_seconds_bucket`, `http_requests_duration_seconds_sum`, `http_requests_duration_seconds_count` | HTTP 요청 지속 시간 | P50/P95/P99 | 인터페이스 지연 증가 |
| gRPC 요청 수 | `grpc_requests_total` | 누적 수 gRPC 요청 | 회/초 | gRPC 호출 예외 |
| gRPC 지속 시간 | `grpc_requests_duration_seconds` | gRPC 요청 지속 시간 | P50/P95/P99 | 하위 시스템 또는 내부 처리 지연 |
| 내보내기 작업 지속 시간 | `export_task_duration_milliseconds_count` | 내보내기 작업 처리 수 및 지속 시간 | ms/시간 | 내보내기 작업 속도 저하 또는 쌓임 |
| 가져오기 작업 기간 | `import_task_duration_milliseconds_count` | 가져오기 작업 프로세스 수 및 기간 | 작업당 ms | 느려지거나 쌓인 가져오기 작업 |
| 진행 중인 내보내기 작업 | `export_task_in_progress` | 현재 실행 중인 내보내기 작업 | 개수 | 오랜 시간 동안 감소하지 않으면 작업이 멈췄음을 나타냄 |
| 진행 중인 가져오기 작업 | `import_task_in_progress` | 현재 실행 중인 가져오기 작업 | 개수 | 오랜 시간 동안 감소하지 않으면 작업이 멈췄음을 나타냄 |
| Tokio 메트릭 | `tokio_metrics` | Rust Tokio 런타임 메트릭 | 현재 값 / 비율 | 비정상 런타임 큐 또는 작업 스케줄링 |
| jemalloc 메트릭 | `jemalloc` | 메모리 할당자 메트릭 | 바이트 / 개수 | 메모리 단편화 또는 할당 이상 |
| TCP 메트릭 | `tcp` | RS 서비스 TCP 연결 관련 메트릭 | 개수 / 비율 | 연결 압력 또는 네트워크 이상 |

조사 제안: RS 서비스는 온라인 요청과 가져오기/내보내기 같은 장기 실행 작업을 모두 확인해야 합니다. 진행 중인 작업의 수가 계속해서 증가하지 않는 것은 평균 기간보다 '작업이 멈춤'을 더 신뢰성 있게 나타냅니다.

## 11. 메트릭 읽기 및 조사 제안

### 11.1 일반 조사 순서

| 단계 | 관찰 항목 | 목적 |
| --- | --- | --- |
| 1 | `up`, 시작 시간, Pod 준비 완료 | 서비스가 여전히 살아 있는지, 최근에 재시작했는지 확인 |
| 2 | 요청량, 오류율, P95/P99 지연 | 실제로 비즈니스에 영향을 미치는지 판단 |
| 3 | CPU, 메모리, 디스크, 네트워크 | 리소스 병목 현상이 있는지 판단 |
| 4 | 하류 의존성 지연, Kafka 지연, 느린 데이터베이스 쿼리 | 의존성으로 인해 속도가 느려졌는지 판단 |
| 5 | 릴리스 버전, 구성, 트래픽 변경 | 변경과 관련이 있는지 판단 |

실제 문제 해결 시, 먼저 모든 차트를 보려 하지 마세요. 먼저 '비즈니스 영향이 있는지'를 확인하고, 그 다음 '영향이 어디서 오는지'를 찾습니다. 예를 들어, 인터페이스가 느리면 먼저 애플리케이션의 P95을 보고, 그 다음 클라이언트 의존성 지연을 확인; 의존성이 정상이라면 서비스의 CPU, GC, 메모리, 컨테이너 쓰로틀링을 확인합니다.

### 11.2 일반적인 예외 조합

| 증상 | 일반 메트릭 성능 | 우선 조사 방향 |
| --- | --- | --- |
| 인터페이스 느림 | 애플리케이션 P95/P99 증가, CPU 높지 않음 | 하류 종속성, 느린 데이터베이스 쿼리, Kafka 지연 |
| CPU 완전히 사용됨 | `container_cpu_usage_seconds_total` 높음, 스로틀 높음 | CPU 제한, 핫 인터페이스, 배치 처리 작업 |
| 메모리 OOM | 작업 집합이 한계에 가까움, 재시작 횟수 증가 | 메모리 누수, 제한이 너무 작음, 대용량 객체 처리 |
| 느린 디스크 | iowait, IO 사용 바쁨, 읽기/쓰기 지연 모두 증가 | 데이터베이스, Kafka, MinIO, 로그 쓰기 |
| 네트워크 이상 | 트래픽 급증과 함께 드롭/오류 발생 | 노드 NIC, CNI, 링크, 연결 수 |
| Kafka 지연 | `kafka_consumergroup_lag` 지속적으로 증가 | 컨슈머 인스턴스, 소비 시간, 하류 종속성 |
| Redis 백프레셔 | 히트율 감소, 미스 증가 | 키 만료 정책, 캐시 침투, 용량 |
| MySQL 느림 | 느린 쿼리, 스캔, 락 대기 증가 | SQL, 인덱스, 락, 디스크 IO |
| MinIO 위험 | 오프라인 디스크, 오류율, 용량 수준 증가 | 디스크, 노드, 버킷 성장, 복구 상태 |
| Elasticsearch 느린 쿼리 | 검색 쿼리/가져오기 시간 증가, 스레드 풀 거부 증가 | 쿼리 조건, 인덱스 구조, JVM, 디스크 IO |
| Elasticsearch 느린 쓰기 | 인덱싱 시간, 병합 시간, 스토어 스로틀 증가 | 쓰기 피크, 새로 고침, 병합, 디스크 레벨 |
