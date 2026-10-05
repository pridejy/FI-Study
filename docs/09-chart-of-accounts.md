# 계정과목표: 회사코드·G/L 계정·RAP 연결

**계정과목표(Chart of Accounts)는 회사가 사용할 G/L 계정의 목록과 구조**입니다.

| 개념 | 질문 |
|---|---|
| 회사코드 | 어느 회사의 회계인가? |
| 계정과목표 | 어떤 계정 목록을 사용하는가? |
| G/L 계정 | 어떤 종류의 거래인가? |
| 총계정원장 | 계정별 거래 내역과 잔액은 얼마인가? |

## 1. 계정과목표 예시

번호와 명칭은 설명용입니다.

**계정과목표 ZKR1**

| 계정번호 | 명칭 | 회계 분류 |
|---|---|---|
| 100100 | 보통예금 | 자산 |
| 110100 | 매출채권 | 자산 |
| 200100 | 매입채무 | 부채 |
| 300100 | 자본금 | 자본 |
| 400100 | 상품매출 | 수익 |
| 500100 | 급여 | 비용 |

ZKR1은 목록의 식별 코드, 100100은 그 목록 안의 G/L 계정입니다. 계정과목표는 거래 내역이나 잔액을 직접 기록하는 장부가 아닙니다.

## 2. 회사코드와의 관계

회사코드에는 운영 계정과목표를 지정하며 여러 회사코드가 같은 체계를 공유할 수 있습니다.

| 회사코드 | 계정과목표 | G/L 계정 | 잔액 예시 |
|---|---|---|---:|
| 1000 | ZKR1 | 100100 | 1,000만 원 |
| 2000 | ZKR1 | 100100 | 300만 원 |

같은 계정 정의를 사용해도 회사별 거래와 잔액은 구분됩니다.

## 3. G/L 계정 마스터의 두 수준

| 수준 | 대표 정보 |
|---|---|
| 계정과목표 수준 | 계정번호·명칭·계정그룹·계정 유형 |
| 회사코드 수준 | 계정 통화·세금 범주·조정계정 여부·미결항목 관리·필드상태그룹 |

계정과목표에 계정이 존재해도 회사코드별 데이터가 없거나 사용이 제한되어 있으면 그 회사에서 전기할 수 없습니다.

> **계정과목표에 존재하는가? → 회사코드에 준비되어 있는가? → 해당 거래에 사용 가능한가?**

## 4. 계정과목표 종류

| 종류 | 역할 |
|---|---|
| 운영 계정과목표 (Operating) | 일상적인 회계 전기의 기본 계정 체계 |
| 그룹 계정과목표 (Group) | 그룹의 공통 보고 체계로 계정 연결 |
| 국가별 계정과목표 (Country) | 국가별 보고 요구에 맞춘 계정 연결 |

그룹·국가별 체계는 필요에 따라 구성합니다. 그룹 계정과목표를 지정한다고 연결재무제표가 자동 완성되는 것은 아닙니다.

## 5. SAP GUI

ECC·S/4HANA 온프레미스 및 Private Edition의 대표 예시입니다. 실제 기능은 버전·권한에 따라 확인합니다.

| 트랜잭션 | 역할 |
|---|---|
| FS00 | G/L 계정 중앙 관리 |
| FSS0 | 회사코드별 계정 데이터 관리 |

FS00에서 계정과목표 공통 정보와 회사코드별 정보를 구분해 확인합니다. Fiori에서는 Manage G/L Account Master Data 등의 앱을 사용합니다.

## 6. RAP에서 참고할 CDS와 필드

| 표준 CDS | 용도 |
|---|---|
| I_GLAccountInChartOfAccounts | 계정과목표 수준 G/L 계정 마스터 조회 |
| I_GLAcctInChtOfAcctsStdVH | G/L 계정 값 도움말 전용 |

| 필드 | 의미 |
|---|---|
| ChartOfAccounts | 계정과목표 코드 |
| GLAccount | 계정번호 |
| GLAccountGroup | 계정그룹 |
| GLAccountName | 계정명. 텍스트 소스·언어 처리를 확인 |

**ChartOfAccounts + GLAccount**가 계정과목표 수준 계정을 식별하는 핵심입니다. 표준 CDS의 실제 필드와 공개 상태는 ADT·View Browser에서 확인합니다. API 엔터티와 CDS는 다른 객체이며 API의 필드 목록을 CDS에 그대로 적용하지 않습니다.

## 7. Association 예시

요청 항목 CDS에 ChartOfAccounts와 GLAccount가 있다고 가정한 부분 코드입니다.

```abap
association [0..1] to I_GLAccountInChartOfAccounts
  as _GLAccount
  on  $projection.ChartOfAccounts = _GLAccount.ChartOfAccounts
  and $projection.GLAccount      = _GLAccount.GLAccount
```

```text
요청 항목: ZKR1 / 500100
             ↓ 두 필드 모두 일치
계정 마스터: ZKR1 / 500100
```

- **_GLAccount:** 관계 이름.
- **ON:** 같은 계정과목표와 계정번호를 연결.
- **[0..1]:** 대상이 없거나 최대 한 건이라는 관계 선언. 존재 검증이나 DB 제약이 아님.
- 대상 키·데이터 관계에 맞게 Cardinality를 지정해야 합니다.

계정번호만 연결하면 다른 계정과목표의 동일 번호와 잘못 연결될 수 있습니다.

회사코드에 지정된 운영 계정과목표와 요청의 ChartOfAccounts도 일치해야 합니다. 회사 설정에서 결정하거나 서버에서 검증합니다. 이 Association은 회사코드별 전기 가능 여부를 보장하지 않습니다.

## 8. 값 도움말·검증·권한

- 회사코드에 맞는 계정과목표로 값 도움말 범위를 제한합니다.
- 입력된 회사코드·계정과목표·계정의 일관성을 서버에서 검증합니다.
- 회사코드별 확장, 전기 차단, 세금·필드 설정 및 표준 업무 제약을 확인합니다.
- 조회는 적절한 DCL, 변경은 RAP 권한 처리로 보호합니다.
- 표준 CDS·API의 공개 상태와 지원 용도를 확인합니다.

## 9. 관련 용어

| 용어 | 뜻 |
|---|---|
| 마스터 데이터 | 반복적으로 사용하는 기본 정보 |
| 계정그룹 | 계정 번호 범위 및 마스터 입력 필드 상태 등을 관리하는 분류 |
| 필드상태그룹 | 전표 입력 필드의 필수·선택·숨김 등을 제어하는 설정 |
| 재무제표 버전 (FSV) | 계정들을 재무제표 항목으로 묶는 보고 구조 |

계정과목표는 사용할 계정 체계이고, FSV는 보고서에서 보여줄 구조입니다.

## 공식 참고 자료

- [계정과목표·회사코드·GL 관계](https://learning.sap.com/courses/explain-the-master-data-concept-in-the-record-to-report-area/explaining-chart-of-accounts-general-ledger-company-code-controlling-area-and-their-relationship_ddd04e2c-562d-4ba5-ad2d-c8c4d394c29d)
- [계정과목표 종류](https://learning.sap.com/courses/defining-general-ledger-master-records/identifying-the-chart-of-accounts-types)
- [계정 마스터 설정 수준](https://learning.sap.com/courses/explain-the-master-data-concept-in-the-record-to-report-area/explaining-g-l-account-settings-on-chart-of-accounts-level-company-code-level-and-controlling-area-level_da3e3d1d-5fbf-4506-a366-a82cac414322)
- [SAP GUI 계정 관리](https://help.sap.com/docs/SAP_S4HANA_ON-PREMISE/651d8af3ea974ad1a4d74449122c620e/34428754dccbe85ee10000000a44176d.html)
- [G/L 계정 CDS 값 도움말](https://help.sap.com/docs/PRODUCT_ID/c0c54048d35849128be8e872df5bea6d/6cd19cee475744b1a89b7d077c764a21.html)
- [계정 API 필드](https://help.sap.com/docs/PRODUCT_ID/3ab6e6fc510f4840a5508e126ef01e22/7d98e39c63384590897f8afc80ed54a0.html)

[FI 학습 순서](02-fi-roadmap.md) · [용어집](05-fi-glossary.md)

정리일: 2026-10-06
