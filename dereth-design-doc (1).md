# Dereth Survivors — Game Design Document

> AC's character depth × Vampire Survivors' visual spectacle × Extraction shooter risk/reward

---

## 1. CORE LOOP

```
CREATE CHARACTER → HUB (Lifestone) → DEPLOY (Run) → FIGHT WAVES → PORTAL OUT or DIE
                      ↑                                                    |
                      └──── XP + Tokens kept, Gear extracted or lost ──────┘
```

**Per Run:** Waves spawn in full. Clear the board → 8 sec window → next wave. Press P to portal home (cast time based on Item Magic skill, interrupted on hit). Survive all 10 waves = auto-extract with no rarity cap.

**Between Runs:** Manage Vault, equip loadout, spend XP on attributes, spend Tokens on gear.

**Risk:** Equipped gear + backpack lost on death. Only XP and Tokens persist always.

---

## 2. HERITAGES

| Heritage | Attribute Bonuses | Weapon Mastery (+15% dmg) | Lore |
|---|---|---|---|
| Aluvian | +5 STR, +5 END | Heavy Weapons | Hardy meadowlanders of Ispar |
| Gharu'ndim | +5 FOC, +5 SELF | War Magic | Desert scholars and mages |
| Sho | +5 COORD, +5 QUICK | Light Weapons | Disciplined eastern warriors |
| Viamontian | +3 STR, +3 COORD, +3 END | Heavy Weapons | Ambitious nobles |
| Umbraen | +5 FOC, +5 QUICK | Missile Weapons | Shadow-touched outcasts |

### Design Notes
- Bonuses are small but meaningful with the tight 200-point budget
- Mastery is the real differentiator — 15% damage to an entire weapon category
- **TODO:** Consider adding a unique racial passive per heritage (e.g., Aluvian: +5% HP, Gharu'ndim: faster portal cast, Sho: +dodge%, Viamontian: +loot luck, Umbraen: enemies glow in dark)
- **TODO:** Heritage-specific cosmetic death/extract animations

---

## 3. ATTRIBUTES

**Creation Budget:** 200 total, 10 minimum each, 100 maximum innate. 60 locked in minimums = **140 free points.**

| Attribute | Abbr | What It Governs |
|---|---|---|
| Strength | STR | Melee damage modifier, Heavy Weapons formula, burden capacity |
| Endurance | END | **Health (END/2 + 10)**, natural regen, damage resistance |
| Coordination | COORD | Ranged/finesse damage, Missile/Light Weapons formula, Healing formula |
| Quickness | QUICK | Movement speed, attack speed, Melee Defense formula, Run skill |
| Focus | FOC | Magic damage, War Magic formula, Healing bonus, Item Magic formula |
| Self | SELF | XP gain multiplier, mana/willpower, War Magic formula, Item Magic formula |

### Innate vs Raised
- **Innate** = set at character creation (10-100). Cannot be changed post-creation.
- **Raised** = points added by spending XP after creation. No hard cap (long-term sink).
- **Effective** = Innate + Raised + Heritage Bonus. This is what formulas use.

### XP Cost to Raise Attributes (AC-faithful escalating cost)
Each attribute is raised independently. Cost escalates per point already raised in that attribute:

| Points Raised | XP Cost for Next Point |
|---|---|
| 0 → 1 | 50 |
| 1 → 2 | 70 |
| 2 → 3 | 98 |
| 3 → 4 | 137 |
| 4 → 5 | 192 |
| 5 → 6 | 269 |
| 10 → 11 | ~1,450 |
| 20 → 21 | ~41,800 |
| 50 → 51 | ~2.5M |

**Formula:** `cost = floor(50 × 1.4^n)` where n = points already raised in that attribute.

Early points are cheap — you can spread a few hundred XP across multiple attributes. But pushing any single attribute high becomes exponentially expensive. This is the long-term progression sink.

### Where to Spend XP on Attributes
- **In the Hub (Lifestone):** Full attribute spending panel with costs visible. Safe, plan your build.
- **During a Run (Quick Menu):** Press TAB or tap a button to open a compact spend panel. Spend XP earned this run immediately for a power boost mid-combat. Risky — if you die, you keep the attribute gains (XP was already spent) but lose your gear.

### Secondary Attributes (derived)
- **Health** = `floor(Effective END / 2) + 10`
  - Range: 15 (END 10) to 60+ (END 100+)
  - Raising END via XP is the primary way to increase survivability over time
- **TODO:** Consider adding Stamina and/or Mana if game complexity warrants it later

### Attribute Scaling in Combat (current values)
- **Damage mod:** `1 + (effective_attr - 10) × 0.008` — governs weapon damage via skill's governing attr
- **Speed mod (QUICK):** `1 + (QUICK - 10) × 0.005` — weapon fire rate + movement
- **XP mod (SELF):** `1 + (SELF - 10) × 0.005` — more XP per kill (compounds over runs)
- **Armor mod (END):** `END × 0.004` — small innate damage reduction

---

## 3B. XP SYSTEM (AC-Faithful)

### Core Principle
**XP is a currency, not just a progress bar.** Earned from kills, spent on attributes and skills. This is the heart of AC's character system.

### XP Earning
- Every enemy kill awards XP based on enemy type (Drudge=3, Olthoi=8, Tusker=12, etc.)
- XP is earned during the run and **always persists** — even on death
- SELF attribute increases XP earned per kill (multiplier)

### XP Spending (two targets)
1. **Attributes** — raise any of the 6 primary attributes by spending XP (escalating cost per point)
2. **Skills** — raise any trained or specialized skill by spending XP (escalating cost, cheaper if specialized)

### XP Spending Locations
- **Hub (Lifestone):** Full panel showing all attributes and skills with current values and next-point cost. Safe planning.
- **During Run (Quick Menu):** Press TAB to open compact spend panel. Spend immediately for mid-run power boost. Panel pauses weapon firing but enemies still move — adds tension.

### Total XP vs Unspent XP
- **Total XP** = all XP ever earned across all runs. Drives character level.
- **Unspent XP** = XP available to spend on attributes/skills. Decreases as you spend.
- Leveling up happens based on total XP thresholds, independent of spending.

### Character Level (from Total XP)
Level is a milestone tracker. It doesn't directly grant power — it unlocks **skill credits**.

| Level | Total XP Required | Skill Credits Earned |
|---|---|---|
| 1 | 0 | — (32 from creation) |
| 5 | 500 | +1 |
| 10 | 2,000 | +2 |
| 15 | 5,000 | +2 |
| 20 | 10,000 | +2 |
| 25 | 20,000 | +2 |
| 35 | 50,000 | +2 |
| 50 | 150,000 | +2 |

**Max additional credits from leveling: +13** (bringing total to 45 over time).
This lets players gradually train new skills and open new weapon types across many runs.

### Skill XP Costs (AC-faithful: specialized = half cost)

Skills can be raised by spending XP, but ONLY if trained or specialized. Untrained skills cannot be raised with XP.

**Specialized skills cost ~half the XP per point** compared to trained. This is the real payoff of specializing — not just the +10 bonus, but dramatically cheaper long-term investment.

| Points Raised in Skill | Trained Cost | Specialized Cost |
|---|---|---|
| 0 → 1 | 30 | 15 |
| 1 → 2 | 42 | 21 |
| 2 → 3 | 59 | 30 |
| 3 → 4 | 82 | 41 |
| 4 → 5 | 115 | 58 |
| 5 → 6 | 161 | 81 |
| 10 → 11 | ~870 | ~435 |
| 20 → 21 | ~25,000 | ~12,500 |

**Formula:**
- Trained: `cost = floor(30 × 1.4^n)`
- Specialized: `cost = floor(15 × 1.4^n)`

### How Raised Skill Points Affect Gameplay
Each point raised in a skill adds directly to the skill's effective value:

```
Effective Skill = Base (from attribute formula) + Raised Points + (Specialized ? 10 : 0)
```

Effective skill value then feeds into:
- **Weapon skills:** Higher skill = more damage, more projectiles, larger AoE
- **Melee Defense:** Higher skill = higher evade chance
- **Healing:** Higher skill = more HP per tick
- **Item Magic:** Higher skill = faster portal cast, higher rarity cap
- **Run:** Higher skill = faster movement

### Strategic XP Decisions
The player constantly faces tradeoffs:
- **Raise STR** for flat melee damage across all heavy weapons? Or **raise Heavy Weapons skill** for the same effect but only for that weapon type?
- **Pump END** for more HP to survive? Or **pump Melee Defense** to avoid hits entirely?
- **Invest in SELF** for more XP per kill? (Compounds over time — the "XP farming" build)
- **Save XP** for a big attribute push later? Or **spend now** mid-run to survive the current wave?

This is the depth that made AC's character system legendary.

---

## 4. SKILLS (Deep Design)

**Budget:** 32 credits at creation + up to 13 from level milestones = 45 lifetime max.
Train first, then specialize. Spec cap: 70 total (train+spec credits count).
Specialized = +10 base bonus + half XP cost to raise.

### Design Principles
1. **Every skill should feel different to play.** Not just stat sticks — each should change how the screen LOOKS and how the player MOVES.
2. **No skill should be mandatory.** Every build should be viable without any single skill.
3. **Trade skills must have tangible in-run effects.** No dead skills.
4. **Credit cost reflects power.** War Magic (8/8) should feel dramatically more impactful than Run (2/2).
5. **Specializing should change behavior, not just numbers.** Spec bonuses add new mechanics.

---

### COMBAT SKILLS

#### ⚔️ Heavy Weapons — Cost: 6/6 — Formula: (STR+COORD)/3
**Fantasy:** You're a plate-armored knight swinging massive weapons in wide arcs.
**Governs:** War Axe, Greatsword auto-attacks.
**Visual:** Gold/bronze spinning blades in a wide orbit around the player.

| Level | Effect |
|---|---|
| Untrained | 1 slow orbiting blade, tiny radius, 0.4x damage |
| Trained | 2 blades, medium radius, full damage |
| Raised (per 10 pts) | +1 blade (max 6), +5% radius |
| Specialized bonus | Blades deal **cleave damage** — hits pass through to enemies behind the target. Slower swing but devastating in crowds. |

**Damage type:** Slash/Bludgeon (randomized per swing for visual variety)
**Strength of build:** Best sustained AoE melee damage. Rewards standing your ground.
**Weakness:** Slow attack speed. Enemies that kite or are fast (Shadows) are hard to hit.

---

#### 🗡️ Light Weapons — Cost: 4/4 — Formula: (COORD+QUICK)/3
**Fantasy:** A nimble duelist with flickering blades striking rapidly.
**Governs:** Swift Blade, Dagger auto-attacks.
**Visual:** Green/silver rapid small slashes in a tight orbit.

| Level | Effect |
|---|---|
| Untrained | 1 fast tiny blade, very small radius, 0.4x damage |
| Trained | 2 blades, small radius, full damage, fast fire rate |
| Raised (per 10 pts) | +1 blade (max 5), +3% speed |
| Specialized bonus | Every 5th hit triggers a **dash strike** — player lunges forward a short distance, dealing 2x damage in a line. Creates a visual slash trail. |

**Damage type:** Pierce/Slash
**Strength of build:** Highest single-target DPS. Great for burning down Tuskers/Olthoi.
**Weakness:** Tiny range. Must be close to enemies. Dangerous against swarms.

---

#### 🏹 Missile Weapons — Cost: 6/6 — Formula: COORD/2
**Fantasy:** A disciplined archer raining arrows from a safe distance.
**Governs:** Bow, Crossbow auto-attacks.
**Visual:** Tan/gold arrow lines firing in the direction you face.

| Level | Effect |
|---|---|
| Untrained | 1 arrow, short range, 0.4x damage |
| Trained | 2 arrows in a spread, full range, full damage |
| Raised (per 10 pts) | +1 arrow (max 5), +1 pierce |
| Specialized bonus | Arrows gain **homing** — slight tracking toward nearest enemy. Also unlocks **Volley** every 8 seconds: fires a burst of 8 arrows in a cone. |

**Damage type:** Pierce
**Strength of build:** Safest combat skill. Damage at range, good pierce for lines.
**Weakness:** Must face enemies to fire. Lower total AoE than War Magic.

---

### MAGIC SKILLS

#### 🔥 War Magic — Cost: 8/8 — Formula: (FOC+SELF)/3
**Fantasy:** A devastating battlemage raining elemental destruction.
**Governs:** Fire Bolt, Frost Ring, Lightning Arc auto-attacks. All three fire simultaneously at different rates.
**Visual:** Mix of orange fireballs, blue frost rings, and white-blue lightning chains.

| Level | Effect |
|---|---|
| Untrained | Only Fire Bolt (1 projectile, slow, 0.4x damage) |
| Trained | Fire Bolt + Frost Ring. Fire Bolt fires 2 homing projectiles. Frost Ring expands outward. |
| Raised (per 10 pts) | +1 Fire Bolt projectile (max 4), +10% Frost Ring radius, +1 Lightning chain |
| Specialized bonus | Unlocks **Lightning Arc** as third weapon. Also all spells gain **elemental rotation** — damage type cycles Fire→Cold→Lightning→Acid, matching enemy weaknesses automatically. |

**Damage type:** Fire, Cold, Lightning (cycling)
**Strength of build:** Highest raw AoE damage. Three simultaneous weapon types. Visual spectacle.
**Weakness:** Expensive (8/8 credits), requires high FOC+SELF (two dump stats for melee builds). Very fragile — no defensive skills left if you spec this.

---

#### 💚 Healing — Cost: 4/4 — Formula: (FOC+COORD)/3
**Fantasy:** A life mage sustaining themselves through endurance.
**Governs:** Heal Pulse auto-cast (passive, periodic).
**Visual:** Green expanding ring centered on player.

| Level | Effect |
|---|---|
| Untrained | Heal 0.5 HP every 12 seconds |
| Trained | Heal 1 HP + (FOC bonus) every 10 seconds |
| Raised (per 10 pts) | +0.3 HP per tick, -0.5 sec off interval (min 4 sec) |
| Specialized bonus | Heal pulse also grants a **shield** — absorbs the next hit completely (1 hit, refreshes each pulse). Visual: faint green shimmer around player. |

**Strength of build:** Sustain. The only way to heal during combat (no potions yet).
**Weakness:** Doesn't help you kill anything. Slow healing won't save you from burst damage.

---

#### 🌀 Item Magic — Cost: 4/4 — Formula: (FOC+SELF)/3
**Fantasy:** A portal mage who manipulates space to extract loot.
**Governs:** Portal summoning, extraction rarity cap, backpack capacity.
**Visual:** Purple swirling portal effects.

| Level | Effect |
|---|---|
| Untrained | 12 sec portal cast, max T2 extraction, 4 backpack slots |
| Trained | 8 sec cast (minus skill scaling), max T3 extraction, 6 backpack slots |
| Raised (per 10 pts) | -0.3 sec cast time (min 2 sec) |
| Specialized bonus | Max T5 extraction (everything). **8 backpack slots.** Portal cast also creates a brief **gravity well** that pulls nearby loot toward you before you vanish. |

**Strength of build:** The Extractor. Best loot retention, fastest portal, biggest backpack.
**Weakness:** Zero combat contribution. Every credit spent here is a credit NOT spent on damage or defense.

---

### DEFENSE SKILLS

#### 🛡️ Melee Defense — Cost: 4/4 — Formula: (COORD+QUICK)/3
**Fantasy:** A seasoned fighter who reads enemy attacks and sidesteps them.
**Governs:** Evade chance when enemies hit you.
**Visual:** Blue "EVADE" text flash when triggered.

| Level | Effect |
|---|---|
| Untrained | 0% evade chance |
| Trained | Evade chance = skill × 0.2% (max ~10% at base) |
| Raised (per 10 pts) | +2% evade chance |
| Specialized bonus | Evade cap raised to 40% (from 30%). Successful evades trigger a **counterattack** — a small shockwave that damages and pushes back nearby enemies. Visual: blue ripple effect. |

**Strength of build:** Survivability. The only way to avoid damage besides not getting hit.
**Weakness:** Probabilistic — you can still die to bad RNG. Doesn't help against ranged enemies (TODO: Missile Defense).

---

### GENERAL SKILLS

#### 👟 Run — Cost: 2/2 — Formula: QUICK
**Fantasy:** Pure speed. Outrun everything.
**Governs:** Movement speed.
**Visual:** Speed lines when moving fast.

| Level | Effect |
|---|---|
| Untrained | Base speed from QUICK only (slow) |
| Trained | Speed = base + skill × 0.012. Noticeable boost. |
| Raised (per 10 pts) | +1.2% movement speed |
| Specialized bonus | Unlocks **Sprint** — double speed for 3 seconds, 15 sec cooldown. Activated automatically when HP drops below 25%. Visual: white streak trail. Also: enemies you run through at sprint speed take minor damage. |

**Strength of build:** Repositioning, kiting, escape. The cheapest skill (2/2) with universal value.
**Weakness:** Doesn't help you kill or survive hits. Pure utility.

---

### TRADE SKILLS

#### ⚗️ Alchemy — Cost: 2/2 — Formula: (FOC+COORD)/3
**Fantasy:** A potion brewer who finds chemical advantages on the battlefield.
**Governs:** Potion drops, buff effects.
**Visual:** Colored potion bottles that appear on wave clear.

| Level | Effect |
|---|---|
| Untrained | No effect (dead skill if untrained) |
| Trained | When a wave is cleared, 1 health potion spawns near player (heals 20% HP on pickup). Also: 5% chance per enemy kill to drop a **damage potion** (10 sec +25% damage buff). |
| Raised (per 10 pts) | +1% potion drop chance per kill, +5% potion effectiveness |
| Specialized bonus | Wave clear spawns **2 potions** (health + choice of damage/speed/armor). Also: potions last 50% longer. Unlocks rare **Elixir** drop from bosses (TODO) — permanent +1 to a random attribute. |

**Strength of build:** Sustain + burst damage from potions. Great supplement to any build.
**Weakness:** RNG-dependent. You might not get the potion you need. 

---

### SKILL BALANCE MATRIX

| Skill | Credits | Damage | Survival | Utility | Visual Impact | Best Heritage |
|---|---|---|---|---|---|---|
| Heavy Weapons | 6/6 | ★★★★ | ★ | ★ | ★★★ | Aluvian, Viamontian |
| Light Weapons | 4/4 | ★★★ | ★ | ★★ | ★★ | Sho |
| Missile Weapons | 6/6 | ★★★ | ★★ | ★ | ★★ | Umbraen |
| War Magic | 8/8 | ★★★★★ | ★ | ★ | ★★★★★ | Gharu'ndim |
| Healing | 4/4 | — | ★★★★ | ★ | ★★ | Any |
| Item Magic | 4/4 | — | ★ | ★★★★★ | ★★★ | Gharu'ndim, Umbraen |
| Melee Defense | 4/4 | ★ (spec) | ★★★★ | ★ | ★★ | Sho |
| Run | 2/2 | — | ★★ | ★★★★ | ★★ | Sho, Umbraen |
| Alchemy | 2/2 | ★★ | ★★ | ★★★ | ★ | Any |

### CREDIT BUDGET ANALYSIS (32 starting)

**Tight aggressive builds (use all 32):**
- War Mage: War Magic spec(16) + Item Magic train(4) + Healing train(4) + Run train(2) = 26, 6 left for Melee Def train(4) = 30 ✓
- Warrior: Heavy spec(12) + Melee Def spec(8) + Healing train(4) + Run train(2) = 26, 6 left for Alchemy(2) + Item Magic train(4) = 32 ✓
- Archer: Missile spec(12) + Item Magic train(4) + Healing train(4) + Run spec(4) + Melee Def train(4) = 28, 4 left for Alchemy(2) = 30 ✓

**Economy builds (save credits for later):**
- Minimalist: Heavy train(6) + Run train(2) + Healing train(4) = 12, save 20 for milestone-earned skills
- Extractor: Item Magic spec(8) + Healing train(4) + Run train(2) = 14, very weak combat but 18 credits saved

**Impossible builds (over budget — forces tradeoffs):**
- Heavy spec(12) + War Magic spec(16) = 28, only 4 left — can't afford defense or extraction
- All combat: Heavy(12) + Missile(12) + War Magic(16) = 40 — impossible at creation, needs 8 from milestones

This is the tradeoff engine. You CANNOT have everything. Every build has a hole.

---

## 5. GEAR SYSTEM (AC-Faithful Loot Tiers)

### Core Principle (from AC)
In AC, loot was organized into 8 tiers tied to creature difficulty. Weapon **max damage** was tied to **wield requirement** (character level needed to use it). Weapon **modifiers** (attack bonus %, damage bonus %) were tied to the **loot tier** of the creature that dropped it. Armor **type** availability expanded at higher tiers (chainmail from T1, covenant/lorica from T5+). Our system adapts this to a survivors context.

### Loot Tiers (adapted from AC's 8-tier system)

We use 6 tiers to match our 10-wave structure. Waves drop loot from specific tiers.

| Tier | AC Name | Our Name | Color | Waves | Armor Level Range | Weapon Dmg Range | Modifier Range | Armor Types Available |
|---|---|---|---|---|---|---|---|---|
| T1 | Tier 1-2 | Scuffed | Gray #7a7a7a | 1-2 | AL 20-40 | 3-5 base | 0-2% | Leather, Chainmail |
| T2 | Tier 3 | Serviceable | Green #2aaa2a | 3-4 | AL 40-80 | 5-8 base | 2-5% | + Studded Leather, Scale |
| T3 | Tier 4-5 | Quality | Blue #4488ee | 5-6 | AL 80-140 | 8-12 base | 5-10% | + Platemail, Yoroi |
| T4 | Tier 6 | Superior | Purple #aa44ee | 7-8 | AL 140-200 | 12-18 base | 10-15% | + Celdon, Amuli, Koujia |
| T5 | Tier 7 | Exquisite | Orange #ee9922 | 9-10 | AL 200-260 | 18-24 base | 15-20% | + Covenant, Lorica |
| T6 | Tier 8 | Atlan | Gold #ffd700 | 10 (rare) | AL 260-320 | 24-32 base | 20-25% | + Atlan, Haebrean |

### Weapon Properties (per piece)
Following AC's system, each weapon has:
- **Base Damage** — flat number, scales with tier (the "wield req" equivalent)
- **Damage Modifier %** — percentage bonus, scales with tier (AC's "damage mod")
- **Attack Modifier %** — accuracy/speed bonus, scales with tier (AC's "attack mod")
- **Damage Type** — Slash, Pierce, Bludgeon, Fire, Cold, Lightning, Acid (visual + enemy weakness matching)

| Property | T1 | T2 | T3 | T4 | T5 | T6 |
|---|---|---|---|---|---|---|
| Base Damage | 3-5 | 5-8 | 8-12 | 12-18 | 18-24 | 24-32 |
| Damage Mod % | 0-2% | 2-5% | 5-10% | 10-15% | 15-20% | 20-25% |
| Attack Mod % | 0-1% | 1-3% | 3-6% | 6-10% | 10-14% | 14-18% |

### Armor Properties (per piece)
Each armor piece has:
- **Armor Level (AL)** — flat damage reduction value, scales with tier
- **Protection Type** — what damage types it resists best (Slash/Pierce/Bludgeon/Elemental)
- **Bonus Stat** — secondary stat (HP, speed, etc.) appears at T3+

| Property | T1 | T2 | T3 | T4 | T5 | T6 |
|---|---|---|---|---|---|---|
| Armor Level | 20-40 | 40-80 | 80-140 | 140-200 | 200-260 | 260-320 |
| Bonus Stat Chance | 0% | 10% | 30% | 50% | 70% | 90% |
| Max Bonus Value | — | low | med | med-high | high | very high |

### Armor Types by Tier (AC-faithful progression)

| Armor Type | First Available | AC Equivalent | Visual Feel |
|---|---|---|---|
| Leather | T1 | Leather | Brown, light |
| Chainmail | T1 | Chainmail | Gray, metal rings |
| Studded Leather | T2 | Studded Leather | Brown + metal studs |
| Scale | T2 | Scalemail | Green-gray, reptilian |
| Platemail | T3 | Platemail | Silver, heavy |
| Yoroi | T3 | Yoroi | Red/black, Sho-style |
| Celdon | T4 | Celdon | Blue-silver, Empyrean |
| Amuli | T4 | Amuli | Dark, lightweight |
| Koujia | T4 | Koujia | Gold-trimmed, Sho |
| Covenant | T5 | Covenant | White-gold, ornate |
| Lorica | T5 | Lorica | Dark purple, rare |
| Atlan | T6 | Atlan/Haebrean | Glowing, legendary |

### Equipment Slots
Weapon, Head, Chest, Hands, Feet, Trinket — 6 slots total.

### Weapon Types by Skill

| Weapon | Skill Required | Damage Type | Speed | Range | Visual |
|---|---|---|---|---|---|
| Sword | Heavy Weapons | Slash | Slow | Melee orbit | Gold spinning blade |
| Axe | Heavy Weapons | Bludgeon | Slow | Melee orbit | Bronze spinning axes |
| Katar | Light Weapons | Pierce | Fast | Small melee orbit | Green quick slashes |
| Dagger | Light Weapons | Pierce | Very fast | Tiny orbit | Silver flicker |
| Bow | Missile Weapons | Pierce | Medium | Projectile line | Tan arrows |
| Crossbow | Missile Weapons | Pierce | Slow | Projectile line | Dark bolts |
| Fire Bolt | War Magic | Fire | Medium | Homing projectile | Orange fireballs |
| Frost Ring | War Magic | Cold | Slow | AoE ring | Blue expanding ring |
| Lightning | War Magic | Lightning | Slow | Chain arc | Blue-white chain |
| Acid Stream | War Magic | Acid | Medium | Homing projectile | Green projectiles |
| Heal Pulse | Healing | — | Very slow | Self AoE | Green pulse |

### Named/Unique Items (T6 Atlan only, very rare)
- [ ] **Sword of Lost Light** — T6 Heavy, 30 base dmg, 24% mod, unique glow
- [ ] **Asheron's Sigil** — Trinket, all stats +small, portal cast -2s
- [ ] **Virindi Mask** — Head, FOC +10, enemies confused occasionally
- [ ] **Olthoi Crown** — Head, END +10, thorns (enemies take dmg on hit)
- [ ] **Atlan Bow** — T6 Missile, 28 base, 22% mod, cannot be lost on death

### Armor Sets (3pc / 5pc bonuses)
- [ ] **Covenant Set** (T5+) — 3pc: +10% AL, 5pc: damage reflect
- [ ] **Celdon Set** (T4+) — 3pc: +15% HP, 5pc: heal on kill
- [ ] **Amuli Set** (T4+) — 3pc: +10% speed, 5pc: dodge bonus
- [ ] **Olthoi Armor** (T4+) — 3pc: +10% damage, 5pc: poison on hit
- [ ] **Haebrean Set** (T6) — 3pc: +all stats, 5pc: faster portal + loot luck

### Portal Extraction Rarity Caps (updated for new tiers)

| Item Magic Level | Max Tier Extractable |
|---|---|
| Untrained | T1 Scuffed, T2 Serviceable |
| Trained | + T3 Quality |
| Specialized | + T4 Superior, T5 Exquisite, T6 Atlan |

### Loot Drop Logic
```
When enemy dies:
  1. Roll drop chance (enemy.dropRate)
  2. Determine tier from wave number (see tier-wave mapping)
  3. Roll within tier's stat ranges (randomized within min-max)
  4. At T3+, roll for bonus stat
  5. At T5+, roll for set piece
  6. At T6, tiny chance for Named item
```

---

## 6. EXTRACTION / PORTAL SYSTEM

### Item Magic Skill Tiers

| Level | Cast Time | Max Rarity Extractable | Notes |
|---|---|---|---|
| Untrained | 12 sec | Sturdy (tier 1) | Brutal — almost impossible mid-wave |
| Trained | 8 sec | Fine (tier 2) | Tight but doable if you clear space |
| Specialized | 4 sec | Atlan (tier 4) | Fast enough for between-wave windows |

### Portal Mechanics
- Press P to begin casting (during combat or between-wave countdown)
- Player is **movement-locked** during cast
- **Any hit interrupts** the cast (resets timer)
- Weapons still fire during cast (auto-attacks continue)
- Melee Defense evades can save your cast
- Purple swirling visual + progress bar during cast
- Backpack items above rarity cap are **left behind**
- Surviving all 10 waves = auto-extract, no rarity cap

### TODO: Portal Enhancements
- [ ] Item Magic skill level could also increase backpack size (untrained=4, trained=6, spec=8)
- [ ] Portal recall to specific Lifestone locations (cosmetic hub customization)
- [ ] Emergency portal scroll consumable (instant cast, one use, found as rare loot)
- [ ] Portal tie-in: higher Item Magic = portal visual is more dramatic

---

## 7. WAVE DESIGN

### Current Waves

| # | Name | Enemy Types | Count | Theme Color | Design Intent |
|---|---|---|---|---|---|
| 1 | Drudge Scouts | Drudge | 10 | Brown | Tutorial wave — learn movement |
| 2 | Shadow Ambush | Shadow | 8 | Purple | Fast enemies — test kiting |
| 3 | Drudge Warband | Drudge, Banderling | 14 | Tan | Mixed melee swarm |
| 4 | Olthoi Brood | Olthoi | 6 | Green | Tanky enemies — DPS check |
| 5 | Shadow Legion | Shadow, Virindi | 12 | Dark Purple | Fast + magic enemies |
| 6 | Tusker Stampede | Tusker | 6 | Warm Brown | Huge HP pools — attrition |
| 7 | Virindi Apparatus | Virindi, Shadow | 10 | Violet | Swarm + high XP |
| 8 | The Horde | All types | 20 | Gold | Chaos wave — everything at once |
| 9 | Olthoi Guard | Olthoi, Tusker | 12 | Dark Green | Double tank wave |
| 10 | Bael'Zharon | Shadow, Virindi, Tusker | 16 | Dark Violet | Final boss wave |

### Enemy Stats (base, scaled by `1 + waveNum * 0.18`)

| Enemy | HP | Speed | Size | XP | Drop% | Visual |
|---|---|---|---|---|---|---|
| Drudge | 20 | 1.0 | 12 | 3 | 12% | Brown, letter D |
| Olthoi | 55 | 0.7 | 18 | 8 | 22% | Green, letter O |
| Shadow | 30 | 1.3 | 14 | 5 | 18% | Purple, letter S |
| Tusker | 80 | 0.5 | 22 | 12 | 28% | Warm brown, letter T |
| Virindi | 45 | 0.9 | 15 | 10 | 30% | Violet, letter V |
| Banderling | 65 | 0.6 | 20 | 7 | 18% | Olive, letter B |

### TODO: Wave Expansion
- [x] Boss enemies — one named boss per wave (see Boss Design below)
- [ ] Elite modifiers (Empowered Drudge = 2x HP, glows, guaranteed drop)
- [ ] Environmental hazards per wave (acid pools, shadow zones that debuff)
- [ ] Bonus wave 11+ for ascension system (harder scaling, better loot)

### Boss Design — One Named Boss Per Wave

Every wave includes one BOSS enemy that spawns alongside the regular enemies. Bosses are:
- **2-3x larger** than normal enemies (visually distinct)
- **Named** with AC-faithful lore names
- **5-10x HP** of the base enemy type for that wave
- **Unique mechanic** per boss (charges, teleports, spawns adds, etc.)
- **Guaranteed loot drop** one tier higher than the wave's normal tier
- **Glowing aura** (pulsing ring around them so you can spot them in the swarm)
- **XP reward** = 5x normal enemy of that type

| Wave | Boss Name | Base Type | HP Mult | Mechanic | Drop Tier | Lore |
|---|---|---|---|---|---|---|
| 1 | Bloody Bones | Drudge | 5x | **Enrage** — speeds up when below 50% HP | T1 guaranteed | A drudge chieftain painted in ritual blood |
| 2 | The Whisperer | Shadow | 6x | **Blink** — teleports to random position every 3 sec | T1 guaranteed | A shadow that speaks in dead languages |
| 3 | Grunter the Brute | Banderling | 6x | **War Cry** — when hit, nearby enemies get +30% speed for 5 sec | T2 guaranteed | Alpha banderling with bone armor |
| 4 | The Brood Mother | Olthoi | 8x | **Spawn Eggs** — every 5 sec spawns 2 mini Olthoi (half HP, no drops) | T2 guaranteed | Olthoi matriarch defending her nest |
| 5 | Martine the Mad | Virindi | 7x | **Phase Shift** — immune to damage for 2 sec every 6 sec (flickers visually) | T3 guaranteed | A human twisted by Virindi experiments |
| 6 | Torgluuk | Tusker | 10x | **Ground Pound** — every 4 sec damages everything in a radius around him | T3 guaranteed | Ancient tusker king, twice the size |
| 7 | The Hollow One | Virindi | 8x | **Mirror Images** — spawns 2 decoys (1 HP, no drops) that look identical | T4 guaranteed | A virindi puppeteer pulling strings |
| 8 | Pandemonium | Mixed | 8x | **Frenzy Aura** — all enemies within 100px of boss attack 50% faster | T4 guaranteed | A writhing mass of merged creatures |
| 9 | Olthoi Eviscerator | Olthoi | 10x | **Charge** — every 5 sec lunges at player at 3x speed | T5 guaranteed | Royal guard to the queen herself |
| 10 | Bael'Zharon | Shadow | 15x | **Dark Nova** — every 8 sec fires expanding ring of damage outward + **Regenerates** 1% HP/sec | T5 guaranteed + chance T6 | The Hopeslayer himself |

### Boss Visual Design
- **Size:** Boss radius = base enemy radius × 2.5
- **Glow:** Pulsing colored aura ring that matches their wave color
- **Name tag:** Boss name displayed above their health bar
- **Health bar:** Wider bar, different color (gold border)
- **Death explosion:** Large particle burst with 2x particles, screen flash

---

## 8. ECONOMY & PROGRESSION

### XP (the core currency — AC-faithful)
- Earned per kill (enemy XP value × SELF modifier)
- **Always persists** across runs (even on death)
- **Spent on two things:** Attributes and Skills (both escalating cost)
- **Spending locations:** Hub (safe planning) OR during run via TAB quick menu
- Total XP drives character level (for skill credit milestones)
- Unspent XP is the spendable pool

### XP Flow Per Run
```
Kill enemies → earn XP → choose: spend now mid-run for immediate power
                                   OR save for hub to plan carefully
                          ↓
                    Total XP accumulates → level up → earn skill credits
                                                       → train new skills
                                                       → open new weapons
```

### Gear Tokens
- 1 earned per 60 seconds survived in a run
- Spent at Lifestone for random gear (currently random slot, random tier weighted to low)
- **TODO:** Token tiers (bronze=T1-2, silver=T3-4, gold=T5-6)
- **TODO:** Targeted token spending (pick the slot, random tier)

### Vault
- 12 slots (current)
- **TODO:** Vault expansion via tokens or XP
- **TODO:** Salvage system — break down gear for materials used to upgrade other gear

### TODO: Crafting / Tinkering
Following AC's tinkering system:
- [ ] Salvage drops from enemies (Iron, Steel, Gold, Pyreal)
- [ ] Apply salvage to gear to boost stats (AC's tinkering)
- [ ] Alchemy skill governs success rate
- [ ] Failed tinkering destroys the salvage
- [ ] Successful tinkering adds a permanent stat boost to gear

---

## 9. BUILD ARCHETYPES (Validated Against Skill Design)

With 200 attr points (140 free), 32 skill credits, and 9 skills competing for resources, here are the core archetypes. Each is validated against credit budget, attribute spread, wave-by-wave viability, and extraction capability.

---

### 🛡️ THE COVENANT KNIGHT (STR/END Tank)
**Heritage:** Aluvian (+5 STR, +5 END) or Viamontian (+3 STR/COORD/END)
**Fantasy:** An armored wall that wades into the horde and cleaves through everything.

**Attributes (200):**
| STR | END | COORD | QUICK | FOC | SELF |
|---|---|---|---|---|---|
| 90 | 50 | 30 | 10 | 10 | 10 |

**Skills (32 credits):**
| Skill | Status | Cost | Why |
|---|---|---|---|
| Heavy Weapons | Specialized | 12 | Core damage — cleave through crowds |
| Melee Defense | Trained | 4 | Evade chance to survive standing in melee |
| Healing | Trained | 4 | Sustain between waves |
| Run | Trained | 2 | Minimum mobility |
| Alchemy | Trained | 2 | Potion drops for emergency heals |
| Item Magic | Trained | 4 | 8 sec portal, T3 extraction |
| **Total** | | **28** | 4 credits saved for milestone skills |

**Effective Stats at Creation:**
- Heavy Weapons base: (95+30)/3 = 41 + 10 spec = **51**
- Melee Defense base: (30+10)/3 = **13** (weak but trained)
- Health: 55/2 + 10 = **37 HP** (tankiest starting build)
- Mastery: +15% Heavy Weapons damage (Aluvian)

**Strengths:**
- Highest HP pool of any build
- Cleave spec demolishes wave 3 (Drudge Warband) and wave 8 (The Horde)
- Alchemy potions provide burst healing when Healing skill can't keep up
- Can survive hits that would kill a mage

**Weaknesses:**
- 10 QUICK = slowest character. Shadows (wave 2, 5) will swarm you
- 10 FOC/SELF = terrible portal casting. 8 sec cast, can only extract T3 Quality
- No ranged damage. Olthoi Brood (wave 4) with their large hitboxes are fine, but kiting Virindi (wave 7) is painful
- Late waves require finding speed gear or pumping Run with XP

**XP Priority:** END first (more HP), then Heavy Weapons skill (more damage), then QUICK (desperately needed mobility)

**Extraction Strategy:** Clear the wave completely, then portal during the 8-second gap. T3 cap means you can't extract the best loot early — need to invest XP into Item Magic over time.

**Wave-by-Wave:**
- W1-3: ★★★★★ Dominates. Cleave shreds Drudge/Banderling swarms.
- W4: ★★★★ Olthoi are tanky but Heavy does great single-target too.
- W5-6: ★★★ Shadows are fast, Tuskers are HP sponges. Starts to struggle with speed.
- W7-8: ★★ Virindi kiting and mixed swarms expose low mobility.
- W9-10: ★ Need significant XP investment or gear to survive.

---

### 🏹 THE SHO RANGER (COORD/QUICK Kiter)
**Heritage:** Sho (+5 COORD, +5 QUICK) or Umbraen (+5 FOC, +5 QUICK)
**Fantasy:** Never stops moving. Arrows rain behind you as you weave through enemies.

**Attributes (200):**
| STR | END | COORD | QUICK | FOC | SELF |
|---|---|---|---|---|---|
| 10 | 30 | 80 | 50 | 10 | 20 |

**Skills (32 credits):**
| Skill | Status | Cost | Why |
|---|---|---|---|
| Missile Weapons | Specialized | 12 | Homing arrows + Volley burst |
| Run | Specialized | 4 | Sprint escape at low HP |
| Melee Defense | Trained | 4 | Evade when enemies close gap |
| Healing | Trained | 4 | Sustain |
| Item Magic | Trained | 4 | Decent portal (FOC is low though) |
| **Total** | | **28** | 4 credits saved |

**Effective Stats at Creation:**
- Missile Weapons base: 85/2 = 42 + 10 spec = **52**
- Run base: 55 + 10 spec = **65** (fastest character)
- Melee Defense base: (85+55)/3 = **46** (excellent evade base!)
- Health: 30/2 + 10 = **25 HP** (fragile)

**Strengths:**
- Fastest movement in the game. Sprint spec = untouchable at low HP.
- Homing arrows + Volley make this the safest damage dealer
- Melee Defense base is actually HIGH because COORD+QUICK feed both Missile AND Defense
- Sho mastery = +15% Light Weapons... wait, that doesn't help. **Umbraen mastery = +15% Missile!** Better heritage pick.

**Weaknesses:**
- 25 HP. Two hits from a wave 6 Tusker and you're dead.
- 10 FOC = terrible portal cast speed. Item Magic base is (10+20)/3 = 10. Portaling is slow.
- No AoE at all. Wave 8 (The Horde, 20 enemies) is a nightmare — killing one at a time.
- SELF at 20 means lower XP gain per kill.

**XP Priority:** END (desperately need HP), then Missile skill (more arrows), then Item Magic (faster portal)

**Extraction Strategy:** Kite to a clear area during the 8-sec gap, portal out. With trained Item Magic and low FOC, portal takes ~7-8 sec. Tight but doable.

**Wave-by-Wave:**
- W1-2: ★★★★ Easy kiting, arrows melt Drudges and Shadows.
- W3-4: ★★★★ Warband is manageable by kiting. Olthoi are slow = free shots.
- W5-6: ★★★ Shadow speed matches yours. Tusker HP pools take forever.
- W7: ★★★★ Virindi are medium speed = good arrow targets.
- W8: ★★ The Horde overwhelms single-target. Need Volley spec badly.
- W9-10: ★★ Tank waves (Olthoi+Tusker) take forever to whittle down.

---

### 🔥 THE GHARU'NDIM WAR MAGE (FOC/SELF Glass Cannon)
**Heritage:** Gharu'ndim (+5 FOC, +5 SELF)
**Fantasy:** The screen is on fire. Three spell types simultaneously. Maximum visual spectacle.

**Attributes (200):**
| STR | END | COORD | QUICK | FOC | SELF |
|---|---|---|---|---|---|
| 10 | 20 | 10 | 10 | 80 | 70 |

**Skills (32 credits):**
| Skill | Status | Cost | Why |
|---|---|---|---|
| War Magic | Specialized | 16 | Three weapons + elemental rotation |
| Item Magic | Trained | 4 | Portal extraction (same attr pair!) |
| Healing | Trained | 4 | Only way to survive |
| Run | Trained | 2 | Minimum mobility |
| **Total** | | **26** | 6 credits saved for Melee Def later |

**Effective Stats at Creation:**
- War Magic base: (85+75)/3 = 53 + 10 spec = **63** (highest skill value of any build)
- Item Magic base: (85+75)/3 = **53** (excellent portal — same attrs!)
- Health: 20/2 + 10 = **20 HP** (glass cannon)
- XP modifier from SELF 75: 1 + (75-10)×0.005 = **1.325x XP** (farms XP fastest)

**Strengths:**
- THREE simultaneous weapons: Fire Bolt (homing), Frost Ring (AoE), Lightning (chain). The screen EXPLODES with color.
- Elemental rotation spec auto-matches enemy weaknesses.
- Item Magic is naturally strong because FOC+SELF governs both War Magic AND Item Magic. Best portal caster in the game.
- Highest XP gain per kill (1.325x) means faster leveling and more XP to spend.
- Gharu'ndim mastery = +15% War Magic damage.

**Weaknesses:**
- **20 HP.** One hit from wave 5+ and you're nearly dead.
- 10 COORD/QUICK = 0 Melee Defense base. You CANNOT evade. Every hit lands.
- 10 QUICK = slowest character tied with Knight. Can't run from anything.
- 16 credits on War Magic spec leaves very little for defense. This is a "kill them before they kill you" build.
- Need to earn credits from milestones to train Melee Defense.

**XP Priority:** END (survive), then War Magic skill (even more destruction), then QUICK or Run skill (mobility)

**Extraction Strategy:** Portal is your best tool. Item Magic base 53 means portal cast is ~5 sec trained. You can portal mid-wave if you create space with Frost Ring knockback. But one hit interrupts...

**Wave-by-Wave:**
- W1-3: ★★★★★ Everything melts. Fire Bolt homing + Frost Ring = clear before they reach you.
- W4: ★★★★ Olthoi are slow and tanky but Lightning chains through groups.
- W5: ★★★ Shadows are FAST. They close the gap and you have no defense.
- W6: ★★ Tuskers = huge HP pools, and if one reaches you, you're dead.
- W7-8: ★★★ Virindi are medium speed — manageable. Horde is chaos but your AoE is king.
- W9-10: ★ Without XP investment in END, you die to a stiff breeze.

---

### 🌀 THE UMBRAEN EXTRACTOR (FOC/QUICK Utility)
**Heritage:** Umbraen (+5 FOC, +5 QUICK)
**Fantasy:** You're not here to kill everything. You're here to survive, grab the loot, and portal out rich.

**Attributes (200):**
| STR | END | COORD | QUICK | FOC | SELF |
|---|---|---|---|---|---|
| 10 | 30 | 20 | 50 | 60 | 30 |

**Skills (32 credits):**
| Skill | Status | Cost | Why |
|---|---|---|---|
| Item Magic | Specialized | 8 | Fastest portal, T5 extraction, 8 backpack slots, gravity well |
| War Magic | Trained | 8 | Fire Bolt + Frost Ring for damage |
| Healing | Trained | 4 | Sustain |
| Run | Trained | 2 | Mobility |
| Melee Defense | Trained | 4 | Evade chance |
| Alchemy | Trained | 2 | Potion drops |
| **Total** | | **28** | 4 credits saved |

**Effective Stats at Creation:**
- Item Magic base: (65+35)/3 = 33 + 10 spec = **43**. Portal cast: ~3 sec. Extracts T5 Exquisite!
- War Magic base: (65+35)/3 = **33** (decent, not amazing)
- Melee Defense base: (20+55)/3 = **25** (moderate evade)
- Health: 30/2 + 10 = **25 HP**

**Strengths:**
- Fastest portal in the game (~3 sec). Can portal out mid-wave if you create even a brief opening.
- 8 backpack slots = 33% more loot per run than everyone else.
- T5 extraction cap means you can bring home Exquisite gear that other builds can't.
- Gravity well on portal pulls loot toward you — grab drops you missed.
- Well-rounded: has damage (War Magic), defense (Melee Def), sustain (Healing), utility (Alchemy).

**Weaknesses:**
- Master of nothing. War Magic trained (not spec) = no Lightning, no elemental rotation.
- Damage is middle of the road. Wave 6+ takes a long time to clear.
- The build's power is in extraction, not combat. You NEED to portal before things get too hard.
- Umbraen mastery is Missile Weapons, which you didn't train. No mastery bonus.

**XP Priority:** FOC (powers both War Magic and Item Magic), then War Magic skill, then END

**Extraction Strategy:** This IS the extraction build. Push to wave 5-6 for Quality/Superior loot, then portal out with 8 slots of gear. Repeat. Accumulate wealth through volume, not depth.

**Wave-by-Wave:**
- W1-4: ★★★ Adequate. Fire Bolt + Frost Ring handles it, just slower.
- W5-6: ★★★ This is your target zone. Good loot tier, manageable difficulty.
- W7+: ★★ Portal out. You've got what you came for.

---

### ⚔️🔥 THE VIAMONTIAN HYBRID (STR/COORD/FOC Flex)
**Heritage:** Viamontian (+3 STR, +3 COORD, +3 END)
**Fantasy:** A battlemage who swings a blade and slings spells. Jack of all trades.

**Attributes (200):**
| STR | END | COORD | QUICK | FOC | SELF |
|---|---|---|---|---|---|
| 50 | 30 | 40 | 10 | 40 | 30 |

**Skills (32 credits):**
| Skill | Status | Cost | Why |
|---|---|---|---|
| Heavy Weapons | Trained | 6 | Melee damage |
| War Magic | Trained | 8 | Fire Bolt + Frost Ring |
| Healing | Trained | 4 | Sustain |
| Run | Trained | 2 | Minimum mobility |
| Item Magic | Trained | 4 | Portal |
| **Total** | | **24** | 8 credits saved for future spec |

**Effective Stats at Creation:**
- Heavy Weapons base: (53+43)/3 = **32**
- War Magic base: (40+30)/3 = **23**
- Health: 33/2 + 10 = **26 HP**
- Viamontian mastery = +15% Heavy Weapons

**Strengths:**
- Two weapon types simultaneously: spinning blades AND fireballs. Visually interesting.
- Flexible XP spending: can pivot to either melee or magic dominance over time.
- 8 saved credits = can spec either Heavy OR War Magic when milestone credits arrive.
- Viamontian's spread bonuses (+3/+3/+3) complement the spread attribute build.

**Weaknesses:**
- Nothing is specialized. No cleave, no Lightning, no special mechanics.
- 10 QUICK = slow. No Melee Defense trained = every hit lands.
- Lower damage than any focused build. Waves take longer.
- "Good at everything, great at nothing" can feel frustrating in later waves.

**XP Priority:** Whatever feels weakest. The Hybrid's strength is adaptability.

**Extraction Strategy:** Moderate portal speed. Push to wave 5-6, portal during gap. 

**Wave-by-Wave:**
- W1-4: ★★★★ Two weapon types handle variety well.
- W5-7: ★★★ Adequate but not dominant. Mixed damage helps against mixed waves.
- W8+: ★★ Needs significant XP investment to push further.

---

### 🗡️💨 THE SHO BLADE DANCER (COORD/QUICK Assassin)
**Heritage:** Sho (+5 COORD, +5 QUICK)
**Fantasy:** A blur of steel. Dash in, kill, dash out. Highest single-target DPS.

**Attributes (200):**
| STR | END | COORD | QUICK | FOC | SELF |
|---|---|---|---|---|---|
| 10 | 20 | 80 | 60 | 10 | 20 |

**Skills (32 credits):**
| Skill | Status | Cost | Why |
|---|---|---|---|
| Light Weapons | Specialized | 8 | Dash strike + rapid attacks |
| Melee Defense | Specialized | 8 | 40% evade cap + counterattack |
| Run | Specialized | 4 | Sprint at low HP + contact damage |
| Healing | Trained | 4 | Sustain |
| **Total** | | **24** | 8 credits saved |

**Effective Stats at Creation:**
- Light Weapons base: (85+65)/3 = 50 + 10 = **60**
- Melee Defense base: (85+65)/3 = 50 + 10 = **60** (highest defense in game!)
- Run base: 65 + 10 = **75** (fastest character in game!)
- Health: 20/2 + 10 = **20 HP** (glass cannon)

**Strengths:**
- THREE specialized skills — all from the same COORD+QUICK attribute pair. Maximum efficiency.
- 60 Melee Defense with spec = ~30-40% evade with counterattack shockwaves. You're dodging constantly.
- Dash strike every 5th hit = extreme burst. Sprint at low HP = emergency escape.
- Sho mastery = +15% Light Weapons. Perfect match.
- The "anime swordsman" build. Visually the most dynamic character.

**Weaknesses:**
- **20 HP.** The lowest. Every hit that gets through evade is devastating.
- No ranged damage at all. Must be in melee range constantly.
- No Item Magic trained = 12 sec portal, T2 extraction cap. Terrible extractor.
- No Alchemy = no potions. No War Magic = no AoE. Pure melee assassin.
- Wave 8 (The Horde) with 20 enemies and no AoE is a nightmare.

**XP Priority:** END (desperately), then Light Weapons skill (more DPS), then Item Magic training from milestone credits

**Extraction Strategy:** Almost can't extract early. Need milestone credits to train Item Magic. Until then, you either full-clear all 10 waves or die trying. The high-risk build.

**Wave-by-Wave:**
- W1-2: ★★★★★ Dash-strike destroys small groups instantly.
- W3-4: ★★★★ Warband is great for dash chaining. Olthoi take time but manageable.
- W5: ★★★★★ Shadows are fast but YOU'RE faster. Evade + dash = perfect counter.
- W6: ★★★ Tusker HP pools = long fight, but evade keeps you alive.
- W7: ★★★ Virindi mixed waves test your positioning.
- W8: ★ The Horde. 20 enemies, no AoE. This is where the Blade Dancer dies.
- W9-10: ★★ If you survived, you're a god. But you probably didn't.

---

### BUILD COMPARISON MATRIX

| Build | HP | DPS | AoE | Speed | Portal | Loot | Visual | Fun Factor |
|---|---|---|---|---|---|---|---|---|
| Covenant Knight | ★★★★★ | ★★★★ | ★★★★ | ★ | ★★ | ★★ | ★★★ | Satisfying |
| Sho Ranger | ★★ | ★★★ | ★★ | ★★★★★ | ★★ | ★★★ | ★★★ | Smooth |
| War Mage | ★ | ★★★★★ | ★★★★★ | ★ | ★★★★ | ★★★ | ★★★★★ | Explosive |
| Extractor | ★★ | ★★★ | ★★★ | ★★★ | ★★★★★ | ★★★★★ | ★★★ | Strategic |
| Hybrid | ★★★ | ★★★ | ★★★ | ★ | ★★★ | ★★★ | ★★★★ | Flexible |
| Blade Dancer | ★ | ★★★★★ | ★ | ★★★★★ | ★ | ★ | ★★★★★ | Thrilling |

### PROGRESSION ARC BY BUILD

**Early runs (level 1-5, Scuffed/Serviceable gear):**
- Knight: Easiest. Survives everything, extracts T3 casually.
- Ranger: Safe but slow. Needs END investment.
- War Mage: Melts waves 1-4, dies on wave 5. Portal out with good loot.
- Extractor: Pushes to wave 5, portals with 8 items. Accumulates fastest.
- Hybrid: Steady, unremarkable.
- Blade Dancer: Thrilling W1-5, dies on W6-8. Gambler's build.

**Mid runs (level 10-20, Quality/Superior gear):**
- Knight: Plateaus. Needs QUICK investment to push past wave 7.
- Ranger: Volley spec starts dominating. Consistent wave 7-8 clears.
- War Mage: With END investment, can survive to wave 8. Lightning chains devastate.
- Extractor: Has a vault FULL of Superior gear. Starts equipping and pushing further.
- Hybrid: Specs one combat skill. Becomes a specialist.
- Blade Dancer: Trains Item Magic from milestones. Can finally portal. Starts extracting.

**Late runs (level 30+, Exquisite/Atlan gear):**
- Knight: Full clear viable with gear carrying the speed weakness.
- Ranger: Full clear. Homing Volley is absurd with high Missile skill.
- War Mage: Full clear. Three elemental weapons at max power = everything dies.
- Extractor: Vault is overflowing. Starts equipping Exquisite gear on new builds.
- Hybrid: Became one of the other archetypes but with more flexibility.
- Blade Dancer: The wave 10 clear gods. If they extract, they bring home Atlan.

---

## 10. IMPLEMENTATION PRIORITIES

### Phase 1 ✅ (Complete)
- [x] Character creation (attrs, skills, heritage)
- [x] Wave combat system
- [x] Gear drops & equipment
- [x] Vault & hub screen
- [x] Portal extraction with Item Magic
- [x] Persistent storage
- [x] Healing skill

### Phase 2 ✅ (Complete)
- [x] Balance pass: 200 attr points, 32 skill credits
- [x] AC-faithful XP spending on attributes AND skills (escalating cost, spec=half)
- [x] TAB menu for mid-run XP spending
- [x] 6 AC-faithful loot tiers mapped to waves
- [x] Skill credit milestones at level thresholds
- [x] **Spec mechanic: Heavy Weapons — cleave (hits pass through)**
- [x] **Spec mechanic: Light Weapons — dash strike every 5th hit**
- [x] **Spec mechanic: Missile Weapons — homing arrows + Volley burst**
- [x] **Spec mechanic: War Magic — unlocks Lightning Arc as 3rd weapon**
- [x] **Spec mechanic: Healing — absorb shield (blocks next hit)**
- [x] **Spec mechanic: Item Magic — 8 backpack slots + gravity well on portal**
- [x] **Spec mechanic: Melee Defense — counterattack shockwave on evade, 40% cap**
- [x] **Spec mechanic: Run — auto-sprint at <25% HP + contact damage**
- [x] **Spec mechanic: Alchemy — 2 potions on wave clear + per-kill potion drops**
- [x] Skill-based weapon scaling (blade count, arrow count from effective skill value)
- [x] Potion system (HP, Damage buff, Speed buff)
- [x] Backpack size from Item Magic (4/6/8)
- [x] Weapon types granted by trained skills (not hardcoded)
- [x] Heritage mastery damage bonus validated per build

### Phase 3 — In Progress
- [x] **Boss enemies — one named boss per wave with unique mechanics**
- [ ] Gear sets (Covenant, Celdon, Amuli, Olthoi — 3pc/5pc bonuses)
- [ ] Salvage & tinkering (break down gear, upgrade with materials)
- [ ] Ascension system (NG+ difficulty tiers with better loot)

---

## CHANGELOG

- **v0.1** — Initial prototype: 3 preset characters, card-based level-up
- **v0.2** — AC gear drop system, rarity tiers, auto-equip
- **v0.3** — Full character creation (6 attrs, 22 skills, 5 heritages)
- **v0.4** — AC-faithful attributes (200 pts), skills (32 cr), formulas
- **v0.5** — Wave system, rest phases, rebalanced combat
- **v0.6** — Extraction loop: Vault, hub, persistent storage, risk/reward
- **v0.7** — Portal extraction via Item Magic skill, interrupt-on-hit
- **v0.8** — Continuous waves (no rest phase), enemy counters, Healing rename
- **v1.1** — BOSS SYSTEM
  - One named boss per wave with AC-lore names
  - Bosses are 2.5-3.5x larger with pulsing colored aura
  - Gold-bordered health bar + name tag above boss
  - 5-15x HP of base enemy type, 5-8x XP reward
  - Guaranteed loot drop one tier above wave normal
  - Bael'Zharon (wave 10) has 30% chance to drop T6 Atlan
  - 10 unique boss mechanics:
    W1 Bloody Bones: Enrage (2x speed below 50% HP)
    W2 The Whisperer: Blink (teleports every 3s)
    W3 Grunter the Brute: War Cry (speeds nearby enemies on hit)
    W4 Brood Mother: Spawn Eggs (2 mini Olthoi every 5s)
    W5 Martine the Mad: Phase Shift (immune 2s every 6s)
    W6 Torgluuk: Ground Pound (AoE damage every 4s)
    W7 The Hollow One: Mirror Images (spawns 2 decoys)
    W8 Pandemonium: Frenzy Aura (nearby enemies +50% speed)
    W9 Olthoi Eviscerator: Charge (lunges at 3x speed)
    W10 Bael'Zharon: Dark Nova (expanding damage ring) + Regenerates 0.1%/sec
  - Boss death: large particle explosion, "[name] SLAIN!" text
  - Phase immune bosses show IMMUNE on damage attempts + flicker visual
  - Bosses hit harder than normal enemies
  - Every skill now has a unique specialization mechanic
  - Heavy Weapons spec: cleave (damage passes through to enemies behind target)
  - Light Weapons spec: dash strike every 5th hit (lunge + 2x damage line)
  - Missile Weapons spec: homing arrows + Volley burst (8 arrows every 8 sec)
  - War Magic spec: unlocks Lightning Arc as 3rd simultaneous weapon
  - Healing spec: absorb shield (blocks next incoming hit, refreshes each pulse)
  - Item Magic spec: 8 backpack slots + gravity well pulls loot on portal
  - Melee Defense spec: counterattack shockwave on evade + 40% evade cap
  - Run spec: auto-sprint at <25% HP (3 sec, 15 sec CD) + contact damage
  - Alchemy: trained = potion on wave clear, spec = 2 potions + per-kill drops
  - Potion system: HP potions (20% heal), Damage potions (+25% 10 sec), Speed potions
  - Weapons now granted by trained skills (heavy→axe, light→blade, missile→arrows, war_magic→fire+frost, healing→heal pulse)
  - Blade/arrow/bolt count scales with effective skill value (not gear)
  - Backpack size: untrained=4, trained=6, specialized=8
  - Portal cast time scales with effective Item Magic skill value
  - Damage formula uses effective skill value with proper AC-style scaling
