# 온:길 (On:Gil)

> 폭염 속 시각장애인과 안내견이
> 상대적으로 열부담이 낮은 길을 선택할 수 있도록 돕는
> AI 기반 보행 경로 추천 서비스

🏆 EST AI Challengers 1기 장려상

---

## Project Overview

기존 길찾기 서비스는 주로 거리와 시간을 기준으로 경로를 추천합니다.

온:길은 보행로의 그늘, 누적 일사량, 포장환경 등을 분석하여
각 보행 Edge의 상대적인 열환경을 계산하고,

- 일반 최단경로
- 상대적으로 열부담이 낮은 온:길 추천경로

를 비교해 제공합니다.

시각장애인 사용자를 고려해
음성 목적지 입력과 GPS 기반 음성 길안내 기능도 구현했습니다.

---

## My Role

**Data Preprocessing · AI Modeling**

프로젝트에서 공간·기상 데이터 전처리와
보행 Edge별 열환경 모델링을 중심으로 담당했습니다.

- 송파구 보행 네트워크 전처리
- 건물·가로수·포장재 데이터 연결
- Edge별 열환경 Feature 생성
- Pearson / VIF 기반 Feature 검토
- GMM 기반 3단계 열환경 군집화
- Cluster → Heat Cost 0·1·2 변환
- GMM P(High)를 활용한 경로 열위험 점수 구성
- Heat Cost와 Dijkstra 경로 탐색 연계
- 현장 측정을 통한 상대적 열환경 방향성 검토

> 모바일 앱과 FastAPI 백엔드는 팀 협업을 통해 통합했습니다.

---

## Why this model?

보행 Edge별 실제 노면온도 정답 데이터가 존재하지 않았기 때문에
절대온도를 예측하는 지도학습 방식에는 한계가 있었습니다.

따라서 문제를

**“이 도로는 몇 ℃인가?”**

가 아니라

**“어느 도로가 상대적으로 열부담이 높은가?”**

로 다시 정의했습니다.

이를 위해 Gaussian Mixture Model(GMM)을 이용하여
보행로를 쾌적·주의·고온의 3개 상태로 군집화했습니다.

---

## Model Pipeline

```text
공간·기상 데이터
↓
보행 Edge 생성
↓
Feature Engineering
↓
Pearson / VIF
↓
GMM 3 Clusters
↓
Heat Cost 0 / 1 / 2
↓
Dijkstra
↓
최단경로 vs 온:길 추천경로
↓
App
