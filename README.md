# TrinityCoreAddClass
How to add a class with features to TrinityCore

Final product: https://www.youtube.com/watch?v=mnOWoI1tVKw

## Table of contents
1. [Changes to the core files](https://github.com/MunchieKnows/TrinityCoreAddClass#changes-to-the-core-files)
   1. [shareddefines.h](https://github.com/MunchieKnows/TrinityCoreAddClass#shareddefinesh)
   2. [objectmgr.cpp](https://github.com/MunchieKnows/TrinityCoreAddClass#objectmgrcpp)
   3. [player.cpp](https://github.com/MunchieKnows/TrinityCoreAddClass#playercpp)
   4. [spell_item.cpp](https://github.com/MunchieKnows/TrinityCoreAddClass#spell_itemcpp)
2. [Changes to the interface files](https://github.com/MunchieKnows/TrinityCoreAddClass/blob/master/README.md#changes-to-the-interface-files)
   1. [CharacterCreate.lua](https://github.com/MunchieKnows/TrinityCoreAddClass/blob/master/README.md#gluexml-charactercreatelua)
   2. [CharacterCreate.xml](https://github.com/MunchieKnows/TrinityCoreAddClass/blob/master/README.md#gluexml-charactercreatexml)
   3. [GlueStrings.lua](https://github.com/MunchieKnows/TrinityCoreAddClass/blob/master/README.md#gluexml-gluestringslua)
   4. [Constants.lua](https://github.com/MunchieKnows/TrinityCoreAddClass/blob/master/README.md#framexml-constantslua)

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
  SPELLFAMILY_PET	  = 17,
  SPELLFAMILY_MONK	  = 18 
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

## Changes to the interface files

**THIS CHANGES WILL REQUIRE YOU TO HAVE BOTH GLUEXML AND FRAMEXML CHECKS DISABLED FROM THE WOW.EXE**

### GlueXML CharacterCreate.lua
```
CHARACTER_FACING_INCREMENT = 2;
MAX_RACES = 10;
MAX_CLASSES_PER_RACE = 11;
NUM_CHAR_CUSTOMIZATIONS = 5;
MIN_CHAR_NAME_LENGTH = 2;
CHARACTER_CREATE_ROTATION_START_X = nil;
CHARACTER_CREATE_INITIAL_FACING = nil;

CLASS_ICON_TCOORDS = {
	["WARRIOR"]	= {0, 0.25, 0, 0.25},
	["MAGE"]	= {0.25, 0.49609375, 0, 0.25},
	["ROGUE"]	= {0.49609375, 0.7421875, 0, 0.25},
	["DRUID"]	= {0.7421875, 0.98828125, 0, 0.25},
	["HUNTER"]	= {0, 0.25, 0.25, 0.5},
	["SHAMAN"]	= {0.25, 0.49609375, 0.25, 0.5},
	["PRIEST"]	= {0.49609375, 0.7421875, 0.25, 0.5},
	["WARLOCK"]	= {0.7421875, 0.98828125, 0.25, 0.5},
	["PALADIN"]	= {0, 0.25, 0.5, 0.75},
	["DEATHKNIGHT"]	= {0.25, 0.49609375, 0.5, 0.75},
	["MONK"]	= {0.49609375, 0.7421875, 0.5, 0.75}
};
```
1. Up the max classes per race, so 10+1 in this case
2. Add the icon (You have to update the CharacterCreate-classes.blp image with the new icon in it)

### GlueXML CharacterCreate.xml
```
<CheckButton name="CharacterCreateClassButton11" inherits="CharacterCreateClassButtonTemplate" id="11">
	<Anchors>
		<Anchor point="LEFT" relativeTo="CharacterCreateClassButton10" relativePoint="RIGHT" x="6" y="0"/>
	</Anchors>
</CheckButton>
```
We add the button

### GlueXML GlueStrings.lua
```
CLASS_DRUID_FEMALE = "Los druidas cambian de forma y sienten afinidad por el reino animal y vegetal. Existen tres tipos: los de Equilibrio, que lanzan hechizos de Naturaleza o Arcanos a distancia; los Ferales, que pueden adoptar la forma felina o de oso para luchar cuerpo a cuerpo; y los de Restauración, que sanan a sus aliados y se centran en los hechizos de sanación en el tiempo. Sus estadísticas principales dependen de su función.";
CLASS_MONK = "Los Monjes son maestros en la lucha a mano limpia y utilizan sus armas solamente para llevar a cabo ejecuciones finales devastadoras.";
CLASS_MONK_FEMALE = "Los Monjes son maestros en la lucha a mano limpia y utilizan sus armas solamente para llevar a cabo ejecuciones finales devastadoras.";
[...]
CLASS_INFO_DEATHKNIGHT5 = "- Usa runas como recurso.";
CLASS_INFO_MONK0 = "- Función: Daño";
CLASS_INFO_MONK1 = "- Armadura media (cuero)";
CLASS_INFO_MONK2 = "- Gran agilidad.";
CLASS_INFO_MONK3 = "- Combate cuerpo a cuerpo.";
CLASS_INFO_MONK4 = "- Usa energía como recurso.";
CLASS_INFO_DRUID0 = "- Función: Daño, tanque, sanador";
[...]
HELP = "Ayuda";
MONK_DISABLED = "Monje\nDebes elegir una raza diferente para ser de esta clase.";
HERTZ = "Hz";
```

Add the strings for the new class (mine are in spanish, use your locale)

### FrameXML constants.lua
```
RAID_CLASS_COLORS = {
	["HUNTER"] = { r = 0.67, g = 0.83, b = 0.45 },
	["WARLOCK"] = { r = 0.58, g = 0.51, b = 0.79 },
	["PRIEST"] = { r = 1.0, g = 1.0, b = 1.0 },
	["PALADIN"] = { r = 0.96, g = 0.55, b = 0.73 },
	["MAGE"] = { r = 0.41, g = 0.8, b = 0.94 },
	["ROGUE"] = { r = 1.0, g = 0.96, b = 0.41 },
	["DRUID"] = { r = 1.0, g = 0.49, b = 0.04 },
	["SHAMAN"] = { r = 0.0, g = 0.44, b = 0.87 },
	["WARRIOR"] = { r = 0.78, g = 0.61, b = 0.43 },
	["DEATHKNIGHT"] = { r = 0.77, g = 0.12 , b = 0.23 },
	["MONK"] = { r = 0.60, g = 0.90, b = 0.40 },
};

CLASS_SORT_ORDER = {
	"WARRIOR",
	"DEATHKNIGHT",
	"PALADIN",
	"PRIEST",
	"SHAMAN",
	"DRUID",
	"ROGUE",
	"MAGE",
	"WARLOCK",
	"HUNTER",
	"MONK",
};

CLASS_ICON_TCOORDS = {
	["WARRIOR"]		= {0, 0.25, 0, 0.25},
	["MAGE"]		= {0.25, 0.49609375, 0, 0.25},
	["ROGUE"]		= {0.49609375, 0.7421875, 0, 0.25},
	["DRUID"]		= {0.7421875, 0.98828125, 0, 0.25},
	["HUNTER"]		= {0, 0.25, 0.25, 0.5},
	["SHAMAN"]	 	= {0.25, 0.49609375, 0.25, 0.5},
	["PRIEST"]		= {0.49609375, 0.7421875, 0.25, 0.5},
	["WARLOCK"]		= {0.7421875, 0.98828125, 0.25, 0.5},
	["PALADIN"]		= {0, 0.25, 0.5, 0.75},
	["DEATHKNIGHT"]		= {0.25, .5, 0.5, .75},
	["MONK"]		= {0.49609375, 0.7421875, 0.5, 0.75},
};
```

1. Color for /who
2. Class order
3. Icon again
