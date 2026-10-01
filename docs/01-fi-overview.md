# FI 개요와 주요 영역

## 1. FI란?

**FI(Financial Accounting)는 SAP의 재무회계 영역**입니다. 회사의 거래를 회계 전표로 기록하고, 계정 잔액과 결산을 통해 재무상태표 및 손익계산서를 작성하는 기반을 제공합니다.

FI는 회사의 자산, 부채, 수익, 비용을 관리합니다. 구매와 판매 등 다른 업무에서 발생한 회계 관련 거래도 FI에 연결됩니다.

## 2. 영역별 분류

| 구분 | 영문 | 한국어 | 핵심 역할 |
|---|---|---|---|
| GL / FI-GL | General Ledger Accounting | 총계정원장 회계 | 계정별 기록, 잔액, 결산, 재무제표 |
| AP / FI-AP | Accounts Payable | 매입채무 회계 | 공급업체별 채무, 지급, 반제 |
| AR / FI-AR | Accounts Receivable | 매출채권 회계 | 고객별 채권, 입금, 반제 |
| AA / FI-AA | Asset Accounting | 자산회계 | 고정자산 취득, 감가상각, 이전, 처분 |
| TAX | Tax | 세금 관련 기능 | 매입·매출 세금, 세금코드, 원천세 등 |
| General | 문맥에 따라 다름 | 공통 설정 또는 일반 업무 | 회사코드, 회계연도, 전표, 전기기간 등일 수 있음 |

GL·AP·AR·AA는 회계 업무별 영역입니다. TAX는 여러 업무에 걸쳐 적용되는 세금 기능입니다. General은 원래 메뉴 또는 문서를 확인해야 의미를 확정할 수 있으므로, 이 여섯 항목을 동일한 수준의 공식 하위 모듈 목록으로 해석하지 않습니다.

SAP 공식 교육은 GL·AP·AR·AA를 주요 FI 업무 영역으로 다룹니다. [SAP FI 교육 범위](https://learning.sap.com/learning-journeys/implementing-financial-accounting-in-sap-s-4hana)

## 3. GL: 전체 장부

GL은 현금, 예금, 매출, 비용, 채무 등의 G/L 계정에 거래를 기록합니다.

사무용품을 현금 100,000원으로 구매한 단순 예시(세금 생략):

| 차변 | 대변 |
|---|---|
| 소모품비 100,000원 | 현금 100,000원 |

- **전표(Accounting Document / Journal Entry)**: 회계 거래를 기록한 문서.
- **전기(Posting)**: 거래를 회계 장부에 반영하는 처리.
- **계정과목표(Chart of Accounts)**: G/L 계정의 목록과 구조.

GL은 이런 기록을 모아 계정 잔액과 재무제표를 구성합니다. [SAP GL 기본 기능](https://learning.sap.com/courses/exploring-end-to-end-business-processes-in-sap-business-suite/managing-general-ledger-accounting_d320f1f2-a708-4f7c-9c4d-872ceea3b6a2)

## 4. AP: 공급업체에 지급할 돈

AP는 공급업체별 송장과 채무, 지급 및 반제를 관리합니다. 송장을 등록하고 아직 지급하지 않았다면 지급할 채무가 남습니다.

- **미결항목(Open Item)**: 아직 정산이 완료되지 않은 항목.
- **반제(Clearing)**: 송장과 지급 등의 관련 항목을 연결하여 정산하는 처리. 부분지급이나 잔액항목 처리에서는 미결 상태가 남을 수 있습니다.
- **지급조건(Payment Terms)**: 지급기한 및 할인조건 등을 정하는 조건.
- **조정계정(Reconciliation Account)**: 보조원장의 상세 거래를 GL에 연결하는 계정.

AP는 공급업체별 상세 내역을, GL은 관련 계정별 합계를 관리합니다. [SAP 보조원장과 GL의 연결](https://learning.sap.com/courses/configuring-asset-accounting-in-sap-s4hana/processing-acquisitions)

## 5. AR: 고객에게 받을 돈

AR은 고객별 청구, 채권, 입금 및 반제를 관리합니다.

| AP | AR |
|---|---|
| 공급업체에 지급할 돈 | 고객에게 받을 돈 |
| 공급업체별 채무 | 고객별 채권 |
| 지급 처리 | 입금 처리 |

고객별 상세 회계도 조정계정을 통해 GL과 연결됩니다. [SAP FI 구성 설명](https://learning.sap.com/courses/outlining-processes-in-record-to-report/identifying-the-parts-of-the-general-ledger-balance-sheet-and-profit-loss-accounts-that-record-all-value-related-business-transactions_ddc27973-a585-479b-a2df-dd3789ee0fb6)

## 6. AA: 고정자산

AA는 건물, 기계, 차량, 컴퓨터 같은 고정자산의 생애를 관리합니다.

- **취득**: 자산을 구매하거나 만들어 등록.
- **감가상각**: 자산의 취득원가를 사용기간에 걸쳐 비용으로 배분.
- **이전**: 자산을 다른 조직 또는 회사코드 등으로 이동.
- **처분**: 자산을 매각하거나 폐기.
- **장부가액**: 장부에 남아 있는 자산 가치. 단순한 경우 취득가액에서 누적 감가상각 등을 차감한 금액.

GL이 기계장치 계정의 총액을 보여준다면 AA는 각 기계의 취득가액, 감가상각 및 장부가액을 상세히 관리합니다. [SAP 자산회계 설명](https://learning.sap.com/courses/outlining-processes-in-record-to-report/identifying-the-parts-of-the-general-ledger-balance-sheet-and-profit-loss-accounts-that-record-all-value-related-business-transactions_ddc27973-a585-479b-a2df-dd3789ee0fb6)

## 7. TAX: 세금 기능

TAX는 AP·AR·GL 등의 거래에 적용되는 세금 기능입니다. 매입·매출 세금과 원천세 등은 세금 종류 및 국가별 설정에 따라 처리합니다.

**세금코드(Tax Code)**는 세금 계산과 전기 처리를 제어하는 데 사용되며, 관련 설정과 함께 세율과 세금 계정 등을 결정합니다. 모든 세금 기능이 하나의 세금코드로 처리되는 것은 아닙니다.

[SAP 세금 전기 설명](https://help.sap.com/docs/SAP_S4HANA_CLOUD/af9ef57f504840d2b81be8667206d485/0970b6531de6b64ce10000000a174cb4.html)

## 8. General: 문맥 확인이 필요한 명칭

General이 Financial Accounting Global Settings를 뜻한다면 FI의 공통 설정을 가리킬 수 있습니다.

| 용어 | 의미 |
|---|---|
| Company Code / 회사코드 | 개별 재무제표를 작성하는 기본 회계 조직 단위 |
| Fiscal Year / 회계연도 | 회계 실적을 집계하는 연도 |
| Posting Period / 전기기간 | 거래를 회계에 반영하는 기간 |
| Document Type / 전표유형 | 전표를 구분하는 유형 |
| Posting Date / 전기일 | 거래가 어느 회계기간에 반영되는지 판단하는 날짜 |

전기기간이 닫혀 있으면 해당 기간으로 전표를 전기할 수 없습니다. [SAP 전기기간 설명](https://help.sap.com/docs/SAP_ERP_SPV/56064c98e77e41c48628aa69987a1290/b9e8c5536a51204be10000000a174cb4.html)

General이 General Ledger의 줄임말이거나 회사 자체의 공통 메뉴일 수도 있습니다. 원래 메뉴명 또는 문서 제목을 확인해야 정확히 구분할 수 있습니다.

## 9. 여러 영역이 연결되는 거래 예시

기계를 공급업체로부터 외상으로 구매하면 한 업무 거래에 여러 영역이 관여합니다.

| 영역 | 역할 |
|---|---|
| AA | 개별 기계의 취득 내역 관리 |
| AP | 공급업체에 지급할 채무 관리 |
| TAX | 해당 구매의 세금 처리 |
| GL | 자산·채무·세금의 계정별 회계 금액 반영 |
| 공통 설정 | 회사코드, 전기일, 전기기간 등 적용 |

위 표는 업무 역할의 설명입니다. 실제 생성 전표와 기술적 상계계정 등은 SAP 버전 및 설정에 따라 확인해야 합니다. [SAP 통합 자산 취득](https://learning.sap.com/courses/configuring-asset-accounting-in-sap-s4hana/processing-acquisitions)

## 10. FI와 RAP의 관계

**FI는 회계 업무 영역이고, RAP은 애플리케이션 개발 기술입니다.**

RAP은 ABAP RESTful Application Programming Model의 약자입니다. CDS 데이터 모델과 업무 동작을 정의하여 OData 서비스 및 Fiori 앱을 개발하는 데 사용합니다. [SAP RAP 공식 설명](https://learning.sap.com/courses/building-transactional-apps-with-the-abap-restful-application-programming-model/exploring-the-concept-and-architecture-of-rap)

공급업체 지급요청 앱을 만드는 개념 예시:

| 구성 | 역할 |
|---|---|
| FI 업무 지식 | 공급업체, 미결항목, 지급기한, 금액, 반제 이해 |
| CDS | 지급요청 및 관련 회계 데이터 조회 모델 |
| RAP Business Object | 지급요청 생성·수정, 검증, 승인요청 등의 동작 |
| OData 서비스 | 화면에서 데이터와 업무 동작에 접근하는 통로 |
| Fiori 화면 | 사용자의 지급요청 입력 및 조회 |

지급요청 저장과 실제 FI 전표 전기는 별개의 업무 단계입니다. 표준 회계 처리는 시스템에서 지원하는 API와 확장 방식으로 연결해야 합니다. 구체적인 구현에서는 SAP 제품, 릴리스, 배포 환경, API 공개 상태, RAP 트랜잭션 규칙을 확인합니다.

## 핵심 정리

- **GL**: 회사 전체의 계정별 장부.
- **AP**: 공급업체에 지급할 돈.
- **AR**: 고객에게 받을 돈.
- **AA**: 개별 고정자산.
- **TAX**: 거래에 적용되는 세금 기능.
- **General**: 원래 메뉴나 문서에 따라 의미가 달라짐.
- **RAP**: 이러한 업무를 다루는 앱과 서비스를 개발하는 기술.
