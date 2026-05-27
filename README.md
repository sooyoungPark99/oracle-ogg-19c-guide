# Oracle GoldenGate 19c 구성 가이드

Oracle ASM 환경에서 GoldenGate 19c를 설치하고 orcl → clonedb 단방향 복제를 구성하는 실습 가이드입니다.
VirtualBox 기반의 실습 환경에서 직접 설치하며 작성했습니다.

---

### 환경 구성

| 항목 | 내용 |
|------|------|
| OS | Oracle Linux 7.9 |
| DB 버전 | Oracle Database 19c |
| OGG 버전 | Oracle GoldenGate 19.1.0.0.4 |
| 스토리지 | Oracle ASM |
| 가상화 | VirtualBox 7.0.18 |
| 메모리 | 8GB |

---

### 서버 구성

| VM 이름 | 호스트 이름 | 메모리 | 어댑터 | 구성 방법 |
|---------|-----------|--------|--------|---------|
| asmtest | asmtest | 8GB | 어댑터에 브리지 | 기존 ASM VM 활용 |

---

### 경로 구성

| 항목 | 경로 |
|------|------|
| ORACLE_HOME | `/app/oracle/product/19.0.0/db_1` |
| GRID_HOME | `/app/grid` |
| OGG_HOME | `/app/oracle/goldengate/19c` |

---

### 네트워크

| VM | IP | 넷마스크 | 게이트웨이 | DNS 서버 |
|----|----|---------|----------|---------|
| asmtest | 172.31.#.### | 255.255.0.0 | 192.168.0.1 | 8.8.8.8 |

---

### 복제 구성도

```
[orcl]
  └─ Extract(EXT1) ─→ Trail(lt) ─→ DataPump(PMP1) ─→ Trail(rt) ─→ Replicat(REP1) ─→ [clonedb]
```

---

### 설치 순서

| 순서 | 내용 |
|------|------|
| 0 | [사전 준비 — clonedb 생성](./OGG_19c_Setup_Guide.md#0-사전-준비--clonedb-생성) |
| 1 | [OGG 다운로드](./OGG_19c_Setup_Guide.md#1-ogg-다운로드) |
| 2 | [OGG 설치](./OGG_19c_Setup_Guide.md#2-ogg-설치-runinstaller---oui-방식) |
| 3 | [환경변수 설정](./OGG_19c_Setup_Guide.md#3-환경변수-설정) |
| 4 | [Oracle DB 사전 설정](./OGG_19c_Setup_Guide.md#4-oracle-db-사전-설정) |
| 5 | [OGG 유저 생성 및 권한 부여](./OGG_19c_Setup_Guide.md#5-ogg-유저-생성-및-권한-부여) |
| 6 | [복제 대상 테이블 생성 및 데이터 동기화](./OGG_19c_Setup_Guide.md#6-복제-대상-테이블-생성-및-데이터-동기화) |
| 7 | [GGSCI 초기 설정 — Manager](./OGG_19c_Setup_Guide.md#7-ggsci-초기-설정-manager) |
| 8 | [Extract 설정](./OGG_19c_Setup_Guide.md#8-extract-설정) |
| 9 | [Data Pump 설정](./OGG_19c_Setup_Guide.md#9-data-pump-설정) |
| 10 | [Replicat 설정](./OGG_19c_Setup_Guide.md#10-replicat-설정) |
| 11 | [복제 테스트](./OGG_19c_Setup_Guide.md#11-복제-테스트) |

---

### 주요 특징

- ASM 환경에서 OGG 19c 설치 및 구성
- Extract / Data Pump / Replicat 3단계 복제 파이프라인 구성
- orcl → clonedb 단방향 실시간 DML 복제
- DDL 미복제 동작 확인 실습 포함
- 실제 설치 중 발생한 오류 및 해결 방법 포함
