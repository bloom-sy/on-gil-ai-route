# 온:길 (On:Gil)

서비스 한 줄 설명
기술 스택 / 수상

## 현재 적용 모델
- 분석 시점
- 분석 범위
- 입력 Feature
- GMM 3군집
- Heat Cost
- 경로 가중치
- P(High) / 경로 열위험 점수

## 전체 처리 구조
Raw Data
 → Spatial Preprocessing
 → Feature Engineering
 → GMM
 → Heat Cost
 → Routing
 → FastAPI
 → App

## 데이터 및 전처리
- 보행로
- 건물
- 가로수
- 포장재
- 기상자료
- 좌표계
- Edge 구성

## 모델
- Feature selection
- GMM
- cluster mapping
- P(High)

## 경로 계산
- shortest
- heat-aware
- Dijkstra
- API 반환값

## Repository Structure
실제 폴더 구조

## Setup
git clone
pip install ...

## Run
실행 명령어

## Validation
- 모델 산출물 검사
- 실측 결과
- 한계

## API / Output
JSON 예시

## My Contribution
짧게 담당 영역

## Data & License
데이터 출처/재배포 관련 내용
