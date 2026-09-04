# 온:길 (On:Gil)

> 시각장애인과 안내견이 폭염 속에서도 열환경 위험을 피해
> 상대적으로 열부담이 낮은 경로를 선택할 수 있도록 돕는 AI 이동지원 서비스

`Python` `GMM` `GeoPandas` `NetworkX` `FastAPI` `React Native` `Expo`

🏆 **EST AI Challengers 1기 장려상**

---

## 프로젝트 개요

온:길은 일반 최단 보행경로와 열환경을 고려한 경로를 비교해 제공하는 AI 이동지원 서비스입니다.

최종 앱 런타임은 **2026-08-15 16:00 KST** 기준 송파구 전체 보행로를 분석한
**3군집 GMM 산출물**을 사용합니다.

보행로의 그늘, 누적 유효 일사량, 포장면 흡수율을 기반으로
각 Edge의 상대적인 열환경을 분석하고,
이를 Heat Cost로 변환해 경로 탐색에 반영합니다.

시각장애인 사용자를 고려해
출발지·도착지 음성 입력과 GPS 기반 음성 길안내 기능도 제공합니다.

---

## 현재 적용 모델

최종 모델은 다음 3개 Feature를 사용합니다.

- `shade_ratio` : 그늘 비율
- `cumulative_effective_solar_mj_m2` : 누적 유효 일사량
- `surface_absorptivity` : 포장면 흡수율

Feature는 Pearson 상관분석과 VIF를 이용해
중복성과 다중공선성을 확인한 뒤 최종 입력값으로 선정했습니다.

### GMM Clustering

보행 Edge별 실제 노면온도 정답 데이터가 충분하지 않아
절대온도 예측 대신 상대적인 열환경을 구분하는 비지도학습 방식으로 접근했습니다.

Gaussian Mixture Model(GMM)을 통해 각 Edge를 다음 3개 상태로 구분합니다.

| Heat Cost | 상태 |
|---:|---|
| `0` | 쾌적 |
| `1` | 주의 |
| `2` | 고온 |

GMM은 군집 결과뿐 아니라
각 Edge가 고온 군집에 속할 확률인 `P(High)`도 계산합니다.

---

## 경로 탐색

### 최단경로

```text
weight = length_m
