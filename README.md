# 온:길 (On:Gil)

> 시각장애인과 안내견이 폭염 속에서도 열환경 위험을 피해 안전하게 이동할 수 있는 경로를 추천하는 AI 서비스

[Python](https://www.python.org/) ([image](https://img.shields.io/badge/Python-3.10-blue))
[FastAPI](https://fastapi.tiangolo.com/) ([image](https://img.shields.io/badge/FastAPI-backend-teal))
[scikit-learn](https://scikit-learn.org/) ([image](https://img.shields.io/badge/scikit--learn-GMM-orange))

---

## Why — 왜 만들었는가

폭염 상황에서는 같은 거리의 보행로라도 그늘, 일사량, 포장면 특성 등에 따라 열환경이 크게 달라질 수 있습니다.

특히 시각장애인은 이동 중 그늘이나 노면 상태와 같은 시각 정보를 즉각적으로 확인하기 어렵고, 안내견은 노면에 직접 노출되기 때문에 보행 경로의 열환경이 이동 안전에 중요한 요소가 될 수 있습니다.

기존 경로 안내 서비스가 주로 거리와 시간을 기준으로 경로를 탐색한다면, 온:길은 여기에 **보행로의 상대적인 열환경**이라는 새로운 기준을 추가하기 위해 시작한 프로젝트입니다.

## What — 무엇을 만들었는가

보행로의 그늘·포장재·일사량 등을 분석해 각 Edge의 상대적인 열환경을 분류하고, 이를 **Heat Cost**로 변환해 경로 탐색에 반영하는 서비스입니다.

사용자는

- 일반 최단 경로
- 상대적으로 열부담이 낮은 온:길 추천 경로

를 비교할 수 있으며, 시각장애인 사용자를 고려해 출발지·도착지 음성 입력과 GPS 기반 음성 길안내 기능도 제공합니다.

최종 앱 런타임은 `2026-08-15 16:00 KST` 기준 송파구 전체 보행로를 분석한 3군집 GMM 결과를 사용합니다.

## How — 어떻게 풀었는가

### 처리 파이프라인

```text
Raw Data → Spatial Preprocessing → Feature Engineering
→ GMM 군집화 → Heat Cost 산출 → Dijkstra Routing → FastAPI → App
