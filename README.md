# Arena XP Gain Modifications for Mount & Blade II Bannerlord

## Overview

This modification changes the experience points (XP) gain in the Arena for the game. Normally, players gain 0.0625f% XP during practice fights and 0.33f% XP during tournaments. This modification increases these values to 0.50f% for both types of Arena combat, allowing players to earn more XP.

## Changes Made

1. **Original XP Values:**
    - Practice Fights: 0.0625f%
    - Tournaments: 0.33f%

2. **Modified XP Values:**
    - Practice Fights: 0.50f%
    - Tournaments: 0.50f%

## Technical Details

### Decompilation and Code Analysis

The `TaleWorlds.CampaignSystem.dll` was decompiled using dnSpy. The relevant code for XP calculation in the Arena is located in `TaleWorlds.CampaignSystem.GameComponents.DefaultCombatXPModel`.
![](https://github.com/Aardenfell/M-BIIarenaXP/blob/main/files/images/dnySpy1.png)

```csharp
// Token: 0x06001537 RID: 5431 RVA: 0x00061F70 File Offset: 0x00060170
		public override void GetXpFromHit(CharacterObject attackerTroop, CharacterObject captain, CharacterObject attackedTroop, PartyBase party, int damage, bool isFatal, CombatXpModel.MissionTypeEnum missionType, out int xpAmount)
		{
			int num = attackedTroop.MaxHitPoints();
			MilitaryPowerModel militaryPowerModel = Campaign.Current.Models.MilitaryPowerModel;
			float defaultTroopPower = militaryPowerModel.GetDefaultTroopPower(attackedTroop);
			float defaultTroopPower2 = militaryPowerModel.GetDefaultTroopPower(attackerTroop);
			float leaderModifier = 0f;
			float contextModifier = 0f;
			if (((party != null) ? party.MapEvent : null) != null)
			{
				contextModifier = militaryPowerModel.GetContextModifier(attackedTroop, party.Side, party.MapEvent.SimulationContext);
				leaderModifier = party.MapEventSide.LeaderSimulationModifier;
			}
			float troopPower = militaryPowerModel.GetTroopPower(defaultTroopPower, leaderModifier, contextModifier);
			float troopPower2 = militaryPowerModel.GetTroopPower(defaultTroopPower2, leaderModifier, contextModifier);
			float num2 = 0.4f * (troopPower2 + 0.5f) * (troopPower + 0.5f) * (float)(MathF.Min(damage, num) + (isFatal ? num : 0));
			num2 *= ((missionType == CombatXpModel.MissionTypeEnum.NoXp) ? 0f : ((missionType == CombatXpModel.MissionTypeEnum.PracticeFight) ? 0.0625f : ((missionType == CombatXpModel.MissionTypeEnum.Tournament) ? 0.33f : ((missionType == CombatXpModel.MissionTypeEnum.SimulationBattle) ? 0.9f : ((missionType == CombatXpModel.MissionTypeEnum.Battle) ? 1f : 1f)))));
			ExplainedNumber explainedNumber = new ExplainedNumber(num2, false, null);
			if (party != null)
			{
				this.GetBattleXpBonusFromPerks(party, ref explainedNumber, attackerTroop);
			}
			if (captain != null && captain.IsHero && captain.GetPerkValue(DefaultPerks.Leadership.InspiringLeader))
			{
				explainedNumber.AddFactor(DefaultPerks.Leadership.InspiringLeader.SecondaryBonus, DefaultPerks.Leadership.InspiringLeader.Name);
			}
			xpAmount = MathF.Round(explainedNumber.ResultNumber);
		}
```
The relevant code would be:<br/>
`missionType == CombatXpModel.MissionTypeEnum.PracticeFight) ? 0.0625f ` for Practice Fights <br/>
& <br/>
`missionType == CombatXpModel.MissionTypeEnum.Tournament` for Tournaments.<br/>

### Hexadecimal Editing

Using HxD, the following hexadecimal values were identified and modified:

1. **Practice Fights:**
    - Original: `00 00 80 3D` (0.0625f) <br/>
![](https://github.com/Aardenfell/M-BIIarenaXP/blob/main/files/images/practiceBefore.png)
    - Modified: `00 00 00 3F` (0.50f) <br/>
![](https://github.com/Aardenfell/M-BIIarenaXP/blob/main/files/images/practiceAfter.png)

2. **Tournaments:**
    - Original: `C3 F5 A8 3E` (0.33f) <br/>
![](https://github.com/Aardenfell/M-BIIarenaXP/blob/main/files/images/tourneyBefore.png)
    - Modified: `00 00 00 3F` (0.50f) <br/>
![](https://github.com/Aardenfell/M-BIIarenaXP/blob/main/files/images/tourneyAfter.png)

The values can be found after the hex sequence: `2B 21 22 00 00 80 3F 2B 1A 22 66 66 66 3F 2B 13` (use CTRL+F).

### File Modification

After editing the hexadecimal values, the modified `TaleWorlds.CampaignSystem.dll` was saved and replaced in the game's bin folder. It is recommended to keep a copy of the original DLL file as a backup. <br/>
You can also throw the modified dll back into dnSpy to see if the values have changed. <br/>
![](https://github.com/Aardenfell/M-BIIarenaXP/blob/main/files/images/dnySpy3.png)

## Usage

1. **Using the Provided DLL:**
    - Replace the original `TaleWorlds.CampaignSystem.dll` in the game’s bin folder with the provided modified DLL.

2. **Customizing XP Values:**
    - If you wish to use different XP values, substitute the provided edited values with your desired values in HxD and save the DLL.

## Disclaimer

This repository is for personal reference and documentation of the modification process. Make sure to back up original files before making any changes.

## Download

[Virus Total](https://www.virustotal.com/gui/file/ee1400b43930433d58e7e924501d9b9788e6f054b7c7d22e82f35a45c296e188?nocache=1)<br/>
[Release](https://github.com/Aardenfell/M-BIIarenaXP/releases/tag/1)
