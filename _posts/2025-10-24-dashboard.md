---
title: "[TS 실습/고급] 데이터 시각화 대시보드 "
date: 2025-10-24 21:40:00 +0900
categories: [웹 개발, 타입스크립트]
tags: [웹개발, 타입스크립트, TS, 대시보드]
pin: true
math: true
mermaid: true
---

d:\typescript-프로젝트-추천 폴더에 있음

고급 프로젝트인 '데이터 시각화 대시보드'를 구현해 보겠습니다. 이 프로젝트는 TypeScript의 고급 타입을 활용하고, 외부 라이브러리 없이 직접 SVG(Scalable Vector Graphics)를 사용하여 동적인 차트를 렌더링하는 실력을 기를 수 있는 좋은 기회입니다.

기존 애플리케이션에 자연스럽게 통합하여, '데이터 시각화 대시보드' 카드의 '데모 실행하기' 버튼을 클릭하면 모달 창으로 대시보드 데모가 나타나도록 기능을 추가하겠습니다.

다음과 같이 파일을 추가하고 업데이트하여 기능을 구현합니다.

1. types.ts: 대시보드에서 사용할 MonthlySale과 CategoryData 타입을 추가합니다.
2. constants.tsx: 대시보드 데모에 사용할 월별 판매량 및 카테고리별 분포에 대한 목업 데이터를 추가합니다.
3. components/dashboard/: 대시보드 데모를 위한 4개의 새로운 컴포넌트를 추가합니다.
   1. DataDashboard.tsx: 여러 차트와 요약 카드를 포함하는 대시보드의 메인 컴포넌트입니다.
   2. SummaryCard.tsx: 총 매출과 같은 핵심 지표를 보여주는 재사용 가능한 카드 컴포넌트입니다.
   3. SalesLineChart.tsx: 순수 SVG로 월별 매출 추이를 보여주는 라인 차트 컴포넌트입니다.
   4. CategoryPieChart.tsx: 순수 SVG로 카테고리별 매출 분포를 보여주는 파이 차트 컴포넌트입니다.
4. App.tsx: 대시보드 데모 모달의 상태를 관리하고, 이를 열기 위한 로직을 추가합니다.
5. components/ProjectCard.tsx: '데이터 시각화 대시보드' 프로젝트에도 '데모 실행하기' 버튼이 보이도록 조건을 수정합니다.


아래 변경될 전체 코드입니다.
```
types.ts
check_circle
constants.tsx
check_circle
App.tsx
check_circle
components/ProjectCard.tsx
check_circle
components/dashboard/DataDashboard.tsx
check_circle
components/dashboard/SummaryCard.tsx
check_circle
components/dashboard/SalesLineChart.tsx
check_circle
components/dashboard/CategoryPieChart.tsx
check_circle
```
