# 회계연도·전기기간: GUI와 RAP

**회계연도는 회계용 1년, 전기기간은 그 연도를 나누어 거래를 기록·집계하는 구간입니다.**

## 1. 회계연도와 전기기간

| 방식 | 범위 예시 |
|---|---|
| 달력연도 | 2026-01-01 ~ 2026-12-31 |
| 4월 시작 | 2026-04-01 ~ 2027-03-31 |

실제 회계연도 번호는 연도 이동 설정에 따라 결정됩니다. 날짜의 연도와 항상 같지 않습니다.

| 달력연도 방식 | 4월 시작 방식 예시 |
|---|---|
| 1월 = 001기간 | 4월 = 001기간 |
| 10월 = 010기간 | 10월 = 007기간 |
| 12월 = 012기간 | 다음 해 3월 = 012기간 |

**날짜의 월을 그대로 전기기간으로 사용하면 안 됩니다.**

## 2. 전기일과 연결

회사코드·관련 원장 설정 → 회계연도 변형 → 전기일로 연도·기간 결정 → 기간 개방·권한 확인.

달력연도 회사에서 전표일 9/30, 입력일 10/6, 전기일 9/30이면 일반적으로 2026년 009기간에 반영됩니다.

## 3. 설정 구분

| 설정 | 역할 |
|---|---|
| 회계연도 변형 (Fiscal Year Variant) | 날짜가 어느 회계연도·기간에 속하는지 정의 |
| 전기기간 변형 (Posting Period Variant) | 기간 개방·마감 관리 설정의 구분 |

Variant는 재사용 가능한 설정 묶음입니다. K4는 1월~12월을 사용하는 표준 회계연도 변형 예시입니다.

## 4. 개방·마감

완료된 장부에 추가 전기가 발생하지 않도록 기간을 통제합니다. 여러 기간을 동시에 열거나 계정 유형·범위·권한에 따라 제한할 수 있습니다.

기간이 열려 있어도 모든 거래가 허용되는 것은 아닙니다. 마감 때문에 날짜를 임의로 변경하지 말고 결산·조정 절차를 따릅니다.

## 5. 특별기간

12개 일반기간 구성에서는 13~16 같은 특별기간을 연말 결산 조정에 사용할 수 있습니다. **13기간은 다음 해 1월이 아닙니다.** 마지막 일반기간을 결산 목적으로 세분화합니다.

- 필요한 특별기간이 설정되어 있어야 합니다.
- 전기일은 마지막 일반기간에 속해야 합니다.
- 특별기간을 별도로 지정하고 개방·권한을 확인합니다.
- 날짜만으로 특별기간이 자동 결정되는 것은 아닙니다.

## 6. SAP GUI

대표적으로 OB52에서 전기기간 개방·마감을 관리합니다. 실제 버전·환경에 따라 해당 구성·Fiori 앱을 사용합니다.

확인 정보: 전기기간 변형, 계정 유형·범위, 시작·종료 기간과 연도, 권한그룹.

회계연도 구조 변경과 일상적인 월말 개방·마감 작업은 다른 업무입니다.

## 7. RAP CDS와 필드

사용자 정의 모델에서 필요한 CompanyCode·Ledger·PostingDate·FiscalYearVariant·FiscalYear·FiscalPeriod를 구분합니다. 모든 필드를 반드시 저장할 필요는 없으며 파생값의 일관성을 관리합니다.

| 표준 CDS 예시 | 목적 |
|---|---|
| F_FiscalPeriod | 날짜·회계연도 변형으로 일반기간 도출 |
| I_FsclPerdWthoutFsclYrForVar | 특정 회계연도와 무관한 변형별 일반기간 구조 |

F_FiscalPeriod는 P_FiscalYearVariant·P_CalendarDate를 입력으로 사용합니다. 기간 도출과 전기 가능 검사는 다릅니다.

아래는 이미 일반기간이 결정된 요청 모델의 관계 예시입니다.

```abap
association [0..1] to I_FsclPerdWthoutFsclYrForVar
  as _FiscalPeriod
  on  $projection.FiscalYearVariant
        = _FiscalPeriod.FiscalYearVariant
  and $projection.FiscalPeriod
        = _FiscalPeriod.FiscalPeriod
```

- 변형과 기간을 함께 비교합니다.
- Cardinality는 실제 키와 관계에 맞게 설정합니다.
- 대상은 특별기간을 제외하므로 특별기간 조회에 그대로 사용하지 않습니다.
- Association은 날짜 변환이나 개방 여부 검사 기능이 아닙니다.
- 실제 필드·공개 상태·지원 용도는 시스템에서 확인합니다.

요청 저장 후 실제 전기까지 기간이 마감될 수 있으므로 표준 FI 전기 시점의 검증 결과를 처리합니다.

## 공식 자료

- [회계연도와 달력연도](https://help.sap.com/docs/SAP_S4HANA_ON-PREMISE/3cb1182b4a184bdd93f8d62e3f1f0741/1952d7531a4d424de10000000a174cb4.html?version=2023.latest)
- [회계연도 변형](https://help.sap.com/docs/SAP_S4HANA_CLOUD/0fa84c9d9c634132b7c4abb9ffdd8f06/7353d7531a4d424de10000000a174cb4.html)
- [특별기간](https://help.sap.com/docs/SAP_S4HANA_ON-PREMISE/651d8af3ea974ad1a4d74449122c620e/2252d7531a4d424de10000000a174cb4.html)
- [OB52](https://help.sap.com/docs/SUPPORT_CONTENT/fiaccounting/3361878638.html)
- [기간 도출 CDS](https://help.sap.com/docs/SAP_S4HANA_CLOUD/c0c54048d35849128be8e872df5bea6d/8fb96737960c4010b7343891b71191e8.html?locale=en-US&state=PRODUCTION&version=2602.500)
- [일반기간 CDS](https://help.sap.com/docs/PRODUCT_ID/c0c54048d35849128be8e872df5bea6d/fcab4fd2ccf9465ebfec648ce361035d.html)

[FI 학습 순서](02-fi-roadmap.md) · [용어집](05-fi-glossary.md)
