# Medusa에서 되는 것과 추가할 것

[추측] 여기서 주문은 고객에게 파는 판매주문이다. 발주는 공급사에 사는 구매발주이다. 두 업무를 같은 것으로 취급하지 않는다. 기본 기능이 있어도 아이엠의 업무 규칙과 맞는지는 별도로 확인한다.

## B2B 도매 기능

| 기능 | 확인한 범위 | 아이엠 설계 |
| --- | --- | --- |
| 고객·고객그룹 | [확인한 사실](https://github.com/medusajs/medusa/blob/develop/packages/modules/customer/src/models/customer.ts) 고객 계정 여부, 회사명, 그룹 연결이 있다. | [추측] 거래처의 담당자 계정과 가격 적용 그룹에 재사용한다. |
| 그룹별 도매 단가 | [확인한 사실](https://github.com/medusajs/medusa/blob/develop/integration-tests/http/__tests__/price-list/admin/price-list.spec.ts) `customer.groups.id` 조건의 가격표 생성 테스트가 있다. | [추측] 거래처 그룹을 가격표에 연결한다. 회사 접근 권한은 별도 검사한다. |
| 수량별 단가 | [확인한 사실](https://github.com/medusajs/medusa/blob/develop/packages/modules/pricing/src/models/price.ts) 가격에 `min_quantity`, `max_quantity`, 통화가 있다. | [추측] 박스 환산과 최소 주문 수량을 추가한다. 중복 가격표의 우선순위는 시험 후 정한다. |
| 단가 적용 기간 | [확인한 사실](https://github.com/medusajs/medusa/blob/develop/packages/modules/pricing/src/models/price-list.ts) 가격표에 시작일·종료일·상태가 있다. | [추측] 계약 기간과 연결한다. AI가 가격표를 직접 확정하지 못하게 한다. |
| 회사·직원·회사별 권한 | [확인한 사실](https://github.com/medusajs/medusa/blob/develop/www/apps/resources/app/recipes/b2b/page.mdx) 공식 가이드는 `Company`, `Employee`를 별도 모듈 예시로 설명한다. | [추측] 회사, 담당자, 구매 권한을 만든다. `company_name`만으로 회사 계정이 완성됐다고 보지 않는다. |
| 견적 협상 | [확인한 사실](https://github.com/medusajs/medusa/blob/develop/www/apps/resources/app/examples/guides/quote-management/page.mdx) 별도 Quote Module로 견적과 고객 수락을 구현한다. | [추측] 필요하면 견적을 추가한다. 고객의 견적 수락과 사내 발주 승인을 구분한다. |
| 임시 주문 | [확인한 사실](https://github.com/medusajs/medusa/blob/develop/packages/core/core-flows/src/draft-order/workflows/convert-draft-order.ts) 임시 주문을 정식 주문으로 바꾸는 workflow가 있다. | [추측] 사람 승인 후 임시 주문도 기록한다. AI 초안은 승인 전 Medusa에 저장하지 않는다. |
| 사내 결재 | [추측] 금액별 결재선·대리 승인·구매발주 결재를 기본 기능으로 확인하지 못했다. | [추측] The_IM 전이 규칙과 별도 ERP 실행 검사를 사용한다. |
| 위치별 재고 | [확인한 사실](https://github.com/medusajs/medusa/blob/develop/packages/modules/inventory/src/models/inventory-level.ts) 품목·위치별 현재·예약·입고 예정 수량이 있다. | [추측] 창고별 재고를 재사용한다. 창고·판매 채널 연결은 시험한다. |
| 여러 위치의 가용량 | [확인한 사실](https://github.com/medusajs/medusa/blob/develop/packages/modules/inventory/src/repositories/inventory-level.ts) 선택한 위치의 현재 수량에서 예약 수량을 빼서 합산한다. | [추측] 입고 예정 수량을 현재 가용량에 더하지 않는다. |
| 재고 예약 | [확인한 사실](https://github.com/medusajs/medusa/blob/develop/packages/core/core-flows/src/cart/steps/reserve-inventory.ts) 품목 잠금 후 재고를 예약한다. 이 단계는 `location_ids[0]`을 선택한다. | [추측] 여러 창고의 자동 분할 예약·출고는 지원을 확인한 것으로 보지 않는다. |
| 주문 수정 | [확인한 사실](https://github.com/medusajs/medusa/blob/develop/packages/core/core-flows/src/order/workflows/order-edit/request-order-edit.ts) 변경 요청을 기록한다. [확인한 사실](https://github.com/medusajs/medusa/blob/develop/packages/core/core-flows/src/order/workflows/order-edit/confirm-order-edit-request.ts) 별도 확정 workflow가 있다. | [추측] 확정 전에 사람 승인, 단가·재고 재검사를 추가한다. |
| 고객 반품 | [확인한 사실](https://github.com/medusajs/medusa/blob/develop/integration-tests/http/__tests__/returns/returns.spec.ts) 판매주문에 대한 반품 생성·변경 테스트가 있다. | [추측] 검사 결과와 재판매 가능 여부를 추가한다. 공급사에 돌려주는 매입 반품은 별도 업무로 둔다. |
| 공급사 구매발주 | [추측] 핵심 모듈·workflow·API 조사에서 전용 구현을 확인하지 못했다. | [추측] 공급사, 발주 품목, 납기, 입고 연결을 만든다. |
| 실물 창고 이동 | [추측] 위치 재고는 있지만 이동 출고·운송 중·입고를 묶는 전용 업무는 확인하지 못했다. | [추측] 실물 이동 기록을 별도 설계한다. 재고 숫자 두 개를 고치는 것으로 끝내지 않는다. |
| 로트·유효기간 | [확인한 사실](https://github.com/medusajs/medusa/blob/develop/packages/modules/inventory/src/models/inventory-item.ts) 확인한 재고 품목 모델에는 로트·유효기간 전용 필드가 없다. | [추측] K-뷰티 상품의 추적이 필요하면 로트와 유효기간별 잔량을 만든다. |
| 재고 예측 | [추측] 수요예측·발주 추천의 기본 제공을 확인하지 못했다. | [추측] 과거 판매와 입고 소요 기간으로 추천만 만든다. 자동 발주하지 않는다. |
| 세금 기능의 범위 | [확인한 사실](https://github.com/medusajs/medusa/blob/develop/packages/core/core-flows/src/draft-order/workflows/convert-draft-order.ts) 주문 전환 workflow에는 세금 줄 갱신이 연결되어 있다. | [추측] 이를 한국 회계·세무·전자세금계산서 지원으로 간주하지 않는다. 해당 업무는 범위 밖이다. |

[추측] 미확인 기능의 검색 범위는 `packages/modules`, `packages/core/core-flows/src`, `packages/medusa/src/api`이다. 구매 관련 검색어는 `purchase.?order`, `procurement`, `supplier`이다. 재고 관련 검색어는 `stock.?transfer`, `forecast`, `lot`, `expiry`, `expiration`이다. 이 검색은 저장소 전체의 절대적 부재를 증명하지 않는다.

## MIT와 Enterprise의 경계

[확인한 사실](https://github.com/medusajs/medusa/blob/develop/ENTERPRISE-LICENSE.md) RBAC와 SSO는 Enterprise 기능이다. RBAC는 역할별 접근 권한, SSO는 통합 로그인을 뜻한다. 아래 목록의 디렉터리는 하위 전체를 포함한다. 변환·컴파일·번들 결과에도 Enterprise 조건이 적용된다.

[추측] 아래 코드를 ERP 권한·승인·인증의 기반으로 사용하지 않는다. 별도 MIT 확장의 업무 권한과 일반 인증을 구분한다. 제외 구성의 실행 가능성이 확인될 때까지 설치·배포 판단은 보류한다.

### Enterprise로 명시된 경로 전체

[확인한 사실](https://github.com/medusajs/medusa/blob/develop/ENTERPRISE-LICENSE.md) RBAC 모듈과 백엔드 제외 목록이다.

```text
packages/modules/rbac/
packages/core/core-flows/src/rbac/
packages/medusa/src/api/admin/rbac/
packages/core/types/src/rbac/
packages/core/types/src/http/rbac/
packages/core/framework/src/policies/
packages/core/framework/src/http/middlewares/check-permissions.ts
packages/core/framework/src/http/utils/policies/rbac-field-filter.ts
packages/core/utils/src/modules-sdk/define-policies.ts
packages/core/utils/src/modules-sdk/policy-to-types.ts
packages/medusa/src/feature-flags/rbac.ts
packages/medusa/src/feature-flags/rbac-filter-fields.ts
packages/medusa/src/modules/rbac.ts
packages/modules/link-modules/src/definitions/invite-rbac-role.ts
packages/modules/link-modules/src/definitions/user-rbac-role.ts
```

[확인한 사실](https://github.com/medusajs/medusa/blob/develop/ENTERPRISE-LICENSE.md) 사용자·초대 기능 안의 RBAC 제외 목록이다.

```text
packages/core/core-flows/src/invite/steps/get-invite-roles.ts
packages/core/core-flows/src/invite/steps/validate-roles-exist.ts
packages/core/core-flows/src/user/steps/get-assignable-policies.ts
packages/core/core-flows/src/user/steps/get-assignable-roles.ts
packages/core/core-flows/src/user/steps/validate-user-role-permissions.ts
packages/core/core-flows/src/user/workflows/assign-user-roles.ts
packages/core/core-flows/src/user/workflows/get-assignable-policies.ts
packages/core/core-flows/src/user/workflows/get-assignable-roles.ts
packages/core/core-flows/src/user/workflows/remove-user-roles.ts
packages/medusa/src/api/admin/users/[id]/roles/
```

[확인한 사실](https://github.com/medusajs/medusa/blob/develop/ENTERPRISE-LICENSE.md) SDK와 관리자 화면의 제외 목록이다.

```text
packages/core/js-sdk/src/admin/rbac-policy.ts
packages/core/js-sdk/src/admin/rbac-role.ts
packages/admin/dashboard/src/routes/policies/
packages/admin/dashboard/src/routes/roles/
packages/admin/dashboard/src/hooks/api/rbac-policies.tsx
packages/admin/dashboard/src/hooks/api/rbac-roles.tsx
packages/admin/dashboard/src/hooks/use-require-rbac-feature.tsx
packages/admin/dashboard/src/components/common/required-permissions-section/
packages/admin/dashboard/src/lib/permissions/
packages/admin/dashboard/src/providers/permissions-provider/
```

[확인한 사실](https://github.com/medusajs/medusa/blob/develop/ENTERPRISE-LICENSE.md) RBAC 테스트의 제외 목록이다.

```text
packages/modules/rbac/integration-tests/
integration-tests/http/__tests__/rbac/
integration-tests/modules/__tests__/rbac/
integration-tests/modules/__tests__/rbac-match-endpoint-entities.spec.ts
```

[확인한 사실](https://github.com/medusajs/medusa/blob/develop/ENTERPRISE-LICENSE.md) SSO·OIDC의 제외 목록이다.

```text
packages/modules/providers/auth-oidc/
packages/medusa/src/modules/auth-oidc.ts
packages/core/types/src/auth/providers/oidc.ts
packages/medusa/src/api/auth/[auth_provider]/user/route.ts
packages/admin/dashboard/src/routes/login/components/sso-login.tsx
```

### 추가로 확인한 표시와 의존성

| 위치 | 확인 내용 |
| --- | --- |
| 루트 LICENSE | [확인한 사실](https://github.com/medusajs/medusa/blob/develop/LICENSE) Enterprise 예외를 둔 MIT 문구이다. |
| RBAC LICENSE | [확인한 사실](https://github.com/medusajs/medusa/blob/develop/packages/modules/rbac/LICENSE) 패키지에 Enterprise 조건을 명시한다. |
| OIDC LICENSE | [확인한 사실](https://github.com/medusajs/medusa/blob/develop/packages/modules/providers/auth-oidc/LICENSE) 패키지에 Enterprise 조건을 명시한다. |
| Medusa 패키지 | [확인한 사실](https://github.com/medusajs/medusa/blob/develop/packages/medusa/LICENSE) MIT와 Enterprise가 섞일 수 있음을 명시한다. |
| 관리자 화면 | [확인한 사실](https://github.com/medusajs/medusa/blob/develop/packages/admin/dashboard/LICENSE) 같은 혼합 라이선스 안내가 있다. |
| Framework | [확인한 사실](https://github.com/medusajs/medusa/blob/develop/packages/core/framework/LICENSE) 같은 혼합 라이선스 안내가 있다. |
| 공통 유틸리티 | [확인한 사실](https://github.com/medusajs/medusa/blob/develop/packages/core/utils/LICENSE) 같은 혼합 라이선스 안내가 있다. |
| 공통 타입 | [확인한 사실](https://github.com/medusajs/medusa/blob/develop/packages/core/types/LICENSE) 같은 혼합 라이선스 안내가 있다. |
| JS SDK | [확인한 사실](https://github.com/medusajs/medusa/blob/develop/packages/core/js-sdk/LICENSE) 같은 혼합 라이선스 안내가 있다. |
| Core Flows | [확인한 사실](https://github.com/medusajs/medusa/blob/develop/packages/core/core-flows/LICENSE) 같은 혼합 라이선스 안내가 있다. |
| InstantSearch 어댑터 | [확인한 사실](https://github.com/medusajs/medusa/blob/develop/packages/core/instantsearch-adapter/LICENSE) 같은 혼합 라이선스 안내가 있다. |
| 모듈 연결 | [확인한 사실](https://github.com/medusajs/medusa/blob/develop/packages/modules/link-modules/LICENSE) 같은 혼합 라이선스 안내가 있다. |
| 기본 설치 의존성 | [확인한 사실](https://github.com/medusajs/medusa/blob/develop/packages/medusa/package.json) RBAC와 OIDC를 직접 의존한다. [추측] 단순 기능 비활성화와 설치물 제외를 구분한다. |
| 루트 소개 | [확인한 사실](https://github.com/medusajs/medusa/blob/develop/README.md) Enterprise 및 라이선스 안내가 있다. [추측] 기능 채택의 근거로 사용하지 않는다. |
| Cloud 가격표 코드 | [확인한 사실](https://github.com/medusajs/medusa/blob/develop/www/apps/cloud/components/Pricing/FeatureSections/index.tsx) `Enterprise License` 표시가 있다. [추측] 표시 문구만으로 해당 파일 전체가 Enterprise라고 판정하지 않는다. |

[추측] Medusa의 파일명 조사에서는 `ee/` 경로를 찾지 못했다. Enterprise 코드는 위처럼 일반 패키지 안에도 있으므로 `ee/`만 빼는 방법은 채택하지 않는다. 파일 헤더의 별도 상용 표시도 재검토 대상으로 둔다.

[추측] 별도 The_IM 조사에서 `ee/LICENSE`, `ee/README.md`를 찾았다. 이 경로와 생성물에는 의존하지 않는다. The_IM의 일반 코드도 MIT가 아니므로 복사·가져오기 대상에서 제외한다.
