# OpenSSD 할당 정책 변경 가이드

## 할당 정책 4가지

1. **Policy 1: Channel → Way → Page → Block** (기본값, 최고 성능)
2. **Policy 2: Way → Channel → Block → Page** (균등 부하 분산)
3. **Policy 3: Block → Page → Way → Channel** (순환, 마모 균등화)
4. **Policy 4: Page → Block → Channel → Way** (역순환, 특수 최적화)

## 사용법

### 1. 정책 선택
`address_translation.h` 파일에서 원하는 정책의 주석을 해제하세요:

```c
// Policy 2를 사용하려면:
// #define ALLOCATION_POLICY_1  // 주석 처리
#define ALLOCATION_POLICY_2     // 주석 해제
// #define ALLOCATION_POLICY_3  // 주석 처리  
// #define ALLOCATION_POLICY_4  // 주석 처리
```

### 2. 빌드 & 플래시
- 전체 프로젝트 빌드
- OpenSSD에 플래시

### 3. 확인
시리얼 콘솔에서 부팅 시 다음 메시지 확인:

```
=== ALLOCATION POLICY INFO ===
Policy: Way -> Channel -> Block -> Page (Policy 2)
USER_CHANNELS=4, USER_WAYS=8, USER_DIES=32
=============================
```

## 성능 테스트

Host PC에서 FIO로 각 정책 성능 비교:

```bash
# 기본 성능 테스트
sudo fio --name=test --filename=/dev/nvme0n1 --rw=randwrite --bs=4k --size=1G --numjobs=4 --runtime=30 --time_based

# 병렬성 테스트
sudo fio --name=parallel --filename=/dev/nvme0n1 --rw=randwrite --bs=4k --iodepth=32 --numjobs=8 --size=512M --runtime=30 --time_based
```

각 정책별로 IOPS, 지연시간, 처리량을 비교해보세요!