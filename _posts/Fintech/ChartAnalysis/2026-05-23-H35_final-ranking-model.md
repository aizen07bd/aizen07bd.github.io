---
title: "H35. 좋은 후보가 여러 개일 때는 순위가 모델이 된다"
date: 2026-05-23 08:40:00 +09:00
categories: [Fintech, ChartAnalysis]
tags: [Finance, Analysis]
math: false
mermaid: false
image:
  path: /assets/img/posts/ChatGPT Image 2026년 5월 15일 오전 02_19_26.png # 대표 이미지 경로
  alt: "차트분석 가설 테스트 이미지"
---


# H35. 좋은 후보가 여러 개일 때는 순위가 모델이 된다

H35는 최종 랭킹 모델에 대한 가설이다.

H1-H34까지는 주로 “어떤 조건이 좋은 후보를 만드는가”를 봤다.

하지만 실제 서비스는 긴 후보 목록을 그대로 보내면 안 된다.

사용자에게 필요한 것은 오늘 볼 만한 1-2개다.

그래서 H35의 질문은 이것이다.

```text
여러 후보가 동시에 통과할 때
무엇을 기준으로 먼저 보여줄 것인가?
```

## 왜 이 가설을 세웠나

H2 promoted, H25D, H24C, H26-B 같은 조건은 각각 의미가 있었다.

문제는 조건이 늘어날수록 후보도 늘어난다는 점이다.

좋은 신호가 많아졌다고 해서 모두 매수 후보로 보내면 서비스 품질은 오히려 떨어진다.

H35는 그래서 “가설을 더 추가하는 단계”가 아니라, 살아남은 조건들을 추천 순서로 바꾸는 단계다.

```text
후보 생성과 후보 정렬은 다르다.
후보 생성은 entry anchor가 하고,
랭킹은 품질 필터, 타이밍 보조, 시장 맥락, 리스크 회피를 점수화한다.
```

## 이 가설의 기술 스펙

초기 통합 랭킹에서는 deterministic sort key를 만들었다.

중요한 필드는 다음이었다.

- `entry_score`
- `entry_grade`
- `entry_rank`
- `component_reason`

등급은 이렇게 나눴다.

| 등급 | 의미 |
|---|---|
| A_PRIMARY | H2 promoted 같은 핵심 진입 후보 |
| B_SECONDARY_FILTERED | H2 pair 확장 + 품질 필터 |
| C_SECONDARY_UNFILTERED_WATCH | 보조 후보지만 품질 필터 부족 |
| D_CONTEXT_ONLY | 설명용 맥락, 추천 제외 |
| X_NONE | 활성 컴포넌트 없음 |

post-H50 랭킹에서는 역할을 더 명확히 분리했다.

| 역할 | 랭킹 반영 방식 |
|---|---|
| entry_anchor | 후보 생성 + 기본 점수 |
| timing_overlay | 타이밍 점수 |
| ranking_boost | 시장/breadth 점수 |
| risk_filter | 과열/실패 회피 점수 |
| high_upside_watch | 고수익 관찰 점수 |
| context_only | 설명만, 점수 미반영 |
| reject | 제외 |

최종 점수는 역할별 점수를 더하되, 보조 역할이 과하게 누적되지 않도록 상한을 뒀다.

```text
timing_overlay max 1.20
ranking_boost max 1.00
risk_filter max 0.80
high_upside_watch max 0.60
```

## 세부 가설 A-E 전체 흐름

H35도 H34처럼 독립적인 H35-A-E 리포트 파일로 실행되지는 않았다.

대신 통합 추천 엔진과 post-H50 후보 랭킹 테스트에서 H35의 A-E 질문을 순차적으로 처리했다.

| 세부 | 검증 관점 | 실제 확인 방식 | 판단 |
|---|---|---|---|
| H35-A | 채택 컴포넌트 등급만으로 순위화 | A/B/C/D/X entry grade와 entry rank 생성 | 기본 구조 채택 |
| H35-B | 기대수익과 손절 전 고점 실패 리스크 반영 | top candidate backtest에서 path-aware return, stop-before-peak 비교 | 평가 기준 채택 |
| H35-C | 시장 breadth와 섹터/테마 확인 반영 | H26-B는 ranking boost, H27은 데이터 한계로 미승격 | 부분 채택 |
| H35-D | 청산 준비도와 보유 경로 반영 | entry-specific exit connection과 H34 경로 기반 청산 후보 연결 | 보류/경고 포함 |
| H35-E | 최종 복합 랭킹으로 top 1/top 2 선택 | post-H50 top 1/2/3/5 랭킹 테스트 | top 5 내부 계산, 메시지는 1-2개 |

## 초기 통합 랭킹 결과

초기 통합 엔진은 먼저 추천 가능한 행과 설명용 행을 분리했다.

최신일 결과는 다음과 같았다.

| 최신일 | entry eligible | top symbol | top grade | top score |
|---|---:|---|---|---:|
| 2026-05-07 | 1 | 001420 | B_SECONDARY_FILTERED | 1,075,068,335 |

이 단계의 의미는 크다.

컨텍스트만 있는 종목을 추천으로 잘못 올리지 않게 됐기 때문이다.

```text
설명할 수 있는 종목과 추천할 수 있는 종목은 다르다.
```

## Top 1/2/3 백테스트

초기 통합 추천 엔진에서는 top 1, top 2, top 3를 비교했다.

| Top N | 거래 수 | 날짜 수 | 승률 | 평균수익률 | 중앙수익률 | 손절 전 고점 실패율 | 후보 부재율 |
|---:|---:|---:|---:|---:|---:|---:|---:|
| 1 | 54 | 54 | 66.67% | +12.97% | +8.65% | 29.63% | 58.14% |
| 2 | 95 | 54 | 72.63% | +16.03% | +8.92% | 25.26% | 58.14% |
| 3 | 126 | 54 | 73.81% | +14.78% | +8.82% | 24.60% | 58.14% |

top 1이 가장 좋을 것이라는 기대와 달리 top 2와 top 3가 더 안정적이었다.

특히 top 2는 승률 72.63%, 중앙수익률 8.92%, 손절 전 고점 실패율 25.26%로 운영 기준을 통과했다.

그래서 초기 운영 기준은 top 2가 됐다.

## Post-H50 복합 랭킹 결과

H50 이후에는 역할 분류를 다시 정리하고, top 1/2/3/5를 비교했다.

| Top N | 거래 수 | 승률 | 평균수익률 | 중앙수익률 | 손절 전 고점 실패율 |
|---:|---:|---:|---:|---:|---:|
| 1 | 55 | 67.3% | +13.6% | +7.5% | 29.1% |
| 2 | 100 | 66.0% | +14.4% | +6.0% | 31.0% |
| 3 | 136 | 69.9% | +13.9% | +7.5% | 27.9% |
| 5 | 182 | 70.3% | +13.1% | +8.0% | 27.5% |

여기서는 top 5가 가장 안정적이었다.

이 결과는 중요한 해석을 만든다.

```text
현재 점수식은 최고의 1개를 완벽히 고르는 모델이라기보다
좋은 후보군 3-5개를 모으는 모델에 가깝다.
```

따라서 H35의 최종 방향은 “무조건 top 1만 보낸다”가 아니다.

내부적으로는 top 5까지 계산하고, 외부 메시지는 그중 강한 1-2개만 보내는 방식이 더 맞다.

## 승률 감각

초기 통합 랭킹:

```text
Top 1 | ███████░░░ 66.7%
Top 2 | ███████░░░ 72.6%
Top 3 | ███████░░░ 73.8%
```

post-H50 복합 랭킹:

```text
Top 1 | ███████░░░ 67.3%
Top 2 | ███████░░░ 66.0%
Top 3 | ███████░░░ 69.9%
Top 5 | ███████░░░ 70.3%
```

두 번의 결과를 같이 보면 top 1 단독 운영은 아직 불안하다.

후보군을 2-5개로 넓히면 안정성은 좋아진다.

다만 사용자 메시지에서는 종목 수를 줄여야 한다.

## 테스트 과정에서 본 시행착오

가장 큰 시행착오는 “좋은 조건은 전부 매수 조건으로 쓰면 된다”는 생각이었다.

실제로는 역할이 다르다.

```text
entry anchor는 후보를 만든다.
timing overlay는 후보의 순서를 올린다.
risk filter는 나쁜 후보를 낮추거나 제외한다.
ranking boost는 시장 환경을 반영한다.
context only는 설명에만 쓴다.
```

이 역할 분리가 없으면 좋은 보조 신호가 후보를 과하게 늘린다.

H35는 그래서 모델 성능만이 아니라 사용자 경험의 문제이기도 하다.

추천 서비스에서 긴 watchlist는 답이 아니다.

## 최종 판단

```text
h35_candidate_ranking_ready_for_final_selection_review
```

현재 판단:

```text
내부 계산은 top 5까지 유지한다.
사용자 메시지는 top 1-2개를 기본으로 한다.
top 1-2가 내부 top 5 대비 약하면 "오늘은 강한 후보 없음"도 허용한다.
context-only 조건은 추천 후보로 올리지 않는다.
entry anchor 없는 종목은 랭킹 점수가 있어도 추천하지 않는다.
```

H35는 H1-H50의 실험을 서비스로 연결하는 다리다.

이제 질문은 “무슨 지표가 좋은가”가 아니다.

```text
오늘 실제로 사용자에게 보낼 1-2개를 어떻게 고를 것인가?
```

그 질문에 답하는 첫 구조가 H35다.

## 원문과 코드

원문 기록:

- `docs/tasks/integrated-recommendation-engine-plan-2026-05-13.md`
- `docs/tasks/post-h50-candidate-ranking-test-2026-05-17.md`
- `docs/tasks/h26-h35-next-hypothesis-pool-2026-05-14.md`

실행 코드:

- `src/supertrader/reports/integrated_ranking_score.py`
- `src/supertrader/reports/post_h50_candidate_ranking_test.py`
- `tests/test_integrated_ranking_score.py`
- `tests/test_post_h50_candidate_ranking_test.py`
