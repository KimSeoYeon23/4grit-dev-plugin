---
name: vue-performance
description: Vue 성능 최적화. 번들, lazy loading, 코드 스플리팅, 로딩 속도, 최적화, 무거운 컴포넌트, 청크, 분석 요청 시 사용
disable-model-invocation: false
---

# Vue 성능 최적화 가이드

## 1. 번들 분석

프로젝트 빌드 도구에 맞는 분석 플러그인 설치 후 빌드.

### Vite
```bash
npm install -D rollup-plugin-visualizer
# vite.config.js에 플러그인 추가 후
npm run build
```

### Webpack (Vue CLI)
```bash
npm run build -- --report
```

### 목표 수치
- gzip 기준: vendor < 200kb, 개별 청크 < 50kb
- LCP (Largest Contentful Paint): < 2.5초
- FCP (First Contentful Paint): < 1.8초

---

## 2. 무거운 라이브러리 판단

### 번들 분석에서 확인
- 50kb 이상 차지하는 라이브러리
- 초기 로딩에 포함되는데 특정 페이지에서만 쓰는 것

### 일반적으로 무거운 유형
- 리치 텍스트 에디터, 차트, PDF, 지도 등

---

## 3. 무거운 컴포넌트 판단

### 코드에서 확인
- 무거운 라이브러리 import하는 컴포넌트
- 하위 컴포넌트 많이 포함
- template에 DOM 노드 많음 (반복문, 중첩 구조)
- 초기 마운트 시 API 호출 많음

### DevTools에서 측정
1. **Vue DevTools** → Performance 탭 → 컴포넌트별 렌더링 시간
2. **Chrome DevTools** → Performance → 녹화 후 해당 컴포넌트 마운트 시간 확인
3. **Network** → 해당 페이지 진입 시 로드되는 JS 청크 크기

### 동적 로드 고려 대상
- 모달/다이얼로그 (초기에 안 보임)
- 특정 탭에서만 보이는 콘텐츠
- 조건부 렌더링되는 복잡한 폼
- 관리자 전용 패널
- 프린트/내보내기 기능

---

## 4. 최적화 방법

### 라이브러리 트리쉐이킹
```js
// ❌ 전체 로드
import * as lib from 'some-library'

// ✅ 필요한 것만
import { SpecificModule } from 'some-library/core'
```

### 컴포넌트 동적 로드
```js
const HeavyComponent = defineAsyncComponent(() =>
  import('@/components/HeavyComponent.vue')
)
```

### 유틸 라이브러리 개별 import
```js
// ❌ 전체
import { something } from 'util-library'

// ✅ 개별 (지원하는 경우)
import something from 'util-library/something'
```

---

## 5. 코드 스플리팅

### 라우트 기반
```js
const routes = [
  {
    path: '/some-page',
    component: () => import('@/views/SomePage.vue')
  }
]
```

### 수동 청크 분리
빌드 설정에서 무거운 라이브러리를 별도 청크로 분리

---

## 6. v-if vs v-show

| | v-if | v-show |
|---|------|--------|
| 초기 로딩 | 조건 false면 렌더링 안 함 ✅ | 전부 렌더링 |
| 토글 시 | DOM 재생성 | display만 변경 ✅ |

- 초기에 안 보임 / 토글 적음 → **v-if**
- 자주 토글 → **v-show**

---

## 7. 분석 결과별 개선 가이드

### vendor 청크가 200kb 초과할 때
1. 번들 분석에서 가장 큰 라이브러리 확인
2. 해당 라이브러리가 모든 페이지에서 필요한지 검토
3. 특정 페이지에서만 쓰면 → 동적 import로 분리
4. 전체에서 쓰면 → 경량 대안 검토 또는 트리쉐이킹

### 특정 라이브러리가 50kb 이상일 때
1. 해당 라이브러리의 트리쉐이킹 지원 여부 확인
2. 지원하면 → 필요한 모듈만 import
3. 특정 페이지에서만 쓰면 → 해당 페이지 진입 시 동적 로드
4. 경량 대안이 있는지 검색 (`{라이브러리명} alternative lightweight`)

### 초기 로딩이 3초 이상일 때
1. Network 탭에서 가장 큰 JS 청크 확인
2. 해당 청크에 포함된 라이브러리/컴포넌트 파악
3. 초기 화면에 필요 없는 것들 동적 로드로 분리
4. 이미지 lazy loading 적용
5. API 호출 최적화 (병렬 처리, 필요한 것만)

### 특정 페이지만 느릴 때
1. 해당 라우트가 lazy loading 되어 있는지 확인
2. 페이지 내 무거운 컴포넌트 파악
3. v-if로 조건부 렌더링 + 동적 로드 조합
```js
<template>
  <div v-if="showHeavy">
    <AsyncHeavyComponent />
  </div>
</template>

<script setup>
const AsyncHeavyComponent = defineAsyncComponent(() =>
  import('@/components/HeavyComponent.vue')
)
</script>
```

### 리렌더링이 잦을 때
1. Vue DevTools에서 불필요한 리렌더링 확인
2. computed 대신 method 쓰고 있으면 → computed로 변경
3. v-for에 :key 제대로 설정되어 있는지 확인
4. 큰 리스트는 가상 스크롤 적용

---

## 8. 체크리스트

- [ ] 번들 분석 실행
- [ ] 50kb 이상 라이브러리 확인
- [ ] 무거운 라이브러리 import하는 컴포넌트 찾기
- [ ] 초기에 안 보이는 컴포넌트 동적 로드
- [ ] 라우트 lazy loading
- [ ] 유틸 라이브러리 개별 import
- [ ] v-if/v-show 적절히 사용
