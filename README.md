# Oracle GoldenGate 19c 구성 가이드

Oracle ASM 환경에서 GoldenGate 19c를 설치하고 orcl → clonedb 단방향 복제를 구성하는 실습 가이드입니다.

---

## 환경

| 항목 | 값 |
|------|-----|
| OS | Oracle Linux 7.9 |
| Oracle DB | 19c (19.0.0.0) |
| OGG | 19.1.0.0.4 |
| ORACLE_HOME | `/app/oracle/product/19.0.0/db_1` |
| OGG_HOME | `/app/oracle/goldengate/19c` |
| hostname | asmtest |
| IP | 192.168.13.181 |

---

## 구성도

```
[orcl]  →  Extract(EXT1)  →  Trail(lt)  →  DataPump(PMP1)  →  Trail(rt)  →  Replicat(REP1)  →  [clonedb]
```

---

## 목차

| 단계 | 내용 |
|------|------|
| [0. 사전 준비](OGG_19c_Setup_Guide.md#0-사전-준비--clonedb-생성) | clonedb DBCA 생성, tnsnames.ora 설정 |
| [1. OGG 다운로드](OGG_19c_Setup_Guide.md#1-ogg-다운로드) | Oracle eDelivery에서 다운로드 |
| [2. OGG 설치](OGG_19c_Setup_Guide.md#2-ogg-설치-runinstaller---oui-방식) | runInstaller Silent 모드 설치 |
| [3. 환경변수 설정](OGG_19c_Setup_Guide.md#3-환경변수-설정) | .bash_profile OGG 경로 추가 |
| [4. DB 사전 설정](OGG_19c_Setup_Guide.md#4-oracle-db-사전-설정) | Archivelog, Supplemental Logging, GoldenGate 파라미터 |
| [5. OGG 유저 생성](OGG_19c_Setup_Guide.md#5-ogg-유저-생성-및-권한-부여) | oggadmin 유저 및 권한 부여 |
| [6. 테이블 생성](OGG_19c_Setup_Guide.md#6-복제-대상-테이블-생성-및-데이터-동기화) | scott.emp, scott.dept 양쪽 동기화 |
| [7. Manager 설정](OGG_19c_Setup_Guide.md#7-ggsci-초기-설정-manager) | GGSCI 초기화 및 MGR 기동 |
| [8. Extract 설정](OGG_19c_Setup_Guide.md#8-extract-설정) | EXT1 — orcl 변경 데이터 캡처 |
| [9. Data Pump 설정](OGG_19c_Setup_Guide.md#9-data-pump-설정) | PMP1 — Trail 파일 전달 |
| [10. Replicat 설정](OGG_19c_Setup_Guide.md#10-replicat-설정) | REP1 — clonedb 적용 |
| [11. 복제 테스트](OGG_19c_Setup_Guide.md#11-복제-테스트) | DML 수행 후 동기화 확인 |

---

## 핵심 주의사항

- orcl과 clonedb **양쪽에 동일한 설정**이 필요하다. 한쪽만 하면 반드시 에러가 난다.
- 복제 전 양쪽 테이블 데이터가 **완전히 일치**해야 한다.
- DDL 패키지 미설치 환경에서는 파라미터에 **DDL 관련 설정을 넣지 않는다**.
- REP1 ABEND 시 **PK 제약 추가** 후 `ALTER REPLICAT REP1, BEGIN NOW`로 리셋한다.

---

## 파일 구성

```
.
├── README.md                  # 현재 파일
└── OGG_19c_Setup_Guide.md     # 전체 구성 가이드
```

