---
title: "1인 게임 개발기 : 균열 방어선 3편 게임 핵심 로직"
date: 2026-05-30 00:50:00 +09:00
categories: [Game, LineDefense]
tags: [Game, 1인게임개발]
math: false
mermaid: false
image:
  path: /assets/img/posts/linedefense-start.png # 대표 이미지 경로
  alt: "균열 방어선 디펜스 게임"
---

# 균열 방어선 개발기 3편: Phaser로 만든 자동 디펜스 핵심 로직

> 이 글은 무료 웹 디펜스 게임 **균열 방어선** 개발기 세 번째 글입니다.  
> 이번 편에서는 게임의 주요 로직을 코드 중심으로 정리합니다.

## 시작하며

균열 방어선은 Phaser 기반의 2D 웹게임입니다. 별도의 서버 로직 없이 브라우저에서 실행되는 정적 게임이고, 대부분의 게임 진행은 하나의 메인 씬 안에서 처리됩니다.

게임 구조를 크게 나누면 다음과 같습니다.

- 맵과 캐릭터 데이터 정의
- 시작 캐릭터에 따른 진행 루트 생성
- 스테이지 시작과 전투 루프
- 몬스터 생성과 이동
- 아군 자동 공격
- 스킬 쿨타임과 스킬 발동
- 보상 카드 선택
- 클리어, 게임 오버, 엔딩 처리

이번 글에서는 전체 코드를 모두 설명하기보다, 게임 흐름을 이해하는 데 필요한 핵심 부분만 골라서 소개하겠습니다.

## 맵 데이터

먼저 맵은 배열 데이터로 관리합니다. 각 맵은 화면 배경, 적 스프라이트 시트, 표시 이름을 갖습니다.

```js
const mapSequence = [
  {
    slug: "forest",
    title: "숲",
    mapKey: "mapForest",
    mapPath: "assets/map-bg.png",
    enemyKey: "enemiesForest",
    enemyPath: "assets/enemies-forest-sheet.png",
  },
  {
    slug: "desert",
    title: "사막",
    mapKey: "mapDesert",
    mapPath: "assets/map-desert.png",
    enemyKey: "enemiesDesert",
    enemyPath: "assets/enemies-desert-sheet.png",
  },
];
```

실제 코드에는 10개 맵이 모두 들어 있습니다. 이 방식의 장점은 맵을 추가할 때 게임 로직을 크게 바꾸지 않아도 된다는 점입니다. 배경 이미지와 적 시트, 표시 이름만 맞춰주면 같은 전투 구조 안에서 새로운 지역을 만들 수 있습니다.

## 캐릭터 데이터

캐릭터도 비슷하게 데이터 중심으로 정의했습니다.

```js
const characterDefinitions = [
  {
    id: "mage",
    name: "마법사",
    faction: "fantasy",
    homeMap: "forest",
    fireDelay: 430,
    damageRate: 1,
  },
  {
    id: "schoolgirl",
    name: "여학생",
    faction: "modern",
    homeMap: "schoolyard",
    fireDelay: 420,
    damageRate: 0.75,
  },
];
```

캐릭터에는 소속 세계, 시작 맵, 공격 속도, 피해 배율 같은 정보가 들어 있습니다. 실제 코드에는 스프라이트 키, 애니메이션 키, 위치, 크기, 투사체 색상도 포함되어 있습니다.

이런 데이터 기반 구조는 캐릭터를 늘릴 때 유리합니다. 새 캐릭터를 추가하려면 캐릭터 정의, VFX 정의, 스킬 정의, 사운드 정도를 함께 추가하면 됩니다.

## 시작 캐릭터에 따른 루트 생성

균열 방어선은 선택한 시작 캐릭터에 따라 진행 루트가 달라집니다. 판타지 캐릭터를 고르면 판타지 세계부터, 현대 캐릭터를 고르면 현대 세계부터 진행합니다.

```js
buildRoute(starterId) {
  const starter = this.getCharacterDefinition(starterId);
  const mapBySlug = Object.fromEntries(mapSequence.map((map) => [map.slug, map]));

  if (starter.faction === "modern") {
    return [...modernMapOrder, ...fantasyMapOrder].map((slug) => mapBySlug[slug]);
  }

  const fantasyRoute = [
    starter.homeMap,
    ...fantasyMapOrder.filter((slug) => slug !== starter.homeMap),
  ];

  return [...fantasyRoute, ...fantasyToModernMapOrder].map((slug) => mapBySlug[slug]);
}
```

여기서 중요한 점은 루트를 고정 배열 하나로 두지 않았다는 것입니다. 시작 캐릭터의 소속과 고향 맵을 기준으로 그때그때 진행 순서를 만듭니다.

예를 들어 마법사를 선택하면 숲에서 시작하고, 사막 캐릭터를 선택하면 사막에서 시작합니다. 여학생이나 남학생을 선택하면 현대 맵인 학교부터 시작합니다.

## 스테이지 정보 계산

각 맵은 5개의 스테이지로 구성됩니다. 현재 스테이지 번호를 기준으로 몇 번째 맵인지, 그 맵 안에서 몇 번째 단계인지 계산합니다.

```js
getStageInfo() {
  const route = this.activeMapSequence ?? mapSequence;
  const maxStage = route.length * STAGES_PER_MAP;
  const stageIndex = Math.min(Math.max(this.stage - 1, 0), maxStage - 1);
  const mapIndex = Math.floor(stageIndex / STAGES_PER_MAP);
  const enemyTier = stageIndex % STAGES_PER_MAP;

  return {
    maxStage,
    mapIndex,
    enemyTier,
    map: route[mapIndex],
  };
}
```

`STAGES_PER_MAP` 값은 5입니다. 그래서 1~5스테이지는 첫 번째 맵, 6~10스테이지는 두 번째 맵이 됩니다. `enemyTier`는 해당 맵 안에서 적 단계가 얼마나 올라갔는지를 나타냅니다.

이 계산 덕분에 맵 수가 늘어나도 전체 스테이지 구조를 일관되게 유지할 수 있습니다.

## 전투 루프

Phaser의 `update` 함수는 매 프레임 호출됩니다. 균열 방어선에서는 현재 상태가 전투 중일 때만 전투 로직을 갱신합니다.

```js
update(time, delta) {
  if (this.state !== "playing") {
    return;
  }

  const dt = delta / 1000;
  this.stageClock -= dt;
  this.spawnTimer -= delta;

  if (this.spawned < this.totalMonsters && this.spawnTimer <= 0) {
    this.spawnMonsterWave();
    this.spawnTimer = this.spawnDelay;
  }

  this.updateMonsters(dt, delta);
  this.updateBullets(dt);
  this.updateCombatants(delta);

  if (this.barricadeHp <= 0) {
    this.gameOver("바리케이트 파괴");
  } else if (this.stageClock <= 0 && this.monsters.countActive(true) > 0) {
    this.gameOver("시간 초과");
  } else if (this.spawned >= this.totalMonsters && this.monsters.countActive(true) === 0) {
    this.stageClear();
  }

  this.updateHud();
}
```

전투 루프는 크게 세 가지를 계속 갱신합니다.

1. 몬스터
2. 탄환과 스킬 효과
3. 아군 캐릭터

그 다음 바리케이드 체력, 제한 시간, 몬스터 잔여 여부를 확인해서 게임 오버 또는 스테이지 클리어를 판단합니다.

## 몬스터 생성

스테이지가 시작되면 총 등장 몬스터 수와 생성 간격이 정해집니다.

```js
this.totalMonsters = 42 + this.currentStageInfo.enemyTier * 6 + this.currentStageInfo.mapIndex * 2;
this.spawnDelay = Math.max(360, 560 - this.currentStageInfo.enemyTier * 28);
```

맵이 뒤로 갈수록, 그리고 같은 맵 안에서 적 단계가 올라갈수록 압박이 커집니다. 등장 수가 늘어나고, 생성 간격은 짧아집니다.

몬스터 웨이브 생성은 현재 활성 몬스터 수와 남은 몬스터 수를 고려해서 처리합니다.

```js
spawnMonsterWave() {
  const remaining = this.totalMonsters - this.spawned;
  const info = this.currentStageInfo ?? this.getStageInfo();
  const activePressure = this.monsters.countActive(true);
  const baseCount = 2 + Math.floor(info.enemyTier / 2);
  const pressureBonus = activePressure < 6 ? 1 : 0;
  const count = Math.min(remaining, baseCount + pressureBonus);

  for (let index = 0; index < count; index += 1) {
    const isBoss = this.spawned === this.totalMonsters - 1;
    this.spawnMonster(null, null, isBoss);
  }
}
```

마지막으로 생성되는 몬스터는 보스 처리됩니다. 맵의 마지막 단계에서는 별도 보스 이미지를 사용합니다.

## 몬스터 이동과 바리케이드 공격

몬스터는 기본적으로 아래쪽 바리케이드를 향해 이동합니다. 바리케이드에 도달하면 더 이상 내려오지 않고, 일정 주기로 바리케이드에 피해를 줍니다.

```js
updateMonsters(dt, delta) {
  this.monsters.getChildren().forEach((monster) => {
    if (monster.y < BARRICADE_Y - 36 && !monster.meta.reachedBarricade) {
      const slowRate = monster.meta.slowTimer > 0 ? 0.48 : 1;
      monster.y += monster.meta.speed * slowRate * dt;
      this.moveMonsterParts(monster);
      return;
    }

    monster.meta.reachedBarricade = true;
    monster.meta.attackTimer -= delta;

    if (monster.meta.attackTimer <= 0) {
      this.damageBarricade(monster.meta.barricadeDamage);
      monster.meta.attackTimer = 760;
    }
  });
}
```

둔화 효과가 걸려 있으면 이동 속도를 낮춥니다. 이 구조 때문에 둔화탄, 덩굴속박, 빙결 같은 효과가 전투에서 의미를 갖습니다.

## 아군 자동 공격

아군 캐릭터는 각자 공격 타이머를 갖고 있습니다. 타이머가 0 이하가 되면 가장 가까운 몬스터를 찾아 공격합니다.

```js
updateCombatants(delta) {
  [this.defender, ...this.allies].filter(Boolean).forEach((combatant) => {
    if (!combatant.visible) {
      return;
    }

    combatant.meta.fireTimer -= delta;

    if (combatant.meta.fireTimer <= 0) {
      this.fireAtNearest(combatant, combatant.meta.animKey);
      combatant.meta.fireTimer = combatant.meta.fireDelay;
    }

    this.updateCombatantSkills(combatant, delta);
    this.updateSkillIndicators(combatant);
  });
}
```

자동 전투 구조이기 때문에 플레이어가 직접 공격 버튼을 누르지 않습니다. 대신 공격 대상 선정, 투사체 이동, 명중 판정, 피해 계산이 매 프레임 처리됩니다.

## 스킬 쿨타임과 발동

각 캐릭터는 2개의 스킬을 가질 수 있습니다. 스킬은 처음부터 모두 열려 있지 않고, 보상 카드로 해금해야 합니다.

```js
updateCombatantSkills(combatant, delta) {
  [1, 2].forEach((skillNumber) => {
    if (!this.isSkillUnlocked(combatant.meta.id, skillNumber)) {
      return;
    }

    combatant.meta.skillTimers[skillNumber] -= delta;

    if (combatant.meta.skillTimers[skillNumber] > 0) {
      return;
    }

    const casted = this.castSkill(combatant, skillNumber);
    const config = skillConfigs[combatant.meta.id]?.[skillNumber];
    combatant.meta.skillTimers[skillNumber] = casted ? config.cooldown : 400;
  });
}
```

스킬은 수동 버튼이 아니라 자동 발동입니다. 해금된 스킬은 쿨타임이 끝나고 공격 가능한 대상이 있으면 자동으로 사용됩니다.

이 방식은 모바일 화면에서 버튼을 줄이고, 전투 화면을 더 깔끔하게 유지하기 위한 선택이었습니다.

## 보상 카드

보상 카드는 기본 성장 카드와 스킬 해금 카드가 함께 섞입니다.

```js
const cardPool = [
  {
    title: "화력 강화",
    desc: "총알 피해 +8",
    apply: (scene) => {
      scene.stats.damage += 8;
    },
  },
  {
    title: "빠른 장전",
    desc: "공격 속도 +18%",
    apply: (scene) => {
      scene.stats.fireDelay = Math.max(120, Math.floor(scene.stats.fireDelay * 0.82));
    },
  },
];
```

기본 카드는 전역 전투 능력을 강화합니다. 피해 증가, 공격 속도 증가, 관통, 둔화, 폭발, 바리케이드 회복 같은 효과가 있습니다.

스킬 해금 카드는 현재 합류한 캐릭터를 기준으로 동적으로 추가됩니다.

```js
getRewardCards() {
  const cards = cardPool.slice();

  [this.defender, ...this.allies]
    .filter((combatant) => combatant?.visible)
    .forEach((combatant) => {
      [1, 2].forEach((skillNumber) => {
        if (this.isSkillUnlocked(combatant.meta.id, skillNumber)) {
          return;
        }

        cards.push({
          title: `${combatant.meta.name} ${skillNumber}`,
          desc: "스킬 해금",
          apply: (scene) => {
            scene.unlockSkill(combatant.meta.id, skillNumber);
          },
        });
      });
    });

  return cards;
}
```

이 구조 덕분에 아직 등장하지 않은 캐릭터의 스킬 카드는 나오지 않습니다. 합류한 캐릭터가 늘어날수록 선택 가능한 성장 방향도 늘어납니다.

## 정리

균열 방어선의 핵심 로직은 복잡한 AI보다 데이터 기반 구성과 단순한 전투 루프에 가깝습니다.

맵, 캐릭터, 스킬, 보상 카드를 데이터로 정의하고, Phaser의 업데이트 루프 안에서 몬스터 이동, 탄환 처리, 캐릭터 공격, 승패 조건을 계속 갱신합니다.

이 구조는 프로토타입을 빠르게 확장하기 좋았습니다. 맵을 추가하려면 맵 데이터를 추가하고, 캐릭터를 추가하려면 캐릭터 정의와 스킬 정의를 추가하면 됩니다. 반대로 아직 개선할 점도 있습니다. 코드가 하나의 파일에 많이 모여 있기 때문에, 다음 버전에서는 데이터와 씬 로직을 분리하는 작업이 필요합니다.

다음 글에서는 코드가 아니라 **디자인 설계** 관점에서 균열 방어선을 살펴보겠습니다. 왜 모바일 세로 화면을 선택했는지, 왜 1차선 구조로 만들었는지, 자동 전투와 카드 선택이 어떤 플레이 경험을 만들도록 설계되었는지 정리해보겠습니다.



