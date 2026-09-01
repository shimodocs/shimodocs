# 객체 스토리지 구성

[← ShimoDocs Suite 배포 문서](../../README.md)

이 문서는 구현, 운영 또는 통합 담당자가 ShimoDocs 외부 타사와의 연결 S3 객체 스토리지를 단계별로 설정합니다.


# 1. 사전 운영 확인

## S3 객체 스토리지 요구사항

`Only object storage fully compatible with the S3 protocol is supported. Huawei Cloud OBS, Alibaba Cloud OSS, Tencent Cloud COS, and AWS S3 are recommended. For local deployment, MinIO can be considered.`



> [!TIP]
>
> 공용 클라우드를 선호하며, 지원이 필요함 HTTPS 외부 접근



## 네트워크 연결

다음 포트 K8s 비즈니스 클러스터 네트워크가 객체 스토리지 인스턴스에 연결할 수 있도록 열려 있어야 함

```js
telnet IP 9000
```
## 접근 제어 및 권한
- 완전한 AK/SK 인증 정보를 제공해야 함
- PutObject, GetObject, DeleteObject, ListObjects, CopyObject, InitiateMultipartUpload와 같은 핵심 인터페이스를 완전히 지원해야 함



# S3 스토리지 요구사항 설명
- 지연 시간 요구 사항: 내부 네트워크 환경에서 스토리지의 평균 응답 시간 API 는 < 50ms가 권장되며, 공용 네트워크 환경에서는 < 200ms가 권장됩니다. 높은 지연 시간은 문서 열기 속도와 첨부 파일 업로드 경험에 직접적인 영향을 줍니다.
- 동시 처리 능력: 기업의 예상 최대치를 지원해야 합니다. QPS. ShimoDocs 은 다중 사용자 협업 및 배치 가져오기/내보내기 동안 폭발적인 트래픽을 생성하므로, 스토리지 측에서는 과도하게 엄격한 속도 제한 정책을 적용해서는 안 됩니다.
- 가용성 SLA: 운영 환경에서 스토리지 가용성이 99.9% 이상이 되도록 권장합니다.
- 공용 네트워크 스토리지 통신은 다음을 통해 수행해야 합니다 HTTPS/TLS 암호화된 채널.
- 시간 동기화: S3 스토리지 서비스와 ShimoDocs 애플리케이션 서버는 NTP를 통해 동기화해야 하며, 그렇지 않으면 S3 서명 검증이 실패합니다.
