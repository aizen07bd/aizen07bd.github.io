---
title: "H30. 신고가 근처라고 다 추격매수는 아니었다"
date: 2026-05-22 08:40:00 +09:00
categories: [Fintech, ChartAnalysis]
tags: [Finance, Analysis]
math: false
mermaid: false
image:
  path: /assets/img/posts/ChatGPT Image 2026년 5월 15일 오전 02_19_36.png # 대표 이미지 경로
  alt: "차트분석 가설 테스트 이미지"
---


# H30. 신고가 근처라고 다 추격매수는 아니었다

H30은 고점 근처 매물 소화에 대한 가설이다.

이전 테스트들에서 고점 근처 조건은 애매했다.

전고점에 가까운 종목은 강한 종목일 수 있다.

하지만 동시에 이미 많이 올라서 늦게 따라붙는 추격매수일 수도 있다.

그래서 H30에서는 질문을 이렇게 잡았다.

```text
고점 근처에 있는 종목 중에서
건강하게 매물을 소화하는 경우와
늦은 추격매수 위험이 큰 경우를 구분할 수 있을까?
```

## 왜 이 가설을 세웠나

차트 분석에서 전고점 근처는 중요한 위치다.

강한 종목은 전고점 근처에서 쉽게 무너지지 않고 좁은 범위로 쉬다가 다시 올라가는 경우가 있다.

반대로 이미 과열된 종목은 전고점 근처에서 윗꼬리를 만들고, 거래량이 터진 뒤 바로 식으면서 손절로 이어질 수 있다.

H9, H12, H19에서 볼린저 압축, 저변동, 상단 접근을 이미 봤지만, 그 테스트들은 고점 근처에서 매물이 소화되고 있는지까지는 직접 보지 못했다.

그래서 H30은 고점 근처 후보를 다음 관점으로 다시 쪼갰다.

```text
전고점 근접
좁은 변동폭
윗꼬리/거절 캔들 회피
거래량 냉각
종가 위치 안정
MFI 과열 완화
MACD 흐름 유지
H24 과열/실패 회피
```

## 이 가설의 기술 스펙

H30에서 사용한 값은 다음과 같다.

- `close_to_prior_high_20_tier`, `close_to_prior_high_60_tier`: 20일/60일 전고점 근접도
- `high_low_range_20d_tier`: 20일 고저폭 압축 정도
- `intraday_range_pct`: 당일 장중 변동폭
- `upper_wick_pct`: 윗꼬리 비율
- `close_location_value`: 종가가 당일 고가/저가 범위에서 어디에 위치하는지
- `volume_ratio_1d`, `relative_volume_20`: 거래량 과열 여부
- `traded_value`: 거래대금 변화
- `mfi_14`: 자금 흐름 과열/냉각
- `macd_hist_delta_1d`: MACD 히스토그램 변화
- `h24_no_climax_rejection`, `h24_no_oscillator_overheat`: H24에서 만든 과열/실패 회피 조건

새로 만든 파생값도 있다.

- `h30_close_location_3d`: 최근 3일 종가 위치 평균
- `h30_volume_ratio_3d`: 최근 3일 거래량 비율 평균
- `h30_intraday_range_3d`: 최근 3일 장중 변동폭 평균
- `h30_traded_value_change_5d`: 5일 전 대비 거래대금 변화율
- `h30_digestion_score`: 매물 소화 조건을 합친 점수

핵심은 고점 근처라는 사실 하나만 보지 않는 것이다.

고점 근처에 있으면서도 윗꼬리가 크지 않고, 변동폭이 과하지 않고, 종가 위치가 무너지지 않고, MFI와 거래량이 식어 있는지를 같이 봤다.

## 세부 가설 A-E 전체 흐름

| 세부 | 검증 관점 | 표본 | 승률 | 중앙수익률 | 손절 전 고점 실패율 | 판단 |
|---|---|---:|---:|---:|---:|---|
| H30-A | near 60d high + narrow range + no rejection | 151 | 74.2% | +6.3% | 23.8% | 리포트 가능 |
| H30-B | near high + declining volume + stable close | 107 | 71.0% | +4.8% | 24.3% | 탐색 후보 |
| H30-C | near high + MFI cooling + MACD intact | 234 | 71.4% | +6.5% | 25.6% | 탐색 후보 |
| H30-D | near high + H24 chase/failure avoidance | 145 | 71.7% | +6.3% | 23.4% | 리포트 가능 |
| H30-E | H25D + near-high digestion score >=5 | 181 | 74.6% | +7.5% | 23.2% | 리포트 가능 |

이번에는 H30-A, H30-D, H30-E가 기준을 통과했다.

다만 H30-E는 H25D 기준과 동일한 표본으로 잡혔다.

즉, H30-E가 H25D를 새롭게 이겼다기보다는 H25D가 이미 고점 근처 매물 소화형 후보를 상당히 포함하고 있었다고 보는 편이 맞다.

## 결과 요약

| 구분 | 승률 | 평균수익률 | 중앙수익률 | 손절 전 고점 실패율 | 중앙 고점일 | 판단 |
|---|---:|---:|---:|---:|---:|---|
| H30-A 60일 고점 근처 + 좁은 변동 + 거절 없음 | 74.2% | +9.6% | +6.3% | 23.8% | 27일 | 리포트 가능 |
| H30-B 고점 근처 + 거래량 감소 + 종가 안정 | 71.0% | +9.3% | +4.8% | 24.3% | 26일 | 탐색 후보 |
| H30-C 고점 근처 + MFI 냉각 + MACD 유지 | 71.4% | +11.9% | +6.5% | 25.6% | 28일 | 탐색 후보 |
| H30-D 고점 근처 + H24 회피 필터 | 71.7% | +10.2% | +6.3% | 23.4% | 27일 | 리포트 가능 |
| H30-E H25D + 매물 소화 점수 | 74.6% | +13.2% | +7.5% | 23.2% | 30일 | 리포트 가능 |

## 승률 감각

```text
H30-A near 60d high + narrow/no rejection | ███████░░░ 74.2%
H30-B declining volume + stable close      | ███████░░░ 71.0%
H30-C MFI cooling + MACD intact            | ███████░░░ 71.4%
H30-D H24 chase/failure avoidance          | ███████░░░ 71.7%
H30-E H25D + digestion score               | ███████░░░ 74.6%
```

숫자만 보면 H30-E가 가장 안정적이다.

하지만 이 결과는 H25D와 완전히 같았다.

그래서 H30-E를 새로운 독립 모델로 보기보다는, H25D가 왜 좋은 후보를 고르는지 설명하는 해석용 컴포넌트로 보는 것이 맞다.

## H2 promoted와 비교

H2 promoted 기준:

```text
103건
승률 79.6%
중앙수익률 +10.9%
손절 전 고점 실패율 19.4%
```

H30의 어떤 세부 가설도 H2 promoted를 넘지는 못했다.

따라서 H30은 강한 추천 1순위 엔진이 아니다.

## 테스트 과정에서 본 시행착오

처음에는 고점 근처라는 조건이 너무 넓었다.

고점 근처에 있다는 사실만으로는 강한 종목과 위험한 종목이 섞였다.

그래서 H30에서는 고점 근처 조건을 유지하되, 실제로는 다음처럼 위험을 줄이는 방향으로 조합을 바꿨다.

```text
윗꼬리가 큰 캔들 제외
H24 과열/실패 조건 제외
거래량이 계속 과열되는 후보보다 식어가는 후보 확인
MFI가 너무 뜨겁지 않은 상태 확인
MACD 흐름이 무너지지 않은 후보 확인
```

결과적으로 아주 강한 신규 모델은 아니었지만, 고점 근처 후보를 해석하는 데는 쓸 수 있는 결과가 나왔다.

## 최종 판단

```text
h30_accept_near_high_digestion_component
```

채택할 부분:

```text
H30-A = near 60d high + narrow range + no rejection
H30-D = near high + H24 chase/failure avoidance
H30-E = H25D 내부 설명용 near-high digestion context
```

보류:

```text
H30-B = 승률은 70%를 넘지만 중앙수익률이 약함
H30-C = 표본은 충분하지만 stop-before-peak risk가 약간 높음
```

사용 방식:

```text
고점 근처 후보의 매물 소화 상태 설명
H25D 후보의 near-high continuation context
최종 랭킹의 보조 확인 신호
```

H30은 H2 promoted를 대체하지 않는다.

하지만 후보가 고점 근처에 있을 때 "이게 추격매수인지, 매물 소화 후 재상승 준비인지"를 설명하는 데는 쓸 수 있다.

이 설명은 실제 사용자 메시지에서도 의미가 있다.

예를 들면 이런 식이다.

```text
전고점 근처지만 과열 윗꼬리는 약하고,
최근 변동폭과 거래량이 식으면서 종가 위치가 유지되는 매물 소화형 후보
```

## 원문과 코드

원문 기록:

- `docs/tasks/h30-near-high-supply-digestion-2026-05-15.md`
- `docs/tasks/h26-h35-next-hypothesis-pool-2026-05-14.md`

실행 코드:

- `hypothesis-test-sources/reports/h30_near_high_supply_digestion.py`
- `hypothesis-test-sources/tests/test_h30_near_high_supply_digestion.py`
