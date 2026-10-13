# TrinityCoreAddClass
How to add a class with features to TrinityCore

Final product: https://www.youtube.com/watch?v=mnOWoI1tVKw

## Table of contents
1. [Changes to the core files](https://github.com/MunchieKnows/TrinityCoreAddClass#changes-to-the-core-files)
   1. [shareddefines.h](https://github.com/MunchieKnows/TrinityCoreAddClass#shareddefinesh)
   2. [objectmgr.cpp](https://github.com/MunchieKnows/TrinityCoreAddClass#objectmgrcpp)
   3. [player.cpp](https://github.com/MunchieKnows/TrinityCoreAddClass#playercpp)
   4. [spell_item.cpp](https://github.com/MunchieKnows/TrinityCoreAddClass#spell_itemcpp)
   5. [stat_system.cpp](https://github.com/MunchieKnows/TrinityCoreAddClass#stat_systemcpp)
2. [Changes to the interface files](https://github.com/MunchieKnows/TrinityCoreAddClass/blob/master/README.md#changes-to-the-interface-files)
   1. [CharacterCreate.lua](https://github.com/MunchieKnows/TrinityCoreAddClass/blob/master/README.md#gluexml-charactercreatelua)
   2. [CharacterCreate.xml](https://github.com/MunchieKnows/TrinityCoreAddClass/blob/master/README.md#gluexml-charactercreatexml)
   3. [GlueStrings.lua](https://github.com/MunchieKnows/TrinityCoreAddClass/blob/master/README.md#gluexml-gluestringslua)
   4. [Constants.lua](https://github.com/MunchieKnows/TrinityCoreAddClass/blob/master/README.md#framexml-constantslua)
   5. [DBC ChrClasses.dbc](https://github.com/MunchieKnows/TrinityCoreAddClass/blob/master/README.md#dbc-chrclassesdbc)
3. [Starting gear](https://github.com/MunchieKnows/TrinityCoreAddClass/blob/master/README.md#starting-gear)
   1. [CharStartOutfit.dbc](https://github.com/MunchieKnows/TrinityCoreAddClass/blob/master/README.md#charstartoutfitdbc)
   2. [playercreateinfo (world database)](https://github.com/MunchieKnows/TrinityCoreAddClass/blob/master/README.md#playercreateinfo-world-database)
   3. [playercreateinfo_item (world database)](https://github.com/MunchieKnows/TrinityCoreAddClass/blob/master/README.md#playercreateinfo_item-world-database)
   4. [Starting spells and action bar (world database)](https://github.com/MunchieKnows/TrinityCoreAddClass/blob/master/README.md#starting-spells-and-action-bar-world-database)
4. [Abilities and talents](https://github.com/MunchieKnows/TrinityCoreAddClass/blob/master/README.md#abilities-and-talents)
   1. [Adding spells (Spell.dbc)](https://github.com/MunchieKnows/TrinityCoreAddClass/blob/master/README.md#adding-spells-spelldbc)
   2. [Adding talents (Talent.dbc + TalentTab.dbc)](https://github.com/MunchieKnows/TrinityCoreAddClass/blob/master/README.md#adding-talents-talentdbc--talenttabdbc)
5. [Base stats and level data (world database)](https://github.com/MunchieKnows/TrinityCoreAddClass/blob/master/README.md#base-stats-and-level-data-world-database)
   1. [player_classlevelstats](https://github.com/MunchieKnows/TrinityCoreAddClass/blob/master/README.md#player_classlevelstats)
   2. [player_levelstats](https://github.com/MunchieKnows/TrinityCoreAddClass/blob/master/README.md#player_levelstats)
   3. [playercreateinfo_skills](https://github.com/MunchieKnows/TrinityCoreAddClass/blob/master/README.md#playercreateinfo_skills)

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


### stat_system.cpp
The diminishing returns caps are per-class arrays. If you don't add an entry for our class (index = class-1 = 11) it is silently zeroed and the monk loses ALL diminishing dodge/parry/miss from rating and defense skill. This affects four arrays: `m_diminishing_k`, `miss_cap`, `parry_cap` and `dodge_cap`.

Example (`dodge_cap`, add the monk value after druid, copy the rogue one in this case):
```
float const dodge_cap[MAX_CLASSES] =
{
    88.129021f,     // Warrior
    [...]
    0.0f,           // ??
    116.890707f,    // Druid
    145.560408f     // Monk
};
```
1. Add a monk value at the end of each of the four arrays (after druid), copy the rogue values in this case

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
### DBC ChrClasses.dbc
**THIS ONE IS MANDATORY: THE CORE LOOKS UP THE CLASS IN THIS DBC WHEN CREATING A CHARACTER, WITHOUT IT CREATION FAILS ("Class (12) not found in DBC") AND YOUR `playercreateinfo` ROWS ARE REJECTED AT STARTUP**

Add a row for the monk:
- id = 12
- DisplayPower = 3 (energy) - **THIS IS WHERE THE CORE GETS THE CLASS POWER TYPE FROM (`uint8 powertype = cEntry->DisplayPower` in Player.cpp), THAT'S WHY THE MONK USES ENERGY WITHOUT ANY CODE CHANGE**
- name in your locale ("Monje"), copy the rest of the fields from an existing row

Save it to both the client Data folder and the server dbc folder (the core loads it too)


## Starting gear

**THE STARTING GEAR IS DATA-DRIVEN: THE MAIN KIT COMES FROM `CharStartOutfit.dbc` (client) AND THE REST FROM THE WORLD DATABASE. NO CORE CODE CHANGES ARE NEEDED FOR THIS PART**

When a character is created the core does this (`Player.cpp`, look for `GetCharStartOutfitEntry`):
1. Looks up the outfit in `CharStartOutfit.dbc` by **race + class + sex** and stores every item in the best slot (food x4, drink x2)
2. Adds the extra items from the `playercreateinfo_item` table
3. Learns the spells/skills of `playercreateinfo_spell_custom` / `playercreateinfo_skills` and casts the spells of `playercreateinfo_cast_spell`
4. Fills the action bar with `playercreateinfo_action`
5. Spawns you at the position defined in `playercreateinfo`

### CharStartOutfit.dbc
This DBC holds the gear every character spawns with: one row per race/class/sex, up to 24 items per row. The server reads this file too - if an item id doesn't exist in `item_template` it will log an error at startup (`ObjectMgr.cpp`) and skip that item.

Fields the core uses (the rest can stay as they are):
```
id              uint   unique row id, pick a free one (continue after the last existing row)
race            byte   race id (1=Human, 2=Orc, 4=Dwarf, 8=NightElf, 16=Undead, 32=Tauren, 64=Gnome, 128=Troll, 512=BloodElf, 1024=Draenei)
class           byte   class id (MONK = 12)
sex             byte   0=male, 1=female
outfit          byte   leave 0
item_1..item_24 int    item entries, -1 when the slot is empty
```

Steps:
1. Open `CharStartOutfit.dbc` with a DBC editor (e.g. DbcEditor)
2. Look at an existing leather row of the same race (rogue/hunter) to see how many items and which slots they use, that's your template
3. Add one row per playable race: class=12, sex=0, with your monk gear item ids (chest, legs, boots, bracers, belt, cloak, main-hand weapon... fill the rest with -1)
4. Duplicate every row with sex=1 for the female characters
5. Save the DBC to both the client Data folder and the server dbc folder

**REMEMBER: EVERY ITEM ID YOU PUT HERE MUST EXIST IN `item_template`, IF YOU CREATE NEW ITEMS FOR THE MONK GEAR ADD THEM TO THE DATABASE FIRST**

### playercreateinfo (world database)
Spawn point per race/class. You need a row for **every** race that can create a monk, otherwise character creation will fail.
```sql
-- human monk -> Elwynn Forest
INSERT INTO `playercreateinfo` (`race`, `class`, `map`, `zone`, `position_x`, `position_y`, `position_z`, `orientation`) VALUES
(1, 12, 0, 34, -8837.4, 626.57, 99.615, 2.3562);
-- repeat for each playable race with its own spawn point (orc -> Durotar, undead -> Tirisfal Glades, ...)
```
Note: some older TDB versions of this table use a single `ID` column = class*100+race (human monk = 1201) instead of separate `race`/`class` columns, check yours.

### playercreateinfo_item (world database)
Extra items on top of the DBC outfit, with amount. Use it for things you don't want to put in the DBC (flasks, tokens...):
```sql
INSERT INTO `playercreateinfo_item` (`race`, `class`, `itemid`, `amount`) VALUES
(1, 12, <item id>, 1);
```

### Starting spells and action bar (world database)
Spells learned at creation, spells cast when you spawn, and the initial action bar:
```sql
-- spell learned on creation (example: Stun). racemask/classmask are bitmasks, monk class mask = 2048, human race mask = 1
INSERT INTO `playercreateinfo_spell_custom` (`racemask`, `classmask`, `Spell`) VALUES
(1, 2048, 740);

-- spell cast right after spawning (optional)
INSERT INTO `playercreateinfo_cast_spell` (`raceMask`, `classMask`, `spell`) VALUES
(1, 2048, <spell id>);

-- action bar: button 1 = Stun (type 0 = spell, type 1 = item)
INSERT INTO `playercreateinfo_action` (`race`, `class`, `button`, `action`, `type`) VALUES
(1, 12, 1, 740, 0);
```
Add here the starter spells of your monk work (the ones from the spell family we defined above).

**AFTER UPDATING THE DBC AND IMPORTING THE SQL, RESTART THE SERVER AND CREATE A MONK: IT SHOULD SPAWN WITH ITS GEAR EQUIPPED AT THE RIGHT POSITION. IF IT SPAWNS EMPTY, CHECK THE LOG FOR `CharStartOutfit`/`item_template` ERRORS**

## Abilities and talents

**SPELLS AND TALENTS ARE DATA-DRIVEN: YOU EDIT `Spell.dbc`, `Talent.dbc` AND `TalentTab.dbc` (CLIENT DBCs, THE CORE READS THEM TOO). NO CORE CODE CHANGES ARE NEEDED FOR THIS PART**

### Adding spells (Spell.dbc)
The workflow is always the same: copy an existing row of a similar spell and modify the fields. Pick free ids above the last existing row (the examples below use 60001-60003, adjust to your file).

Fields that matter for a simple monk spell (full list in the `spellsdbc.txt` notes):
```
1   id               unique spell id
4-11 attributes     leave 0x10000 (prevents cast when shapeshifted)
16  target type      copy from your reference spell
28  cast time row    row in SpellCastTimes.dbc, 0 = instant
40  duration index   row in SpellDuration.dbc (for auras), 0 if not used
41  powertype        3 = energy (our class resource)
46  rangeindex       row in SpellRange.dbc, melee spells use the melee row
71-73 spell effects  order matters: first effect uses 71/80/95, second 72/81/96, third 73/82/97
80-82 effectbasepoints damage/heal amount of each effect
95-97 aura type      only when the effect is APPLY_AURA (e.g. 3 = periodic damage)
98-100 amplitude    tick in ms / bonus value of the aura, per effect
131-134 visuals/icons any valid ids from SpellVisual/SpellIcon.dbc
142 name             spell name
159 rank             0 for a single-rank spell
153-168 ranks        all 0 for a single-rank spell
208 spellfamilyname  18 (SPELLFAMILY_MONK, the one we defined in shareddefines.h)
```

**Example 1: basic attack (instant physical damage, energy cost)**
Copy any instant melee spell and set:
- 71 = 2 (`SPELL_EFFECT_SCHOOL_DAMAGE`), 72-73 = 0
- 225 = 1 (physical school of damage)
- 80 = base damage (e.g. 50), 81-82 = 0
- 41 = 3 (energy), 28 = 0 (instant), 46 = melee range row
- 142 = "Tiger Palm", 208 = 18

**Example 2: dot (periodic damage aura)**
Copy any poison/dot spell and set:
- 71 = 6 (`SPELL_EFFECT_APPLY_AURA`)
- 95 = 3 (`SPELL_AURA_PERIODIC_DAMAGE`)
- 40 = duration row in SpellDuration.dbc (e.g. 12s), 98 = tick in ms (e.g. 2000)
- 80 = damage per tick (e.g. 25)
- 41 = 3 (energy), 28 = 0 (instant)
- 142 = "Jade Serpent Strike", 208 = 18

**Example 3: energy drink (instant)**
Copy any drink/food spell and set:
- 71 = 30 (`SPELL_EFFECT_ENERGIZE`), 80 = energy restored (e.g. 40)
- 28 = 0 (instant), keep the self target of your reference drink
- 142 = "Brew", 208 = 18

**REMEMBER: SET 208 = 18 ON EVERY MONK SPELL, THE CORE USES THE SPELLFAMILY TO KNOW THE SPELL BELONGS TO OUR CLASS (THE ONE WE ADDED IN shareddefines.h)**

Starter abilities that are not talents can be given on character creation with `playercreateinfo_spell_custom` (see the starting gear section). Later, if you want a buff instead of a dot, use 95 = 29 (`SPELL_AURA_MOD_STAT`) and put the stat index in the effect misc value field (0=strength, 1=agility, 2=stamina, 3=intellect, 4=spirit).

### Adding talents (Talent.dbc + TalentTab.dbc)
In TrinityCore 3.3.5 talents are NOT in a SQL table: the core reads `Talent.dbc` and `TalentTab.dbc` at startup (`SpellMgr::LoadSpellTalentRanks`) and logs an error for every talent that references a missing spell, so add the spells first.

Step 1 - create the monk tab in `TalentTab.dbc`:
- new row with the next free id (the existing tabs are 0-9, use 10)
- name in your locale (e.g. "Monje"), any valid icon id
- ClassMask = 2048 (the monk class mask from shareddefines.h)

Step 2 - create the talent rows in `Talent.dbc` (pick free ids above the last existing row):
```
1  ID               unique talent row id
2  iRefID_TalentTab your monk tab (10)
3  tierID           row in the tree
4  columnIndex      column in the tree
5-9 iRefID_spellRank spell ids, one per rank (up to 5 ranks, 0 when empty)
14 iRefID_prereqTalent id of the talent that unlocks this one (0 for first tier)
17 iRefID_prereqRank points required in that talent
20 flags            1 if the talent only has one point, otherwise 0
21 requiredSpellID  0
```

Example tree:
```
tier 1: [Tiger Palm (5 pts)]    [Brew (1 pt)]
tier 2: [Jade Serpent Strike (requires 5 in Tiger Palm)]
```
- row id=900: tab=10, tier=1, col=1, spellRank1=60001, prereq=0, flags=0
- row id=901: tab=10, tier=1, col=2, spellRank1=60003, prereq=0, flags=1 (single point)
- row id=902: tab=10, tier=2, col=1, spellRank1=60002, prereqTalent=900, prereqRank=5, flags=0

**AFTER UPDATING THE DBCs RESTART THE SERVER AND CHECK THE LOG: EVERY "does not exist" ERROR MEANS A SPELL OR TALENT REFERENCE IS WRONG. THEN CREATE A MONK AND OPEN ITS TALENT WINDOW TO VERIFY THE TREE**

## Base stats and level data (world database)

**THE CORE ABORTS AT STARTUP IF THIS DATA IS MISSING FOR A CLASS PRESENT IN ChrClasses.dbc, SO THIS SECTION IS MANDATORY**

### player_classlevelstats
Base health/mana per class per level. Without a level 1 row for the monk the server refuses to start ("Class 12 Level 1 does not have health/mana data!"). The simplest way is to derive it from an existing class:
```sql
-- copy the rogue rows (basemana = 0, we use energy)
INSERT INTO `player_classlevelstats` (`class`, `level`, `basehp`, `basemana`)
SELECT 12, level, basehp, 0 FROM `player_classlevelstats` WHERE class = 4;
```

### player_levelstats
Base stats (str/agi/sta/inte/spi) per race/class per level. Without a level 1 row for every race that can create a monk the server refuses to start ("Race X Class 12 Level 1 does not have stats data!").
```sql
-- copy the rogue rows of each playable race
INSERT INTO `player_levelstats` (`race`, `class`, `level`, `str`, `agi`, `sta`, `inte`, `spi`)
SELECT race, 12, level, str, agi, sta, inte, spi FROM `player_levelstats` WHERE class = 4;
```
Missing levels are filled from the previous one at startup, but a missing level 1 is fatal.

### playercreateinfo_skills
Skills learned on creation (languages, first aid, fishing...). Not fatal if empty, but your monk will spawn without them:
```sql
-- example: first aid for human monks (raceMask/classmask are bitmasks, repeat per race or combine masks)
INSERT INTO `playercreateinfo_skills` (`raceMask`, `classMask`, `skill`, `rank`) VALUES
(1, 2048, 129, 75);
```

**AFTER IMPORTING ALL OF THIS THE SERVER SHOULD START WITHOUT "does not have ... data" ERRORS: CREATE A MONK AND CHECK IT HAS SANE BASE STATS AT LEVEL 1**
