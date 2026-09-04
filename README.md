# 온:길 (On:Gil)

> 시각장애인과 안내견이 폭염 속에서도 열환경 위험을 피해 안전하게 이동할 수 있는 경로를 추천하는 AI 서비스

[![Python](https://img.shields.io/badge/Python-3.10-blue)](https://www.python.org/)
[![FastAPI](https://img.shields.io/badge/FastAPI-backend-teal)](https://fastapi.tiangolo.com/)
[![scikit-learn](https://img.shields.io/badge/scikit--learn-GMM-orange)](https://scikit-learn.org/)

---

## Why — 왜 만들었는가

여름철 폭염 시 보행로의 표면온도는 기온보다 훨씬 높게 올라갑니다 (예: 기온 30°C일 때 아스팔트 표면은 57°C 이상). 안내견은 신발을 신지 않는 것이 원칙이라, 뜨거운 노면이 직접적인 화상·건강 위험으로 이어질 수 있습니다.

기존 경로 안내 서비스는 최단거리만 고려할 뿐, **열환경 위험도**는 반영하지 않습니다. 온:길은 이 문제를 풀기 위해 시작된 프로젝트입니다.

## What — 무엇을 만들었는가

보행로의 그늘·포장재·일사량 등을 분석해 구간별 "열위험 점수"를 매기고, 이를 바탕으로 **최단 경로가 아닌 '가장 덜 뜨거운' 경로**를 추천하는 서비스입니다.

## How — 어떻게 풀었는가

### 처리 파이프라인

```
Raw Data → Spatial Preprocessing → Feature Engineering
→ GMM 군집화 → Heat Cost 산출 → Dijkstra Routing → FastAPI → App
```

1. **데이터 수집 및 전처리** — 보행로, 건물, 가로수, 포장재, 기상자료를 좌표계 기준으로 정합시켜 Edge 단위 그래프 구성
2. **Feature Engineering** — 그늘 비율, 최근 직사광 노출시간, 누적 유효 일사량, 열저장 지표, 포장면 흡수율 등 열환경 관련 피처 생성
3. **군집화 (GMM)** — 실측 노면온도 데이터를 구할 수 없어 비지도학습으로 접근. Pearson 상관분석·VIF로 피처를 정제하고 StandardScaler 적용 후 GMM으로 3개 열위험 군집(Low/Mid/High) 도출
4. **Heat Cost 설계** — 각 Edge에 군집 결과 기반 Heat Cost(0/1/2) 부여
5. **경로 탐색** — Dijkstra 알고리즘으로 Heat Cost를 반영한 최적 경로 계산 (최단 경로와 분리된 별도 옵션 제공)
6. **서비스화** — FastAPI로 경로 계산 결과를 JSON으로 반환, 앱에서 음성 길안내로 전달

### 모델 구조

| 단계 | 내용 |
|---|---|
| Feature Selection | Pearson 상관분석, VIF 기반 다중공선성 제거 |
| Clustering | GMM 3-cluster (StandardScaler 적용) |
| Risk Mapping | 군집 → Heat Cost(0/1/2) 매핑, P(High) 열위험 확률 산출 |
| Routing | Dijkstra, Heat-aware vs Shortest 경로 비교 |

## Result — 결과

- 열환경을 고려한 경로와 최단 경로를 비교해, 이동거리 증가 대비 열노출 위험 감소 효과를 검증
- 실제 노면온도 실측 데이터 부재라는 한계를 비지도학습 기반 근사로 보완
- FastAPI 기반 API로 경로 계산 결과를 앱에서 바로 소비 가능한 형태로 제공

## Repository Structure

```
ongil/
├── data/               # 원본 및 전처리 데이터
├── preprocessing/       # 공간 데이터 전처리 스크립트
├── model/               # GMM 군집화, Heat Cost 산출
├── routing/              # Dijkstra 기반 경로 탐색
├── api/                  # FastAPI 서버
└── README.md
```

## Setup & Run

```bash
git clone https://github.com/<repo>/ongil.git
cd ongil
pip install -r requirements.txt
uvicorn api.main:app --reload
```

## Validation & Limitations

- 실측 노면온도 데이터가 없어 비지도학습(GMM) 기반으로 열위험을 근사함 — 향후 실측 데이터 확보 시 지도학습으로 고도화 가능
- 노면살수(물뿌리기) 반영 여부, OSM 포장재 태그 정확도, 차량 통행으로 인한 실측 난이도 등이 남은 리스크

## API / Output 예시

```json
{
  "route": [...],
  "total_distance_m": 820,
  "heat_score": 0.34,
  "risk_level": "Low"
}
```

## My Contribution

데이터 전처리 및 모델링(피처 엔지니어링, GMM 군집 분석, Heat Cost 설계)을 담당했고, 앱 UI를 만드는 개발자에게 결과를 JSON 형태로 전달하며 협업했습니다.

## Data & License

- 보행로·건물·가로수·포장재: [출처 명시]
- 기상자료: [출처 명시]
- 「기후위기 취약계층 실태조사 안내서」 통계 인용
