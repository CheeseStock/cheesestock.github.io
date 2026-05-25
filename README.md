# CheeseStock Data

CheeseStock-Android M5+ 서버사이드 사전 계산 manifest 산출 repo.

## 산출

- `manifest/v1/pattern_match.json.gz` — 닮은꼴 매칭 (DTW + multi-window)
- `manifest/v1/market_ranking.json.gz` — 시가총액 상위 200 종목 일일 스냅샷
- `manifest/v1/checksum.json` — SHA256 무결성 검증

## 갱신 주기

매일 KST 15:30 (본인 PC Task Scheduler) 또는 KST 16:30 (GitHub Actions fallback).

## License

CC BY 4.0 (DataGo 공공누리 §4 정합).
