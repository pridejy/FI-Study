# 전표 헤더·항목: GUI와 RAP

**헤더는 전표 전체의 공통 정보, 항목은 각 계정에 기록할 개별 회계 정보입니다.**

## 전표 예시

당월 임차료·전기요금을 처음 비용으로 기록하고 예금으로 지급하는 예시이며 세금은 생략합니다.

| 헤더 정보 | 예시 |
|---|---|
| 회사코드 | 1000 |
| 전표번호 / 회계연도 | 1900000001 / 2026 |
| 전표일 / 전기일 | 2026-10-06 / 2026-10-06 |
| 전표 통화 | KRW |
| 헤더 적요 | 10월 임차료·전기요금 지급 |

| 항목 | 차변·대변 | 계정 | 금액 |
|---|---|---|---:|
| 001 | 차변 | 임차료 | 300,000 |
| 002 | 차변 | 전기요금 | 100,000 |
| 003 | 대변 | 예금 | 400,000 |

헤더 하나에 여러 항목이 연결됩니다. 항목 한 줄은 분개 전체가 아닙니다.

## 대표 정보

- **헤더:** 회사코드·전표번호·회계연도·전표일·전기일·전표유형·전표 통화·헤더 적요·참조번호.
- **항목:** 항목번호·계정·차변/대변·금액·원가센터·세금코드·항목 적요 등.
- 공급업체·고객 계정 항목은 관련 조정계정을 통해 GL과 연결됩니다.
- 항목의 여러 금액은 각각 대응하는 통화와 함께 읽어야 합니다.

## GUI와 기술 구조

FB03에서 회사코드 + 전표번호 + 회계연도로 조회합니다.

| 테이블 | 역할 | 대표 식별 필드 |
|---|---|---|
| BKPF | 회계 전표 헤더 | BUKRS·BELNR·GJAHR |
| BSEG | 운영 회계 전표 항목 | 위 필드 + BUZEI |

같은 클라이언트에서 회사코드·전표번호·회계연도로 관계를 연결하며, 클라이언트 간 처리에는 클라이언트도 고려합니다.

S/4HANA의 총계정원장 관점 Universal Journal 항목과 BSEG 운영 항목은 목적·키·행 구성이 다를 수 있습니다. 모든 회계 항목을 BSEG만으로 설명하지 않습니다.

## 표준 CDS 예시

I_JournalEntryItem에서 CompanyCode·FiscalYear·AccountingDocument·LedgerGLLineItem·Ledger·SourceLedger 등의 필드와 _JournalEntry 헤더 Association을 확인할 수 있습니다.

실제 필드·키·공개 상태는 시스템에서 확인합니다. LedgerGLLineItem을 BSEG의 BUZEI와 동일하게 가정하지 않습니다.

## 사용자 정의 RAP Composition 예시

아래는 가상의 테이블을 사용하는 학습 코드입니다. 완성 앱이 아니며 테이블·Behavior·DCL·Projection·서비스를 별도로 구현합니다.

```abap
define root view entity ZI_FIRequest
  as select from zfi_req_h
  composition [0..*] of ZI_FIRequestItem as _Items
{
  key request_uuid as RequestUUID,
      company_code as CompanyCode,
      posting_date as PostingDate,
      _Items
}
```

```abap
define view entity ZI_FIRequestItem
  as select from zfi_req_i
  association to parent ZI_FIRequest as _Header
    on $projection.RequestUUID = _Header.RequestUUID
{
  key request_uuid as RequestUUID,
  key item_no      as ItemNo,
      gl_account   as GLAccount,
      _Header
}
```

- **_Items:** 헤더가 소유하는 항목 목록. [0..*]은 입력 도중 항목이 없을 수 있는 구조를 표현합니다.
- **_Header:** 항목에서 소속 헤더로 돌아가는 관계.
- **ON:** RequestUUID가 같은 부모를 연결.
- **항목 키:** RequestUUID + ItemNo.
- 표준 회사코드·계정 참조는 일반 Association, 소유 자식 항목은 Composition으로 모델링합니다.
- Projection에서는 관계를 해당 Projection 대상으로 redirect합니다.
- Behavior에서 부모 기반 잠금·권한, 항목 생성 및 검증 등을 설계합니다.
- 항목 없는 요청을 회계 전표로 전기해도 된다는 뜻은 아닙니다.

## 개발 오류와 원인

| 실수 | 원인·결과 |
|---|---|
| 전표번호만 연결 | 회사·연도 식별이 빠져 다른 전표 항목이 섞임 |
| 여러 항목과 연결 후 헤더 금액 합산 | 헤더가 항목 수만큼 반복되어 중복 집계 |
| 통화 없이 금액 합산 | 서로 다른 통화가 섞임 |
| 요청 저장을 FI 전기로 간주 | 요청 데이터만 저장되고 회계 장부에 반영되지 않음 |

## 참고 자료

- [SAP BKPF·BSEG 설명](https://help.sap.com/doc/484dc3450ff1424f8846bcbd60eaebc2/3.20/en-US/Sample%20Content%20Process%20Mining%20on%20SAP%20S4HANA%20Accounts%20Receivable.pdf)
- [SAP 전표 통화와 항목 금액](https://help.sap.com/docs/SUPPORT_CONTENT/fiaccounting/3361880798.html)
- [SAP RAP Composition](https://learning.sap.com/courses/building-transactional-apps-with-the-abap-restful-application-programming-model/defining-compositions-in-odata-ui-services)

[FI 학습 순서](02-fi-roadmap.md) · [용어집](05-fi-glossary.md)
