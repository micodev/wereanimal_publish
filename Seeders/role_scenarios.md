# Role Interaction Scenarios (Engine Accurate)

> Priority order (low = first): Rhino(0) → Sheep(0) → Possum(0) → Cursed(0) → Serpent(1) → Witch(5) → Ghost Dog(6) → Chemist(7) → King(10) → Disguised(10) → Alpha(11) → Snake(12) → Vulture(15) → Raccoon(16) → Owl(20) → Fledgling(21)

---

## 🚨 CRITICAL ENGINE BEHAVIORS 🚨
Before looking at specific roles, understand these core engine rules that govern all interactions:

1. **Death Interrupts Actions**: If a player is killed by a faster action (e.g., Witch at Priority 5), their pending night action (e.g., King at Priority 10) is **skipped**. They die before they can act.
2. **Rhino's Blind Vengeance**: The Rhino does **NOT** automatically retaliate against their attacker. The Rhino retaliates against whoever the Rhino selected as their `NightTargetId`. If they guess wrong, they kill an innocent player instead of the attacker!
3. **Rhino's Death Hook Bypass**: When the Rhino retaliates, the engine directly sets the target's `IsAlive = false` without calling the `KillPlayer` method. This means **Rhino victims do NOT trigger death hooks**. (e.g., If Rhino kills the Serpent, the servants do NOT cascade die!)
4. **Cascade Orphans**: The Serpent's cascade death only triggers if a player dies *while holding the `hypnotic_serpent` role*. If their role is changed before they die (e.g., Alpha infection, Raccoon steal), the cascade is permanently bypassed and the servants live on as orphaned Solitaries.

---

## 1. Kill vs Protection Matrix

| Attacker | Target | Witch Heal? | Ghost Dog? | Result |
|---|---|---|---|---|
| King of Lions | Common Sheep | ❌ | ❌ | **Sheep dies** |
| King of Lions | Common Sheep | ✅ | ❌ | **Sheep lives** (Witch potion consumed) |
| King of Lions | Common Sheep | ❌ | ✅ | **Sheep lives** (Ghost protects) |
| King of Lions | Common Sheep | ✅ | ✅ | **Sheep lives** (both wasted, stacks don't matter) |
| Disguised Lion | Common Sheep | ❌ | ❌ | **Sheep dies** |
| Witch Kill Potion | Common Sheep | ✅ (same target) | ❌ | **Sheep dies** — Kill Potion pierces own protection |
| Witch Kill Potion | Common Sheep | ❌ | ✅ | **Sheep dies** — Kill Potion bypasses ALL protection |
| Serpent Proxy Kill | Common Sheep | ❌ | ✅ | **Sheep lives** (Ghost protects proxy kill) |
| Chemist Toad | Common Sheep | — | — | **50/50** — either Chemist or Sheep dies, protection irrelevant |

---

## 2. Kill vs Special Targets (The Rhino Factor)

| Attacker | Target | Rhino's `NightTargetId` | Result |
|---|---|---|---|
| King of Lions | **Rhino Hunter** | King of Lions | **Both die** (Rhino guessed right) |
| King of Lions | **Rhino Hunter** | Common Sheep | **Rhino and Sheep die!** King lives. (Rhino guessed wrong) |
| Serpent Proxy | **Rhino Hunter** | Servant (Proxy) | **Rhino and Servant die** |
| Witch Kill Potion | **Rhino Hunter** | Witch | **Both die** (Witch is 5, Rhino dies before acting but Death Hook still fires) |

| Attacker | Target | Result |
|---|---|---|
| King of Lions | **Cursed Sheep** | **Cursed transforms → King of Lions** (joins Apex!) |
| Disguised Lion | **Cursed Sheep** | **Cursed transforms → King of Lions** |
| Witch Kill Potion | **Cursed Sheep** | **Cursed dies** (Witch is Herd, curse only triggers on Apex) |
| King of Lions | **Ghostly Dog** | **Dog dies** but continues protecting from grave |
| King of Lions | **Playing Possum** | **Possum dies** (night kill ≠ lynch, no win) |

---

## 3. Alpha Lion Infection Scenarios

Infection simply overwrites the target's role. It does not trigger `KillPlayer`.

| Alpha Infects | Protection? | Result |
|---|---|---|
| Common Sheep | ❌ | **Sheep → King of Lions** (joins Apex) |
| Common Sheep | Witch Heal | **Infection blocked** — target protected |
| Cursed Sheep | ❌ | **Cursed → King of Lions** — infection overrides curse |
| Hypnotic Serpent | ❌ | **Serpent → King of Lions**. 🚨 **Servants DO NOT DIE**. Since Serpent wasn't killed, no cascade. Servants are orphaned. |
| Playing Possum | ❌ | **Possum → King of Lions** — loses SelfWinOnLynch |

---

## 4. Investigation Results Matrix

| Investigator | Target | Result Seen |
|---|---|---|
| Wise Owl | Common Sheep | **Herd** ✅ |
| Wise Owl | King of Lions | **Apex** ✅ |
| Wise Owl | **Alpha Lion** | **Herd** ❗ (disguised) |
| Wise Owl | **Disguised Lion** | **Herd** ❗ (disguised) |
| Wise Owl | **Hypnotic Serpent** | ☠️ **Owl dies instantly** (Lethal gaze) |
| Apex Vulture | Alpha Lion / Disguised | **Herd** ❗ (Disguise fools own team) |
| Apex Vulture | **Hypnotic Serpent** | ☠️ **Vulture dies instantly** |
| Fledgling Owl | Alpha Lion vs King | **Different** (Herd vs Apex) — Disguise works on Fledgling |
| Fledgling Owl | Serpent vs Sheep | **Different** — And Fledgling **SURVIVES** (CompareFactions bypasses lethal gaze) |

---

## 5. Witch Dual-Action Scenarios

| Priority 5 | Priority 10 | Result |
|---|---|---|
| Witch kills King | King targeted Sheep | **King dies first**. King's action is SKIPPED. Sheep lives. |
| Witch heals Sheep | King targeted Sheep | **Sheep lives** (protected). |
| Witch heals King | King targeted Sheep | King protected, Sheep dies. |
| Witch kills Serpent | — | **Serpent dies → Servants cascade die**. |

---

## 6. Hypnotic Serpent Deep Scenarios

| Scenario | Result |
|---|---|
| Serpent dominates Sheep (Herd) | **Fails** — can only dominate Apex faction |
| Serpent dominates Alpha / Disguised | **Success** — They are Apex faction internally |
| Serpent dies to King / Witch / Poison | **ALL servants cascade die instantly** |
| Serpent dies to Rhino Retaliation | 🚨 **Servants SURVIVE**. Rhino bypasses `KillPlayer`, breaking the cascade. |

---

## 7. Lovers Interaction Matrix (Matchmaking Dove)

| Scenario | Result |
|---|---|
| Lover A is killed by Wolf | Lover A dies. **Lover B instantly dies of grief.** |
| Lover A is attacked but Protected | Both survive. No grief death triggered. |
| Lover A is lynched | Lover A dies. **Lover B instantly dies of grief.** |
| Lover A (Rhino) is killed | Lover A dies. Lover B dies of grief. **Lover A still retaliates.** |
| Lover A (Serpent) dies | Lover A dies. Servants cascade die. **Lover B dies of grief.** |
| **Only Lover A & Lover B remain** | **Lovers Win!** (Overrides all other faction win conditions) |
| Dove doesn't pick in time | The game **randomly assigns 2 alive players as Lovers**. |
| Night 2 arrives | Dove **does not get a prompt** (Ability is one-time use). |
| Serpent infected by Alpha | 🚨 **Servants SURVIVE**. Role changed, not killed. |
| Raccoon steals Serpent role | 🚨 **Servants SURVIVE**. Role changed, not killed. |

---

## 7. Venomous Snake Poison Timeline

| Night | Event | Result |
|---|---|---|
| N | Snake (12) bites target | Target gets `PoisonTurnsLeft = 2` |
| N+1 | Upkeep Phase | Drops to 1 |
| N+2 | Upkeep Phase | Drops to 0 → **Target dies** |
| Any | Witch heals poisoned target | **Poison cured instantly** |

*Note: If a player dies to poison on N+2, it triggers `KillPlayer`. If it's the Rhino, they will retaliate against whoever their `NightTargetId` is on N+2!*

---

## 8. Ghostly Dog Edge Cases

| Scenario | Result |
|---|---|
| Dog dead → protects Sheep | Works, Dog keeps power |
| Dog dead → protects King / Alpha / Disguised | Works, but **Dog loses power forever** (Target is Apex) |
| Dog dead → protects Serpent | Works, Dog keeps power (Serpent is Solitaries, not Apex) |

---

## 9. Sneaky Raccoon Steal Scenarios

| Target | Result (Standard Mode) |
|---|---|
| Wise Owl | **Raccoon becomes Owl**, target becomes Common Sheep |
| King of Lions | **Raccoon becomes King of Lions** (joins Apex!), target becomes Sheep |
| Hypnotic Serpent | **Raccoon becomes Serpent**. Old Serpent becomes Sheep. 🚨 **Servants are orphaned** and stay linked to old player ID. |
| Dead player (killed by Apex) | **Raccoon dies** — stumbled into Apex kill scene |
| Poisoned player (dying tonight) | **Steal fails** — target dying |

---

## 10. Crazy Chemist Toad Interactions

| Scenario | Result |
|---|---|
| Chemist gambles with target | **50/50** chance: Chemist dies OR Target dies |
| Target is Cursed Sheep | 50% Cursed dies (Chemist is Herd, no transform) |
| Target is protected (Witch/Ghost) | **Protection ignored** |

---

## 11. Day Phase Edge Cases

| Scenario | Result |
|---|---|
| Majority votes Possum | **Possum wins instantly** |
| All votes tied or Skip | **No lynch** |
| Player AFK 2+ days | **Player auto-eliminated** (fled) |
| Lynch Serpent | **Serpent dies → servants cascade die** |
| Lynch Cursed Sheep / Rhino | **Dies normally** (abilities are night-only) |

---

## 12. Multi-Role Chain Reactions (Engine Proven)

**Chain A: The Disconnected Vengeance**
King kills Rhino. Rhino had randomly targeted the Witch. Result: King lives, Rhino dies, Witch dies.

**Chain B: The Broken Cascade**
Alpha infects Serpent. Owl investigates the (now King) Serpent. Result: Serpent becomes King. Servants are orphaned. Owl sees King as "Apex" and lives (lethal gaze removed).

**Chain C: Witch's Preemptive Strike**
Witch (5) throws Kill potion at Alpha Lion (11). Alpha was going to infect the Possum. Result: Alpha dies before priority 11. Possum stays Possum.

**Chain D: The False Retaliation**
Snake poisons Rhino. Night 2: Rhino targets a Sheep. Upkeep phase: Poison kills Rhino. Result: Rhino retaliates against the Sheep! The Snake gets away.

**Chain E: Raccoon's Empty Empire**
Raccoon steals Serpent. Old Serpent becomes Sheep. Servants stay tied to the Sheep (orphaned). If Raccoon dominates someone new, Raccoon gets a servant. If Raccoon dies, only the NEW servant dies. If old Serpent (Sheep) dies, no one dies.

---

---

# ⚠️ NOT COVERED SCENARIOS (Open Loops & Engine Gaps)

These scenarios exist in real game play but are **absent from the sections above** because the current engine either handles them silently, produces surprising results, or has no explicit documentation of the outcome. Each entry is traced directly to the engine source code.

---

## NC-1. AFK Flee Does NOT Trigger Death Hooks

**Code reference**: `ResolutionEngine.cs` lines 103–123 (night AFK) and 446–462 (day AFK). Both branches set `p.IsAlive = false` directly — **without calling `KillPlayer`**.

| Scenario | Result |
|---|---|
| AFK Rhino flees (2nd missed night action) | `IsAlive = false` set directly. **Rhino's retaliation death hook NEVER fires**. `NightTargetId` is ignored. |
| AFK Serpent flees | `IsAlive = false` set directly. **Cascade does NOT fire**. All servants become permanently orphaned. |
| AFK Common Sheep / Owl / etc. flees | No death hook exists for these roles, so the result is identical to a normal death. |
| AFK player flees on day 1 (only missed 1 vote) | `MissedVotes++` only. Player stays alive. No penalty until 2nd consecutive miss. |

> [!WARNING]
> This is a **silent open loop**: an AFK Serpent with dominated servants loses their cascade on flee, permanently blocking the servant's death — servants survive as orphans with no master.

---

## NC-2. Poison Cure Timing Is Conditional (Not Instant on Night Bitten)

**Code reference**: `ResolutionEngine.cs` lines 421–437. The cure check is:
```csharp
if (player.IsProtected && player.PoisonTurnsLeft < 2)
```
The key is `< 2`. Witch heals on priority 5; Snake bites on priority 12. So the protection and poison application both happen in the same resolution pass before upkeep.

| Scenario | Result |
|---|---|
| Snake bites target on Night N. Witch heals same target same night. | Upkeep: `PoisonTurnsLeft == 2`. Condition `< 2` is **FALSE**. Poison is NOT cured. Target will die on Night N+2 unless healed again. |
| Snake bites target on Night N. Witch heals same target on Night N+1. | Upkeep on N+1: `PoisonTurnsLeft == 1`, `IsProtected == true`. Condition `< 2` is **TRUE**. Poison IS cured. |

> [!CAUTION]
> Healing a poisoned player on the **same night they are bitten does not cure them**. The Witch must heal again on a subsequent night.

---

## NC-3. Snake Re-Bite Resets the Timer (No Stack)

**Code reference**: `ResolutionEngine.cs` line 311: `target.PoisonTurnsLeft = 2;` — unconditional assignment, not additive.

| Scenario | Result |
|---|---|
| Snake bites target on Night N (2 turns left). Snake bites the **same target** on Night N+1 (cooldown resets to 2, so PoisonCooldown is back to 0 on N+1? No — cooldown is 2.) | Snake's `PoisonCooldown` is set to 2 on Night N. It ticks down on upkeep: 2→1→0. Snake can bite again on Night N+2. If they re-bite, `PoisonTurnsLeft` resets to 2 — which **extends the victim's death by 2 more nights**. |
| Snake bites a target who is about to die from prior poison (`PoisonTurnsLeft == 1`). | `PoisonTurnsLeft` is set back to `2`. Death is **delayed by 2 more nights**. |

---

## NC-4. Raccoon Steal Ignores Protection (`IsProtected`)

**Code reference**: `ResolutionEngine.cs` lines 355–405. The `StealRole` case has **no `target.IsProtected` check** — unlike `Kill` (line 171) and `Infect` (line 239).

| Scenario | Result |
|---|---|
| Raccoon targets Witch-healed Sheep | **Steal succeeds**. Protection is completely ignored. Raccoon becomes Sheep's role. Witch potion is consumed for nothing (King didn't attack). |
| Raccoon targets Ghost Dog-protected Sheep | **Steal succeeds**. Ghost Dog protection does not block theft. |
| Raccoon targets Alpha-infected target (who becomes King mid-night at priority 11, before Raccoon at priority 16) | Raccoon steals the **King of Lions** role (the new role after infection). |

> [!IMPORTANT]
> Protection **does not block** Raccoon's steal. Only death or dying-to-poison blocks it.

---

## NC-5. Raccoon Targeting a Player Killed by Non-Apex (Witch, Chemist, Poison)

**Code reference**: `ResolutionEngine.cs` lines 362–374. The "did Apex kill them?" check looks for event key `engine_night_kill`. Witch uses `engine_night_witch_kill`. Chemist uses `engine_night_chemist_gamble_target`. Poison uses `engine_night_delayed_poison`.

| Scenario | Result |
|---|---|
| Raccoon targets a player killed by **Witch Kill Potion** that same night | `killedByApex == false`. **Raccoon does NOT die**. Steal silently fails with `engine_night_steal_failed`. |
| Raccoon targets a player killed by **Chemist** that same night | Same as above — Raccoon lives, steal fails silently. |
| Raccoon targets a player dying from **poison upkeep** that same night | `dyingToPoison` check is evaluated at the **start** of the steal case. If `PoisonTurnsLeft <= 1` and not protected, steal fails (no death to Raccoon). |
| Raccoon targets a player killed by **King of Lions** that same night | `killedByApex == true`. **Raccoon dies** — stumbled into Apex kill scene. |

---

## NC-6. Raccoon in ThiefFullMode (Unrestricted Steal)

**Code reference**: `ResolutionEngine.cs` lines 378–404. `session.ThiefFullMode` enables a separate branch where Raccoon can steal repeatedly, has a 50% random failure, and gives the **old Raccoon role** to the victim (not Common Sheep).

| Scenario | Result (ThiefFullMode) |
|---|---|
| Raccoon attempts steal | **50% fail** — `engine_night_steal_failed`. Raccoon keeps their current role. |
| Raccoon succeeds steal | Raccoon gets target's role. **Target gets `sneaky_raccoon` role** (not Common Sheep). `HasUsedThiefAbility` is NOT set — Raccoon can steal again next night. |
| Raccoon steals Serpent (ThiefFullMode) | Raccoon becomes Serpent. Old Serpent gets `sneaky_raccoon` role. Servants are orphaned from old player. |
| Raccoon succeeds multiple nights | Each success changes Raccoon's role again. No limit. |

> [!NOTE]
> In Standard Mode, a successful steal sets `HasUsedThiefAbility = true`, permanently disabling future steals. In ThiefFullMode, this flag is never set.

---

## NC-7. Chemist Killing Roles With Death Hooks

**Code reference**: `ResolutionEngine.cs` lines 332–353. Both Chemist death outcomes call `KillPlayer`, which triggers all death hooks normally.

| Scenario | Result |
|---|---|
| Chemist gambles, **Chemist dies** (50%) | `KillPlayer(actor)` fires. If actor is Rhino (impossible — Chemist is `crazy_chemist_toad`, not Rhino), hook fires. No hook for Chemist itself. |
| Chemist gambles, **Rhino dies** (50%) | `KillPlayer(rhino)` fires. **Rhino's death hook triggers** → Rhino retaliates against their `NightTargetId`. Chemist may have indirectly killed a third player. |
| Chemist gambles, **Serpent dies** (50%) | `KillPlayer(serpent)` fires. **Cascade triggers** — all servants die. |
| Chemist gambles, **Cursed Sheep is the target** | `KillPlayer(cursedSheep)` — Chemist is Herd, NOT Apex. The transform check (`actor.AssignedRole?.Faction == Faction.Apex`) is in the `Kill` case only. Chemist goes through `ChemistGamble` case. **Cursed Sheep dies normally**, no transformation. |

---

## NC-8. Ghostly Dog Protecting Solitaries (Possum / Serpent / Orphaned Servants)

**Code reference**: `ResolutionEngine.cs` lines 317–330. Power loss only triggers if `target.AssignedRole?.Faction == Faction.Apex`. Solitaries (`hypnotic_serpent`, `playing_possum`, orphaned servants) are **not Apex**.

| Scenario | Result |
|---|---|
| Dead Dog protects **Playing Possum** | Protection applied. Dog **keeps power**. Possum survives night kill. |
| Dead Dog protects **Hypnotic Serpent** | Protection applied. Dog **keeps power**. Serpent survives night kill, servants do not cascade. |
| Dead Dog protects **orphaned servant** (Solitaries faction) | Protection applied. Dog **keeps power**. |

---

## NC-9. Win Condition — Orphaned Servants Blocking Herd/Apex Victory

**Code reference**: `GameSession.cs` lines 211–242. `dominatedServants` counts all players with `MasterId != null`, including those whose Serpent is dead.

The Serpent win condition only evaluates when `aliveSerpents > 0`. So orphaned servants (Serpent dead) do **not** help Solitaries win. However, they count toward total alive players in the standard checks:

| Scenario | Result |
|---|---|
| Serpent dead. 1 Apex alive. 1 Herd alive. 2 orphaned servants alive. | `aliveApex=1`, `aliveHerd=1`. Check: `aliveApex >= aliveHerd` → `1 >= 1` → **Apex wins**. Orphans are alive but belong to Herd or Solitaries faction. They count in `aliveHerd` if their role faction is Herd (dominated Apex players had faction set to Solitaries during domination). |
| Serpent dead. 0 Apex. 1 Herd. 2 orphaned servants (Solitaries faction). | `aliveApex=0`, `aliveHerd=1`, `aliveSerpents=0`. → **Herd wins** (first check: `aliveApex==0`). Orphans are Solitaries faction, counted in neither Herd nor Apex — they lose. |

> [!IMPORTANT]
> Orphaned servants do **not** count toward Serpent's win condition after the Serpent dies. They become faction "Solitaries" and effectively have no winning team — they lose regardless.

---

## NC-10. Raccoon Steals Serpent → Raccoon Becomes the New Serpent (Win Condition Impact)

**Code reference**: `ResolutionEngine.cs` lines 387–404, `GameSession.cs` line 216.

After a successful steal, `actor.AssignedRole = stolenRole` — the Raccoon's role object IS the Serpent role, with `Id == "hypnotic_serpent"`. The win condition counts `p.AssignedRole?.Id == "hypnotic_serpent"`.

| Scenario | Result |
|---|---|
| Raccoon steals Serpent role | `aliveSerpents` now counts Raccoon. **Raccoon can win as Serpent**. Raccoon inherits the `Dominate` ability and can dominate Apex players. |
| Raccoon (now Serpent) dominates an Apex player | Raccoon's new servant has `MasterId = Raccoon.Id`. If Raccoon dies via `KillPlayer`, cascade fires — servant dies. |
| Old Serpent (now Common Sheep) dies | No cascade (role is no longer `hypnotic_serpent`). Old servants (MasterId = old Serpent ID) are orphaned from the old ID. |
| Raccoon (new Serpent) investigated by Wise Owl | **Owl dies instantly** — lethal gaze is based on `target.AssignedRole?.Id == "hypnotic_serpent"`, not the original player. |

---

## NC-11. Serpent Domination — What Happens to Servants' Own Night Actions

**Code reference**: `ResolutionEngine.cs` lines 259–303. After domination, the servant's role is cloned with `Faction = Solitaries` but retains their original `AbilityType` and `PriorityLevel`. Their own `NightTargetId` is unaffected.

| Scenario | Result |
|---|---|
| Dominated Wise Owl still submits their own investigation target | **Owl's investigate still fires** (AbilityType is unchanged). Owl investigates their chosen target AND is used as Serpent's proxy kill target. Two independent actions. |
| Dominated King of Lions submits a kill target | **King's kill still fires** — King kills their chosen target. Serpent also uses the servant as a proxy kill weapon on a different target. The King acts twice: once for themselves, once as Serpent's instrument. |
| Dominated Alpha Lion submits an infect target | **Alpha infects their target**. Alpha is now Solitaries faction — the infected player becomes King of Lions (Apex). |

> [!CAUTION]
> **This is a massive double-action exploit gap**: a dominated Apex player retains full use of their ability AND serves as Serpent's proxy killer. The engine has no restriction preventing this.

---

## NC-12. Tie Vote With Possum as One of the Tied Players

**Code reference**: `ResolutionEngine.cs` lines 472–513. `isTie == true` → skip resolution entirely, `engine_tie_vote` is emitted. No player is eliminated.

| Scenario | Result |
|---|---|
| Possum and Sheep are tied in votes | **No lynch**. Possum does NOT win. `engine_tie_vote` fires. Day ends without elimination. |
| Possum is the sole top vote but has exactly majority | **Possum wins** — `engine_day_possum_win` fires, game over. |
| All living players vote Skip (`Guid.Empty`) | `topVote.TargetId == Guid.Empty` → `engine_day_skip`. No elimination. |
| Only one player votes, rest are AFK (not yet fled) | If no tie, that single vote determines lynch. Possum with one vote pointing at them wins. |

---

## NC-13. Ghost Dog Cannot Act While Alive

**Code reference**: `GameSession.cs` lines 100–101:
```csharp
if (sourcePlayer.IsAlive && sourcePlayer.AssignedRole?.AbilityType == AbilityType.GhostlyProtect)
    throw new InvalidOperationException("Ghostly Protector cannot act while alive.");
```

| Scenario | Result |
|---|---|
| Living Ghost Dog tries to submit a night action | **InvalidOperationException thrown** — action rejected. Dog must be dead to protect. |
| Ghost Dog is killed Night N | Dog can start protecting from Night N+1 onward (dead at start of next night). |
| Ghost Dog targeted by Raccoon steal (Dog is alive) | Raccoon steals `GhostlyProtect` ability. Raccoon is now `ghostly_dog`. **If Raccoon is alive, they also cannot use the ability** — same `IsAlive` check applies to Raccoon. |

---

## NC-14. `RequiresNightAction` — Roles Silently Exempt From AFK Tracking

**Code reference**: `ResolutionEngine.cs` lines 86–96. AFK penalty only applies to players for whom `RequiresNightAction` returns `true`. The following are silently exempt:

| Role / Condition | AFK Tracked? | Reason |
|---|---|---|
| Common Sheep (`AbilityType.None`) | ❌ No | `None` ability returns false |
| Cursed Sheep (`AbilityType.None`) | ❌ No | Same |
| Rhino Hunter (`AbilityType.RetaliateKill`) | ❌ No | Passive — no night action |
| Playing Possum (`AbilityType.SelfWinOnLynch`) | ❌ No | Passive — no night action |
| Ghost Dog after `LostPower = true` | ❌ No | `LostPower` check at line 103 rejects submission |
| Raccoon after 1 successful steal (Standard Mode) | ❌ No | `HasUsedThiefAbility == true` → `RequiresNightAction` returns false |
| Witch with both potions used | ❌ No | `!HasProtectPotion && !HasKillPotion` returns false |
| Snake while `PoisonCooldown > 0` | ❌ No | Cooldown check returns false |
| Chemist while `ChemistCooldown > 0` | ❌ No | Cooldown check returns false |

---

## NC-15. Raccoon Steals Ghost Dog With `LostPower = true`

**Code reference**: `GameSession.cs` lines 103–104. If `sourcePlayer.LostPower == true`, the action is rejected. But there's no check when Raccoon steals from a Dog that has already `LostPower = true`.

| Scenario | Result |
|---|---|
| Ghost Dog protects Apex → Dog's `LostPower = true`. Later, Raccoon steals the `ghostly_dog` role. | **Raccoon can use the Ghost Dog ability**. `LostPower` is a player-level field on the original Dog, not on the role. Raccoon's `LostPower` remains `false`, effectively resurrecting a dead power. |

> [!WARNING]
> This is a silent open loop that allows the Raccoon to resurrect an exhausted Ghostly Protector ability.

---

## NC-16. Serpent's Servant Killed Before Proxy Kill Resolves

**Code reference**: `ResolutionEngine.cs` line 263: `existingServant = session.Players.FirstOrDefault(p => p.IsAlive && p.MasterId == actor.Id)`.

| Scenario | Result |
|---|---|
| Rhino (Priority 0) retaliates and kills the Servant. Serpent (Priority 1) tries to use the Servant for a proxy kill. | Servant is dead (`IsAlive = false`). `existingServant == null`. **Serpent falls into the domination branch** and tries to dominate their proxy kill target instead. |

> [!CAUTION]
> If a servant dies at Priority 0, the Serpent's intended proxy kill silently converts into a domination attempt on the target.

---

## NC-17. `TriggerGameOver` Kills Herd Without Triggering Death Hooks

**Code reference**: `GameSession.cs` lines 192–199. When Apex or Solitaries win, `TriggerGameOver` iterates over all alive Herd players and sets `IsAlive = false` directly without calling `KillPlayer`.

| Scenario | Result |
|---|---|
| Apex wins the game. Rhino (Herd) is still alive. | `IsAlive = false` set directly on Rhino. **No retaliation fires**. |

---

## NC-18. Win Condition Math — Orphaned Servants and Raccoon-Serpent

**Code reference**: `GameSession.cs` lines 220–228. The win formula is `serpentTeamTotal = aliveSerpents + dominatedServants`.

| Scenario | Result |
|---|---|
| Raccoon steals Serpent. Raccoon dominates a servant. Old Serpent (now Common Sheep) also had an orphaned servant. | `aliveSerpents = 1` (Raccoon). `dominatedServants = 2` (both have `MasterId != null`). Both the new servant and the old orphaned servant count towards the new Serpent's win condition. |

---

## NC-19. Serpent Domination on a Poisoned Target

**Code reference**: `ResolutionEngine.cs` lines 260–303. No check for `target.PoisonTurnsLeft` during domination.

| Scenario | Result |
|---|---|
| Serpent dominates a player who has 1 poison turn left. | Domination succeeds. Next upkeep, the servant dies to poison. `KillPlayer(servant)` fires. **Cascade does not fire** (because the servant is not the Serpent). Serpent silently loses the servant but stays alive. |

---

## NC-20. Alpha Infects a Protected Cursed Sheep

**Code reference**: `ResolutionEngine.cs` lines 239–256. `IsProtected` blocks infection completely.

| Scenario | Result |
|---|---|
| Alpha targets Cursed Sheep. Witch heals Cursed Sheep. | `IsProtected = true`. **Infection blocked**. The Cursed Sheep does NOT transform into King of Lions. (Unlike the Kill action which transforms Cursed Sheep regardless of normal kill flow). |

---

## NC-21. Witch Self-Targeting

**Code reference**: `ResolutionEngine.cs` lines 147–167. No restriction on `target == actor.Id`.

| Scenario | Result |
|---|---|
| Witch heals herself | Works normally. Witch gains `IsProtected = true`. |
| Witch throws kill potion at herself | `KillPlayer(witch)` fires. Witch dies to her own potion. |

---

## NC-22. Fledgling Owl Comparing After Mid-Night Role Change

**Code reference**: `ResolutionEngine.cs` lines 223–235. `GetApparentFaction` is evaluated at resolution time (Priority 21).

| Scenario | Result |
|---|---|
| Alpha (11) infects Sheep (Herd) → becomes King (Apex). Fledgling (21) compares the infected Sheep and the original King. | At priority 21, the Sheep is already Apex. Fledgling sees "Same Faction" (`True`). The mid-night role change alters the result. |

---

## NC-23. Bots Are Exempt From AFK Tracking

**Code reference**: `ResolutionEngine.cs` line 103 and `GameSession.cs` line 446.

| Scenario | Result |
|---|---|
| Bot misses a vote or night action | Bots never accumulate `MissedNightActions` or `MissedVotes`, and never flee. |
