# Skills Engine — the player's collection of skill cards

Skills Engine keeps the skills the player has collected and shows them in the **Skills** panel as cards. A skill can open a possibility in another mechanic (a passive "gate"), hand the player a new action card for battles, and add numbers to the **Collection effect** — the total of all collected skills.

It is part of Cthangover, where the player starts with one skill, **Slave Trader**.

## The Skills panel

Open it with the skills icon on the toolbar.

![The Skills panel in Cthangover: the Slave Trader card (Epic, Passive, +10% recruitment chance) in the grid, its description in the Card study on the right, and the Collection effect plate in the corner showing "+10% recruitment chance".](assets/skills-panel.jpg)

- **Tabs** filter by type — **Passive**, **Active**, **For character** — and the rarity row by rarity, from **Common** to **Legendary**; the search field and the sort list narrow the grid.
- **The frame colour and the diamonds** show a card's rarity.
- **Click a card** to see it in the **Card study**: picture, rarity and type badges, description, its effects and, for a skill that grants one, the action card it gives.
- **Collection effect** adds up the effects of all collected skills: two skills of +10% and +5% recruitment chance show +15%.

The Slave Trader card has no picture yet: the game names `characters/skills/slave_master` as its image, but no mod ships that file, so the frame stays empty.

## A skill definition

Skills are JSON files of kind `skill`, anywhere in a mod. This is Slave Trader from Cthangover — compare it with the panel above:

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

- `Name`, `Description` — translation keys.
- `Image` — the card picture.
- `Type` — `Active` (0), `Passive` (1) or `ForCharacter` (2); names or numbers both work. Slave Trader is **Passive**.
- `Rare` — `Common` (0), `Uncommon`, `Rare`, `Epic` (3), `Legendary`. Slave Trader is **Epic**.
- `Effects` — numbers for the Collection effect: `Key` is the translation key of what is improved, `Value` the amount, `Percent` whether it is shown with %. One key must keep the same unit in every skill, because the values are added up.
- `GrantsAction` (optional) — the id of an action card (see Characters Engine). Getting the skill also puts this card into the action pool of the **Character actions** panel.

## What a skill does in the game

The panel only shows a skill. What it changes is decided by the mods that check for it:

- Recruiting offers to **enslave** a defeated enemy only while the player has `slave_master`. The chance of success is the enemy's own `RecruitmentChance`; the +10% on the card is shown in the panel, but recruiting does not add it.
- A skill with `GrantsAction` gives its action card, which the player then puts into a character's slot.

## Give a skill from a scenario

```scenario
action character.add_skill skill_id=slave_master
```

Giving a skill twice does nothing the second time, so a repeated scene cannot duplicate it or its action card. Cthangover gives Slave Trader when a new game starts.

## If something goes wrong

1. **The skill does not appear.** The `skill_id` must match the skill's `Id`; an unknown id is written to the log as a warning.
2. **The card has no picture.** No mod ships an image under the name in `Image`.
3. **The Collection effect shows a strange total.** Two skills use the same effect `Key` with different `Percent` values.
