---
title: "H29. 변동성 수축 후 확장 타이밍은 보조 신호로 쓸 수 있었다"
date: 2026-05-22 08:30:00 +09:00
categories: [Fintech, ChartAnalysis]
tags: [Finance, Analysis]
math: false
mermaid: false
image:
  path: /assets/img/posts/ChatGPT Image 2026년 5월 15일 오전 02_19_36.png # 대표 이미지 경로
  alt: "차트분석 가설 테스트 이미지"
---


# H29. 변동성 수축 후 확장 타이밍은 보조 신호로 쓸 수 있었다

H29는 변동성 수축과 확장에 대한 가설이다.

H9와 H12에서 이미 압축 조건을 봤다.

하지만 그때 얻은 결론은 단순했다.

```text
압축만으로는 부족하다.
```

차트가 조용해졌다는 사실만으로는 매수 타이밍이 되지 않는다.

조용해진 뒤 어느 방향으로 움직이기 시작하는지가 더 중요하다.

그래서 H29에서는 질문을 바꿨다.

```text
압축 상태에서 방향성 확장으로 넘어가는 순간을 잡으면
H2/H25 후보의 품질이 좋아지는가?
```

## 왜 이 가설을 세웠나

주식 차트에서 변동성 압축은 자주 언급된다.

볼린저 밴드가 좁아지고, ATR이 낮아지고, 박스권 폭이 줄어들면 곧 큰 움직임이 나올 수 있다는 생각이다.

하지만 압축은 양방향이다.

위로 터질 수도 있고, 아래로 무너질 수도 있다.

그래서 이번에는 압축 자체가 아니라 다음 조건을 같이 봤다.

```text
MACD 전환
장중 변동폭 확장
종가 위치 개선
거래대금 지속성
H25D 내부 압축-확장 점수
```

## 이 가설의 기술 스펙

H29에서 사용한 주요 값은 다음과 같다.

- `bollinger_bandwidth_20_2`: 볼린저 밴드 폭
- `atr_14`, `natr_14`: ATR/NATR 변동성
- `donchian_width_20`: 돈치안 채널 폭
- `macd_hist_delta_1d`: MACD 히스토그램 하루 변화
- `close_location_value`: 당일 종가가 고가/저가 범위 안에서 어디에 있는지
- `h29_range_expansion_ratio_5d`: 당일 장중 변동폭 / 직전 5일 평균 변동폭
- `h29_bollinger_bandwidth_change_5d`: 볼린저 밴드 폭의 5일 변화율
- `h29_donchian_width_change_5d`: 돈치안 폭의 5일 변화율
- `h28_rank_persistent_3d`, `h28_rank_persistent_5d`: H28에서 만든 거래대금 지속성

핵심은 압축과 확장을 분리해서 보는 것이다.

압축은 준비 상태이고, 확장은 타이밍 신호다.

## 세부 가설 A-E 전체 흐름

| 세부 | 검증 관점 | 표본 | 승률 | 중앙수익률 | 손절 전 고점 실패율 | 판단/메모 |
|---|---|---:|---:|---:|---:|---|
| H29-A | Bollinger compression + MACD turn | 210 | 73.3% | +7.5% | 24.8% | 리포트 가능 |
| H29-B | ATR/NATR compression + range expansion | 132 | 75.0% | +6.4% | 23.5% | 리포트 가능 |
| H29-C | Donchian compression + close-location improvement | 225 | 72.0% | +7.3% | 25.3% | 탐색 후보 |
| H29-D | compression + traded-value persistence | 59 | 66.1% | +12.1% | 32.2% | 수익폭은 크지만 실패 |
| H29-E | H25D + compression expansion score >=2 | 179 | 74.9% | +7.5% | 22.9% | 리포트 가능 |

이번에는 H29-A, H29-B, H29-E가 기준을 통과했다.

다만 통과했다고 해서 핵심 모델을 교체할 정도는 아니다.

## 결과 요약

| 구분 | 승률 | 평균수익률 | 중앙수익률 | 손절 전 고점 실패율 | 판단 |
|---|---:|---:|---:|---:|---|
| H29-A Bollinger + MACD | 73.3% | +12.8% | +7.5% | 24.8% | 리포트 가능 |
| H29-B ATR/NATR + range expansion | 75.0% | +12.4% | +6.4% | 23.5% | 리포트 가능 |
| H29-C Donchian + close-location | 72.0% | +12.4% | +7.3% | 25.3% | 탐색 후보 |
| H29-D compression + traded value | 66.1% | +15.1% | +12.1% | 32.2% | 실패 |
| H29-E H25D + expansion score | 74.9% | +13.3% | +7.5% | 22.9% | 리포트 가능 |

## 승률 감각

```text
H29-A Bollinger + MACD       | ███████░░░ 73.3%
H29-B ATR/NATR + range       | ████████░░ 75.0%
H29-C Donchian + close loc   | ███████░░░ 72.0%
H29-D + traded value         | ███████░░░ 66.1%
H29-E H25D + expansion score | ███████░░░ 74.9%
```

승률 기준으로는 H29-B가 가장 좋았다.

하지만 중앙수익률은 H29-D가 가장 높았다.

문제는 H29-D의 손절 전 고점 실패율이 32.2%였다는 점이다.

H28에서 봤던 문제가 그대로 반복됐다.

거래대금 지속성은 수익폭을 키우지만, 위험도 같이 키운다.

## H25D와 비교

H25D 기준:

```text
181건
승률 74.6%
중앙수익률 +7.5%
손절 전 고점 실패율 23.2%
```

H29-B:

```text
132건
승률 75.0%
중앙수익률 +6.4%
손절 전 고점 실패율 23.5%
```

H29-E:

```text
179건
승률 74.9%
중앙수익률 +7.5%
손절 전 고점 실패율 22.9%
```

H29-E는 H25D와 거의 비슷하지만, 손절 전 고점 실패율이 조금 낮았다.

즉, H29는 H25D를 대체한다기보다 H25D 후보의 상태를 설명하는 보조 신호에 가깝다.

## H2 promoted와 비교

H2 promoted 기준:

```text
103건
승률 79.6%
중앙수익률 +10.9%
손절 전 고점 실패율 19.4%
```

H29의 어떤 branch도 H2 promoted를 이기지는 못했다.

따라서 H29를 강한 추천 1순위로 쓰면 안 된다.

## 결과를 보며 느낀 점

H29는 의미가 있다.

압축만 보는 것보다, 압축 이후 방향성 확장까지 같이 보는 것이 낫다는 결과가 나왔다.

특히 H29-A와 H29-B는 각각 MACD 전환과 장중 변동폭 확장이라는 다른 방식으로 타이밍을 잡았는데 둘 다 기준을 통과했다.

하지만 성과는 보조 신호 수준이다.

H2 promoted처럼 강하게 매수 타이밍을 찍어주는 엔진은 아니고, H25D 후보를 해석하거나 랭킹에서 약한 가산점을 줄 때 쓸 수 있는 정도다.

## 최종 판단

```text
h29_accept_volatility_expansion_component
```

채택할 부분:

```text
H29-A = Bollinger compression + MACD turn
H29-B = ATR/NATR compression + range expansion
H29-E = H25D + compression expansion score >=2
```

보류 또는 폐기:

```text
H29-C: 탐색 후보
H29-D: 수익폭은 크지만 손절 전 고점 실패율이 너무 높아 폐기
```

사용 방식:

```text
보조 타이밍 컴포넌트
pre-breakout context
최종 랭킹의 약한 confirmation signal
```

H29는 H2/H25를 대체하지 않는다.

하지만 H25D 후보가 나왔을 때 "압축에서 확장으로 넘어가는 중인가"를 설명하는 데는 쓸 수 있다.

## 원문과 코드

원문 기록:

- `docs/tasks/h29-volatility-contraction-expansion-timing-2026-05-15.md`
- `docs/tasks/h26-h35-next-hypothesis-pool-2026-05-14.md`

실행 코드:

- `hypothesis-test-sources/reports/h29_volatility_contraction_expansion_timing.py`
- `hypothesis-test-sources/tests/test_h29_volatility_contraction_expansion_timing.py`
