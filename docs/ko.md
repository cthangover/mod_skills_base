# Skills Engine — 플레이어의 스킬 카드 컬렉션

Skills Engine은 플레이어가 모은 스킬을 보관하고 **스킬** 패널에 카드로 보여 줍니다. 스킬 하나는 다른 메커니즘의 가능성을 열어 주는(패시브 "관문") 존재일 수도 있고, 전투용 새 액션 카드를 건네 줄 수도 있으며, 모든 스킬의 합계인 **컬렉션 효과**에 수치를 더할 수도 있습니다.

Cthangover의 일부로, 플레이어는 스킬 하나 **노예 상인**으로 시작합니다.

## 스킬 패널

툴바의 스킬 아이콘으로 엽니다.

![Cthangover의 스킬 패널: 격자 속 노예 상인 카드(에픽, 패시브, 영입 확률 +10%), 오른쪽 카드 연구의 설명, 구석의 컬렉션 효과 판 "영입 확률 +10%".](assets/skills-panel.jpg)

- **탭**은 종류로 걸러 줍니다(**패시브**, **액티브**, **캐릭터용**), 희귀도 줄은 희귀도로 걸러 줍니다. **커먼**부터 **레전더리**까지. 검색창과 정렬 목록이 격자를 좁혀 줍니다.
- **테두리 색과 다이아몬드 무늬**가 카드의 희귀도를 보여 줍니다.
- **카드를 클릭**하면 카드 연구에서 자세히 볼 수 있습니다. 그림, 희귀도와 종류 배지, 설명, 효과, 그리고 부여 스킬이라면 주는 액션 카드까지요.
- **컬렉션 효과**는 모은 모든 스킬의 효과를 합산합니다. 영입 확률 +10%와 +5% 스킬 두 개는 +15%로 표시됩니다.

노예 상인 카드에는 아직 그림이 없습니다. 게임은 `characters/skills/slave_master`를 이미지로 지정하지만, 그 파일을 함께 제공하는 mod가 없어 테두리가 비어 있습니다.

## 스킬 정의

스킬은 kind `skill` JSON 파일로, mod 안 어디에나 둘 수 있습니다. Cthangover의 노예 상인입니다. 위 패널과 비교해 보세요.

```json
{
  "Kind": "skill",
  "Items": [
    {
      "Id": "slave_master",
      "Name": "characters/skills/slave_master_name",
      "Description": "characters/skills/slave_master",
      "Image": "characters/skills/slave_master",
      "Type": 1,
      "Rare": 3,
      "Effects": [
        { "Key": "characters/skills/effects/recruit", "Value": 10, "Percent": true }
      ]
    }
  ]
}
```

- `Name`, `Description` — 번역 키.
- `Image` — 카드 그림.
- `Type` — `Active`(0), `Passive`(1), `ForCharacter`(2). 이름이든 숫자든 됩니다. 노예 상인은 **패시브**.
- `Rare` — `Common`(0), `Uncommon`, `Rare`, `Epic`(3), `Legendary`. 노예 상인은 **에픽**.
- `Effects` — 컬렉션 효과에 더해지는 수치. `Key`는 무엇이 강화되는지의 번역 키, `Value`는 양, `Percent`는 %로 표시할지 여부입니다. 값은 합산되므로 같은 키는 모든 스킬에서 같은 단위를 유지해야 합니다.
- `GrantsAction`(선택) — 액션 카드의 id(Characters Engine 참조). 스킬을 얻으면 이 카드도 캐릭터 액션 패널의 액션 풀에 들어갑니다.

## 스킬이 게임에서 하는 일

패널은 스킬을 보여 줄 뿐입니다. 무엇을 바꾸는지는 그것을 확인하는 mod들이 결정합니다.

- recruiting은 플레이어가 `slave_master`를 가지고 있는 동안에만 쓰러뜨린 적을 **노예로 삼자**고 제안합니다. 성공 확률은 적 자신의 `RecruitmentChance`이며, 카드의 +10%는 패널에 보일 뿐 recruiting이 더하지 않습니다.
- `GrantsAction`을 가진 스킬은 그 액션 카드를 건네 주고, 플레이어는 나중에 그것을 캐릭터의 슬롯에 놓습니다.

## 시나리오에서 스킬 주기

```scenario
action character.add_skill skill_id=slave_master
```

스킬을 두 번 줘도 두 번째에는 아무 일도 일어나지 않습니다. 그래서 반복되는 장면이 스킬이나 그 액션 카드를 복제하지 못합니다. Cthangover는 새 게임을 시작할 때 노예 상인을 줍니다.

## 문제가 생겼을 때

1. **스킬이 나타나지 않습니다.** `skill_id`는 스킬의 `Id`와 일치해야 합니다. 알 수 없는 id는 경고로 로그에 기록됩니다.
2. **카드에 그림이 없습니다.** `Image`에 적힌 이름의 그림을 제공하는 mod가 없습니다.
3. **컬렉션 효과의 합계가 이상합니다.** 두 스킬이 같은 효과 `Key`를 서로 다른 `Percent`로 사용하고 있습니다.
