# TrinityCoreAddClass
How to add a class with features to TrinityCore

Final product: https://www.youtube.com/watch?v=mnOWoI1tVKw

## Changes to the core files

###### shareddefines.h
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
```
1. Add the class ID, this is going to be the class number and with what we will calculate the CLASS_MASK (this case 2048)
2. Up the number of classes (in this case 12+1)
3. Define the class as playable (classes can be used only as npc usable)
4. Add the spellfamily (very important to remember this number, we will use it later to create spells for this class)

###### objectmgr.cpp
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
1. We add a *break;* and the case for our class
