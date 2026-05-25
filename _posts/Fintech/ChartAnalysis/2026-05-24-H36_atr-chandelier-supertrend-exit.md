---
title: "H36. ATR 기반 청산도 단순하게 쓰면 수익을 지키지 못했다"
date: 2026-05-24 08:00:00 +09:00
categories: [Fintech, ChartAnalysis]
tags: [Finance, Analysis]
math: false
mermaid: false
image:
  path: /assets/img/posts/ChatGPT Image 2026년 5월 15일 오전 02_19_26.png # 대표 이미지 경로
  alt: "차트분석 가설 테스트 이미지"
---


# H36. ATR 기반 청산도 단순하게 쓰면 수익을 지키지 못했다

H36은 ATR 기반 청산 규칙에 대한 가설이다.

H33에서 우리는 단순 매도 신호를 테스트했다.

MACD 약화, 과열 후 약한 종가, H24형 거절 캔들 같은 규칙이었다.

결과는 좋지 않았다.

그래서 이번에는 다른 방향을 봤다.

```text
고정된 매도 신호가 아니라
종목별 변동성에 맞춰 움직이는 ATR 기반 청산은 더 나을까?
```

## 왜 이 가설을 세웠나

ATR은 종목의 평균 변동폭을 반영한다.

변동성이 큰 종목에는 넓은 손절/추적청산선을 주고, 변동성이 작은 종목에는 더 좁은 청산선을 주는 식이다.

이론적으로는 고정 손절보다 유연하다.

그래서 H36에서는 다음 청산 규칙을 테스트했다.

```text
Chandelier Exit
SuperTrend
Keltner midline failure
ATR ratchet trailing
entry family별 hybrid ATR exit
```

## 이 가설의 기술 스펙

사용한 주요 값은 다음과 같다.

- `atr_14`: 14일 ATR
- `natr_14`: 정규화 ATR
- `supertrend_10_3`
- `supertrend_direction_10_3`
- `keltner_mid_20`
- `keltner_upper_20_2`
- `keltner_lower_20_2`

진입군은 H33과 같은 계열을 썼다.

- H2 promoted
- H25D
- H7C
- H12E
- H16E

## 세부 가설 A-E 전체 흐름

| 세부 | 검증 관점 | 표본 | 승률 | 중앙수익률 | 손절률 | 중앙 청산일 | 판단 |
|---|---|---:|---:|---:|---:|---:|---|
| H36-A | Chandelier Exit 3ATR | 746 | 39.0% | -2.0% | 36.3% | 21일 | 실패 |
| H36-B | SuperTrend flip or line break | 746 | 39.9% | -5.4% | 53.4% | 30일 | 실패 |
| H36-C | Keltner midline failure after profit | 746 | 43.7% | -5.4% | 51.1% | 27일 | 실패 |
| H36-D | ATR ratchet trailing after profit | 746 | 40.8% | -1.4% | 39.5% | 20일 | 실패 |
| H36-E | hybrid ATR exit by entry family | 2,331 | 42.2% | -2.0% | 42.9% | 22일 | 실패 |

## 기준 보유와 비교

20일 보유 + -5% 손절:

```text
승률 48.7%
중앙수익률 -0.2%
손절률 28.0%
```

60일 보유 + -5% 손절:

```text
승률 36.2%
중앙수익률 -5.4%
손절률 55.8%
```

H36은 60일 보유보다는 일부 위험을 줄였지만, 20일 보유보다 나빴다.

실전 기준으로는 20일 보유보다 좋아야 의미가 있다.

## 승률 감각

```text
H36-A Chandelier Exit       | ████░░░░░░ 39.0%
H36-B SuperTrend            | ████░░░░░░ 39.9%
H36-C Keltner midline       | ████░░░░░░ 43.7%
H36-D ATR ratchet           | ████░░░░░░ 40.8%
H36-E hybrid ATR            | ████░░░░░░ 42.2%
```

결과는 분명히 약하다.

ATR 기반이라고 해서 자동으로 좋은 청산이 되는 것은 아니었다.

## 테스트 과정에서 본 시행착오

H36은 H33과 같은 방식으로 실제 경로를 따라갔다.

```text
1. 매수 후 다음 날부터 60거래일 경로 확인
2. -5% 손절이 먼저 오면 손절
3. ATR 기반 청산 신호가 오면 그날 종가 청산
4. 아무 신호가 없으면 60일 종가 청산
```

이 방식은 현실적이다.

그만큼 결과도 냉정하게 나왔다.

Chandelier와 ATR ratchet은 매도 신호가 자주 나왔지만, 손절을 충분히 줄이지 못했다.

SuperTrend와 Keltner는 신호가 너무 늦거나 약해서 중앙수익률이 무너졌다.

## 최종 판단

```text
h36_reject_current_design
```

폐기:

```text
H36-A
H36-B
H36-C
H36-D
H36-E
```

실무적 결론:

```text
ATR 기반 청산 지표를 단순 규칙으로 쓰지 않는다.
ATR, SuperTrend, Keltner 값은 H34의 경로 기반 최적화 재료로만 남긴다.
```

H36은 H33의 결론을 더 강하게 만든다.

매도는 단일 지표로 해결되지 않는다.

다음 단계는 H34다.

H34에서는 진입군별 고점일 분포, 수익 발생 속도, 손절 전 고점 도달 가능성을 기준으로 경로 기반 청산을 다시 찾아야 한다.

## 원문과 코드

원문 기록:

- `docs/tasks/h36-atr-chandelier-supertrend-exit-2026-05-15.md`
- `docs/tasks/h36-h41-extended-hypothesis-pool-2026-05-15.md`

실행 코드:

- `hypothesis-test-sources/reports/h36_atr_chandelier_supertrend_exit.py`
- `hypothesis-test-sources/tests/test_h36_atr_chandelier_supertrend_exit.py`
