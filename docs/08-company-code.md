# 회사코드: SAP GUI와 RAP 연결

> **회사코드: 어느 회사의 회계인가?**  
> **G/L 계정: 어떤 종류의 거래인가?**  
> **전표: 어떤 거래를 얼마로 기록했는가?**

## 1. 회사코드란?

**회사코드(Company Code)는 완전하고 독립적인 회계 기록과 개별 재무제표를 작성할 수 있는 외부회계의 기본 조직 단위**입니다. 회계 계정이나 로그인 계정이 아닙니다.

일반적으로 법인에 대응하지만 반드시 법인 하나와 회사코드 하나가 일대일인 것은 아닙니다.

| 회사코드 — 예시 | 회계 조직 |
|---|---|
| 1000 | 한국 제조회사 |
| 2000 | 한국 유통회사 |

같은 SAP 시스템과 계정과목표를 사용해도 각 회사의 거래와 잔액은 구분합니다.

## 2. 회사코드에 연결되는 설정

| 용어 | 의미·관계 |
|---|---|
| 계정과목표 | 사용할 G/L 계정의 목록과 구조 |
| 회사코드 통화 | 회사 회계의 기본 통화. 추가 통화는 원장 등 관련 설정에 따라 관리 |
| 회계연도 변형 | 회계연도를 기간으로 나누는 규칙 |
| 전기기간 변형 | 전기 가능한 기간을 관리하는 설정 구분 |
| G/L 계정 회사코드별 데이터 | 해당 회사에서 계정을 사용하는 방식 |

계정과목표에 계정이 존재하는 것과 특정 회사코드에서 그 계정을 사용할 수 있는 것은 별개의 확인 사항입니다.

## 3. SAP GUI에서 사용

SAP GUI는 사용자가 SAP 업무 화면에 접속하는 프로그램입니다. 아래는 전통적인 ECC와 S/4HANA 온프레미스·Private Edition의 대표 예시입니다. 실제 가용성은 버전·권한에 따라 확인하며 Public Edition은 해당 구성·Fiori 방식을 사용합니다.

| 작업 | 트랜잭션 | 회사코드의 역할 |
|---|---|---|
| 회사코드 정의 | OX02 | 명칭 등 조직 정보 관리 |
| 회사코드 공통 설정 | OBY6 | 회계 관련 기본 설정 확인·관리 |
| 회계 전표 조회 | FB03 | 조회할 회사의 전표 지정 |

설정 업무와 일상적인 전표 업무를 구분합니다.

### 전표 입력 예시

```text
회사코드: 1000
전기일:   2026-10-05

차변: 급여  100만 원
대변: 예금  100만 원
```

당월 급여를 처음 비용으로 기록하고 지급하는 단순 예시입니다. 이 거래는 1000 회사의 비용과 예금에 반영됩니다. 잘못된 회사코드를 선택해도 설정·권한상 전기가 가능할 수 있으므로 성공 여부만으로 정확성을 판단하지 않습니다.

### 전표 식별

전통적인 FI 전표 조회에서는 **회사코드 + 전표번호 + 회계연도**를 함께 사용합니다.

| 회사코드 | 전표번호 — 예시 | 회계연도 |
|---|---|---|
| 1000 | 1900000001 | 2026 |
| 2000 | 1900000001 | 2026 |

두 행은 다른 전표일 수 있습니다. 개발에서 전표번호만으로 식별하면 안 됩니다. 여러 시스템을 통합하면 원천 시스템 등의 구분도 필요합니다.

## 4. 기술 필드와 표준 CDS

| 위치 | 대표 이름 |
|---|---|
| GUI | 회사코드 / Company Code |
| 전통적인 ABAP | BUKRS |
| 표준 CDS·API·RAP 모델 | CompanyCode 등 |

**I_CompanyCode**는 회사코드 정보를 다루는 표준 CDS 예시입니다.

| 필드 | 의미 | 사용 예 |
|---|---|---|
| CompanyCode | 회사코드, 대상의 식별 키 | 요청 데이터와 연결 |
| CompanyCodeName | 회사코드 명칭 | 화면에 설명 표시 |
| Currency | 회사코드 통화 | 회사의 기본 통화 참고 |
| Country | 국가·지역 코드 | 국가 관련 정보 조회 |

SAP 공식 자료에서 확인되는 필드 예시입니다. 실제 시스템의 필드·객체 존재 여부와 릴리스 계약은 ADT 및 View Browser에서 확인합니다. **표준 CDS라는 이유만으로 모든 ABAP Cloud 환경에서 사용 가능한 것은 아닙니다.** BTP ABAP 환경에 해당 S/4HANA 객체가 없다면 원격 API 등의 방식이 필요합니다.

회사코드의 Currency와 지급요청의 거래 통화는 항상 같지 않습니다. 외화 지급요청이 가능하다면 별도 통화·환산 정책을 설계해야 합니다.

## 5. RAP 지급요청 모델

아래 필드는 학습용으로 만든 사용자 정의 모델입니다.

| 필드 | 의미 | 키 |
|---|---|---|
| RequestUUID | 요청 식별자 | 예 |
| CompanyCode | 요청의 회계 조직 | 아니오 |
| Amount | 요청 금액 | 아니오 |
| Currency | 요청 금액의 통화 | 아니오 |

표준 회사코드는 요청에 참조되지만 요청의 소유 자식 데이터가 아닙니다. 따라서 일반적으로 **Association**으로 연결합니다.

## 6. Association CDS 예시

아래는 **설명용 Interface CDS 예시**입니다. 사용자 정의 테이블 `zfi_payreq`가 없으므로 그대로 실행할 수 있는 완성 앱은 아닙니다. DCL·Behavior·Projection·서비스는 별도 구현해야 합니다.

```abap
@EndUserText.label: '지급요청 - 회사코드 연결'
@AccessControl.authorizationCheck: #CHECK
define root view entity ZI_FIPaymentRequest
  as select from zfi_payreq as Request
  association [0..1] to I_CompanyCode as _CompanyCode
    on $projection.CompanyCode = _CompanyCode.CompanyCode
{
  key Request.request_uuid as RequestUUID,

      @ObjectModel.foreignKey.association: '_CompanyCode'
      Request.company_code as CompanyCode,

      @Semantics.amount.currencyCode: 'Currency'
      Request.amount as Amount,

      @Semantics.currencyCode: true
      Request.currency as Currency,

      _CompanyCode.CompanyCodeName as CompanyCodeName,
      _CompanyCode
}
```

### 어떻게 연결되는가?

```text
ZI_FIPaymentRequest                 I_CompanyCode
CompanyCode = 1000   ────────────>  CompanyCode = 1000
                                   CompanyCodeName = 한국 제조회사
```

- **Association:** 두 데이터 모델의 관계를 정의합니다.
- **_CompanyCode:** 이 관계에 붙인 이름입니다.
- **ON 조건:** 요청의 CompanyCode와 대상 CompanyCode가 같은 행을 연결합니다.
- **$projection.CompanyCode:** 현재 CDS에서 노출하는 CompanyCode 요소를 참조합니다.
- **[0..1]:** 요청 한 건에 대해 연결 대상이 없거나 최대 한 건이라는 선언입니다. 데이터와 실제 유일성에 맞게 지정해야 하며 존재 여부를 검증하는 기능은 아닙니다.
- **경로 표현식:** `_CompanyCode.CompanyCodeName`으로 연결된 회사 명칭을 가져옵니다.
- **_CompanyCode 노출:** 소비 계층에서 관계를 사용할 수 있도록 요소 목록에 포함합니다.

이 예시에서는 명칭 경로를 선택하므로 실행 쿼리에서 대상과의 관계가 평가됩니다. Association은 무조건 사전에 데이터를 복사하거나 모든 연결 데이터를 미리 읽는 기능이 아닙니다.

### Association과 Composition

| 구분 | 회사코드 참조 | 지급요청 헤더·항목 |
|---|---|---|
| 관계 | 요청이 기존 회사코드를 참조 | 헤더가 소유하는 항목 |
| 모델링 예 | Association | Composition |
| 생명주기 | 요청 삭제가 회사코드 삭제를 뜻하지 않음 | 부모·자식의 업무 생명주기를 함께 설계 |

단순히 두 필드가 같다는 이유로 Composition을 사용하는 것은 아닙니다.

## 7. 값 도움말

**값 도움말(Value Help)**은 입력 가능한 값을 검색·선택하는 기능이며 GUI의 F4 도움말과 비슷한 역할입니다.

서비스에 노출하고 권한을 갖춘 회사코드 값 도움말 CDS를 준비한 뒤 소비 계층에 다음과 같은 annotation을 적용할 수 있습니다.

```abap
@Consumption.valueHelpDefinition: [
  { entity: { name: 'ZC_CompanyCodeVH',
              element: 'CompanyCode' } }
]
CompanyCode,
```

`ZC_CompanyCodeVH`는 설명용 사용자 정의 이름이며 SAP 표준 객체가 아닙니다. 실제 구현에서는 사용 가능한 표준 값 도움말 또는 지원되는 소스를 이용한 사용자 정의 값을 선택합니다.

**Association만 선언했다고 원하는 값 도움말과 입력 검증이 모두 자동 구현되는 것은 아닙니다.**

## 8. 검증과 권한

| 구분 | 질문 | 구현 관점 |
|---|---|---|
| 업무 검증 | 회사코드가 유효한가? 관련 공급업체·계정이 사용 가능한가? | RAP Validation 및 표준 업무 검증 |
| 조회 권한 | 사용자가 이 회사의 데이터를 볼 수 있는가? | CDS DCL 등 조회 접근제어 |
| 변경 권한 | 이 회사의 요청을 생성·수정·승인해도 되는가? | RAP 권한 처리 |
| 화면 필터 | 어떤 회사의 데이터를 검색할 것인가? | UI 검색 조건. 권한을 대체하지 않음 |

회사코드 2000이 존재해도 사용자가 2000 업무를 처리할 권한이 없을 수 있습니다.

- `@AccessControl.authorizationCheck: #CHECK`만으로 회사코드 권한 규칙이 완성되지 않습니다. 적절한 DCL을 구현해야 합니다.
- 연결된 표준 CDS의 DCL이 사용자 정의 CDS의 권한을 자동으로 모두 보호한다고 가정하지 않습니다.
- 내부·특권 호출과 EML 호출 경로도 검토합니다.
- 여러 요청을 검증할 때는 회사코드를 모아 중복을 제거하고 적절한 일괄 조회를 고려합니다. 반복문 안의 건별 SELECT는 피합니다.

구체적인 권한 객체와 구현은 환경·BO·업무에 맞게 선정하며 ATC 통과를 코드 예시만으로 보장할 수 없습니다.

## 9. GUI와 RAP의 업무 연결

```text
회사코드 1000의 공급업체 송장·채무
                ↓
RAP 앱에서 1000 선택 및 권한 확인
                ↓
미결항목 조회 → 지급요청 생성·검증·승인
                ↓
지원되는 표준 업무/API로 회계 처리 연결
                ↓
생성된 FI 전표를 GUI 또는 Fiori에서 확인
```

**RAP 요청 저장과 FI 전표 전기는 별도 단계**입니다. 사용자 정의 요청 테이블에 회사코드를 저장한다고 장부에 자동 전기되지 않습니다. 실제 회계 처리는 지원 API, RAP 트랜잭션 호환성 및 오류·중복 처리 등을 확인해야 합니다.

## 공식 참고 자료

- [회사코드 정의](https://help.sap.com/docs/SAP_ERP/6a49d1604ffc4b908f9f78fba3824187/3a27d153c9684608e10000000a174cb4.html)
- [G/L 계정 구조](https://learning.sap.com/courses/customizing-core-settings-in-financial-accounting-in-sap-s4hana/maintaining-general-ledger-g-l-accounts)
- [OX02 관련 SAP 문서](https://userapps.support.sap.com/sap/support/knowledge/en/2855574)
- [OBY6 관련 SAP 문서](https://help.sap.com/docs/SUPPORT_CONTENT/fiaccounting/3361878918.html)
- [FB03 조회 정보](https://help.sap.com/docs/SAP_BUSINESSOBJECTS_FINANCIAL_INFORMATION_MANAGEMENT/42177c639aea4f559027e8a25064bf3b/f9cce1486faf1014878bae8cb0e91070.html?version=10.0.17)
- [I_CompanyCode와 Country 필드](https://developers.sap.com/tutorials/abap-env-ddls-extend/)
- [I_CompanyCode 회사명·통화 예시](https://hub.sap.com/odata/1.0/catalog.svc/Files%28%27ab9c3c55-8d3e-4a64-9ef4-e512b2d84c23_Configuration_and_User_Guide_for_Direct_Supplier_Down_Payment_Request%27%29/%24value)
- [RAP 권한 처리](https://learning.sap.com/courses/building-transactional-apps-with-the-abap-restful-application-programming-model/implementing-authority-checks)

[FI 학습 순서](02-fi-roadmap.md) · [통합 용어집](05-fi-glossary.md)

문서 작성일: 2026-10-05
