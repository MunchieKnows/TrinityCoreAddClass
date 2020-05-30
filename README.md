# TrinityCoreAddClass
How to add a class with features to TrinityCore

Final product: https://www.youtube.com/watch?v=mnOWoI1tVKw

## Table of contents
1. Changes to the core files
   1. [shareddefines.h](https://github.com/MunchieKnows/TrinityCoreAddClass#shareddefinesh)
   2. [objectmgr.cpp](https://github.com/MunchieKnows/TrinityCoreAddClass#objectmgrcpp)
   3. [player.cpp](https://github.com/MunchieKnows/TrinityCoreAddClass#playercpp)
   4. [spell_item.cpp](https://github.com/MunchieKnows/TrinityCoreAddClass#spell_itemcpp)

## Changes to the core files

### shareddefines.h
We add the class so that the core will enable the creation of the class, this are the changes to make:
```
enum Classes
{
    CLASS_NONE          = 0,
    CLASS_WARRIOR       = 1,
    CLASS_PALADIN       = 2,
    CLASS_HUNTER        = 3,
    CLASS_ROGUE         = 4,
    CLASS_PRIEST        = 5,
    CLASS_DEATH_KNIGHT  = 6,
    CLASS_SHAMAN        = 7,
    CLASS_MAGE          = 8,
    CLASS_WARLOCK       = 9,
    //CLASS_UNK           = 10,
    CLASS_DRUID         = 11,
    CLASS_MONK          = 12
};

#define MAX_CLASSES     = 13

#define CLASSMASK_ALL_PLAYABLE 
        ((1<<(CLASS_WARRIOR-1))|(1<<(CLASS_PALADIN-1))| (1<<(CLASS_HUNTER-1)) |
    (1<<(CLASS_ROGUE-1))   |(1<<(CLASS_PRIEST-1)) | (1<<(CLASS_SHAMAN-1)) | 
    (1<<(CLASS_MAGE-1))    |(1<<(CLASS_WARLOCK-1))| (1<<(CLASS_DRUID-1)) |  
    (1<<(CLASS_DEATH_KNIGHT-1)) | (1<<(CLASS_MONK-1)))
    
enum SpellFamilyNames
{
  SPELLFAMILY_DEATHKNIGHT = 15,
  // 16 - unused
  SPELLFAMILY_PET	        = 17,
  SPELLFAMILY_MONK		= 18 
};

enum PlayerSpecializations
{
    SPEC_WARRIOR_ARMS = 0,
    SPEC_WARRIOR_FURY = 1,
    SPEC_WARRIOR_PROTECTION = 2,
    SPEC_PALADIN_HOLY = 0,
    SPEC_PALADIN_PROTECTION = 1,
    SPEC_PALADIN_RETRIBUTION = 2,
    SPEC_HUNTER_BEAST_MASTERY = 0,
    SPEC_HUNTER_MARKSMANSHIP = 1,
    SPEC_HUNTER_SURVIVAL = 2,
    SPEC_ROGUE_ASSASSINATION = 0,
    SPEC_ROGUE_COMBAT = 1,
    SPEC_ROGUE_SUBLETY = 2,
    SPEC_PRIEST_DISCIPLINE = 0,
    SPEC_PRIEST_HOLY = 1,
    SPEC_PRIEST_SHADOW = 2,
    SPEC_DEATH_KNIGHT_BLOOD = 0,
    SPEC_DEATH_KNIGHT_FROST = 1,
    SPEC_DEATH_KNIGHT_UNHOLY = 2,
    SPEC_SHAMAN_ELEMENTAL = 0,
    SPEC_SHAMAN_ENHANCEMENT = 1,
    SPEC_SHAMAN_RESTORATION = 2,
    SPEC_MAGE_ARCANE = 0,
    SPEC_MAGE_FIRE = 1,
    SPEC_MAGE_FROST = 2,
    SPEC_WARLOCK_AFFLICTION = 0,
    SPEC_WARLOCK_DEMONOLOGY = 1,
    SPEC_WARLOCK_DESTRUCTION = 2,
    SPEC_DRUID_BALANCE = 0,
    SPEC_DRUID_FERAL = 1,
    SPEC_DRUID_RESTORATION = 2,
    SPEC_MONK_BREWMASTER = 0
};
```
1. Add the class ID, this is going to be the class number and with what we will calculate the CLASS_MASK (this case 2048)
2. Up the number of classes (in this case 12+1)
3. Define the class as playable (classes can be used only as npc usable)
4. Add the spellfamily (very important to remember this number, we will use it later to create spells for this class)
5. Add the specialization (will be used to add talents and spells to book)

### objectmgr.cpp
This here controls de gain of stats each level, I just copied the warrior ones in this case

This works as a bool, it stablishes that if the player is over lvl 23 he gains 2 points of strenght each level, but if he's not over lvl 23 but over lvl 1 he gains 1
```
for (uint8 lvl = sWorld->getIntConfig(CONFIG_MAX_PLAYER_LEVEL)-1; lvl < level; ++lvl)
    {
        switch (_class)
        {
            [...]        
            case CLASS_DRUID:
                info->stats[STAT_STRENGTH]  += (lvl > 38 ? 2: (lvl > 6 && (lvl%2) ? 1: 0));
                info->stats[STAT_STAMINA]   += (lvl > 32 ? 2: (lvl > 4 ? 1: 0));
                info->stats[STAT_AGILITY]   += (lvl > 38 ? 2: (lvl > 8 && (lvl%2) ? 1: 0));
                info->stats[STAT_INTELLECT] += (lvl > 38 ? 3: (lvl > 4 ? 1: 0));
                info->stats[STAT_SPIRIT]    += (lvl > 38 ? 3: (lvl > 5 ? 1: 0));
                break;
	        case CLASS_MONK:
                info->stats[STAT_STRENGTH]  += (lvl > 23 ? 2: (lvl > 1  ? 1: 0));
                info->stats[STAT_STAMINA]   += (lvl > 23 ? 2: (lvl > 1  ? 1: 0));
                info->stats[STAT_AGILITY]   += (lvl > 36 ? 1: (lvl > 6 && (lvl%2) ? 1: 0));
                info->stats[STAT_INTELLECT] += (lvl > 9 && !(lvl%2) ? 1: 0);
                info->stats[STAT_SPIRIT]    += (lvl > 9 && !(lvl%2) ? 1: 0);
        }
    }
}
```
**MY CLASS WILL WORK WITH ENERGY INSTEAD OF MANA, THAT'S WHY I USED THE WARRIOR ONE**
1. We add a *break;* and the case for our class

### player.cpp

```
if (_class == CLASS_WARRIOR || _class == CLASS_PALADIN || _class == CLASS_DEATH_KNIGHT || _class == CLASS_MONK)

const float dodge_base[MAX_CLASSES] =
    {
         0.024211f, // Warlock
         0.0f,      // ??
         0.056097f,  // Druid
	 0.064974f  // Monk
    };
    
const float crit_to_dodge[MAX_CLASSES] =
    {         
	 1.00f/1.15f,    // Mage
         0.97f/1.15f,    // Warlock (?)
         0.0f,           // ??
         2.00f/1.15f,     // Druid
	 1.75f/1.15f	 // Monk
    };
```
1. The LFG (looking for group) system needs to know if classes can roll for certain items, I'm setting it with plate users, you can do whatever you want
2. Dodge based on agility, use values you want
3. Like dodge from agilty, use values you want

### spell_item.cpp
```
void HandleDummy(SpellEffIndex /*effIndex*/)
            {
                Unit* caster = GetCaster();
                std::vector<uint32> possibleSpells;
                switch (caster->getClass())
                {
                    case CLASS_WARLOCK:
                    case CLASS_MAGE:
                    case CLASS_PRIEST:
                        possibleSpells.push_back(SPELL_FLASK_OF_THE_NORTH_SP);
                        break;
		    case CLASS_MONK:
		    case CLASS_DEATH_KNIGHT:
                    case CLASS_WARRIOR:
                        possibleSpells.push_back(SPELL_FLASK_OF_THE_NORTH_STR);
                        break;
```
This is just for complementing more data for the new class on how things interact with it


**THIS CORE SHOULD COMPILE AND BUILD WITHOUT PROBLEM, CHECK IF IT DOES**
