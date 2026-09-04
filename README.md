# 온:길 (On:Gil)

> 시각장애인과 안내견을 위한 열환경 기반 산책 경로 추천 서비스

[![Python](https://img.shields.io/badge/Python-3.10-blue)](https://www.python.org/)
[![FastAPI](https://img.shields.io/badge/FastAPI-backend-teal)](https://fastapi.tiangolo.com/)
[![scikit-learn](https://img.shields.io/badge/scikit--learn-GMM-orange)](https://scikit-learn.org/)

## 한눈에 보기

| | |
|---|---|
| **문제** | 여름철 아스팔트 표면온도는 기온보다 훨씬 높음 (30°C → 57°C 이상). 안내견은 신발을 신지 않아 화상·건강 위험에 그대로 노출됨. 기존 경로 안내는 최단거리만 계산할 뿐 이 위험을 반영하지 않음 |
| **해결** | 보행로 구간별로 그늘·포장재·일사량을 분석해 열위험 점수를 매기고, 최단 경로 대신 **가장 덜 뜨거운 경로**를 추천 |
| **핵심 기술** | GMM 비지도 군집화로 열위험 3단계 분류 → Dijkstra로 Heat Cost 반영 경로 탐색 → FastAPI로 서빙 |

---

## 파이프라인

```
Raw Data → Spatial Preprocessing → Feature Engineering
→ GMM 군집화 → Heat Cost 산출 → Dijkstra Routing → FastAPI → App
```

| 단계 | 내용 |
|---|---|
| 데이터 전처리 | 보행로·건물·가로수·포장재·기상자료를 좌표계 기준으로 정합해 Edge 그래프 구성 |
| Feature Engineering | 그늘 비율, 최근 직사광 노출시간, 누적 유효 일사량, 열저장 지표, 포장면 흡수율 |
| Clustering | Pearson·VIF로 피처 정제 → StandardScaler → GMM 3-cluster (Low/Mid/High) |
| Heat Cost | 군집 결과를 Edge별 Cost(0/1/2)로 매핑, P(High) 열위험 확률 산출 |
| Routing | Dijkstra로 Heat-aware 경로와 최단경로를 각각 계산해 비교 |

## 폴더 구조

```
ongil/
├── backend/
│   ├── app/                    FastAPI + 경로 계산 로직
│   ├── data/models/.../
│   │   ├── runtime/             앱 실행용 GPKG·매핑 데이터
│   │   └── source/               모델링 Notebook·학습 소스
│   └── tests/
├── ongil-mobile/
│   ├── app/                     Expo Router 화면
│   └── src/                      API·지도·음성 인식 연동
└── src/ongil/                   연구·데이터 파이프라인
```

## 실행 방법

```bash
cd backend
pip install -r requirements.txt
uvicorn app.main:app --reload
```

```bash
cd ongil-mobile
npm run build:ios
npm run ios
```

실제 휴대폰에서는 `EXPO_PUBLIC_API_BASE_URL`을 개발 PC의 LAN IP로 바꾸고 같은 네트워크에 연결합니다.

## 앱 사용 흐름

1. 지원 주소 검색 또는 음성 입력
2. 빠른 산책(최단경로) / 시원한 산책(Heat-aware 경로) 선택
3. 시원한 산책은 최단경로와 Heat Cost 최적 경로의 거리·점수를 비교해 제시
4. 선택한 경로를 GPS 지도에 표시, 좌·우회전과 이탈·복귀·도착을 음성·진동으로 안내

## 한계 및 검증

- 실측 노면온도 데이터가 없어 비지도학습(GMM)으로 근사 — 실측 데이터 확보 시 지도학습으로 고도화 가능
- 노면살수 반영 여부, OSM 포장재 태그 정확도, 차량 통행으로 인한 실측 난이도가 남은 리스크
- `make test`, `make build-web`으로 회귀 검증

## API 응답 예시

```json
{
  "route": [...],
  "total_distance_m": 820,
  "heat_score": 0.34,
  "risk_level": "Low"
}
```

## Data & License

- 위 파일 참고
