---
layout: default
title: Converting Animations
permalink: /slsb/converting-animations/
---

# Converting Animations

How to convert SLAL animation packs to SLSB format for SexLab P+.

---

## Quick Start

To convert SLAL packs quickly:

1. Set up your development environment (see [Environment Setup](../environment-setup/))
2. Place SLAL packs in `SLAL_Packs/` folder with correct structure
3. Run `execute_convert_full.cmd`
4. Collect converted `.slr` files from `SLSB_Outputs/`
5. Package and distribute

---

## Detailed Conversion Process

### Prerequisites

Before starting, ensure you have:
- The SLSB.Convert.Dev.Essentials downloaded and extracted
- All FNIS tools installed (see [Environment Setup](../environment-setup/))
- Latest hashes from `Automated.SLSB.Conversions` in `updated_slsb_jsons/`
- SLAL packs to convert in `SLAL_Packs/` folder

### Step 1: Organize Your Packs

Place SLAL packs in the `SLAL_Packs/` folder with the required structure:

```
SLAL_Packs/
├── BillyyCreatures/
│   └── SLAnims/
│       └── json/
│           ├── BillyyCreatures.json
│           └── [other animation files]
├── AnimationsByLeito/
│   └── SLAnims/
│       └── json/
│           └── AnimationsByLeito.json
└── [other packs]/
    └── SLAnims/
        └── json/
```

**Important:** Structure must be `PackName/SLAnims/json/` or the converter won't find the files.

### Step 2: Run the Batch Converter

#### Windows
1. Open File Explorer to your dev environment
2. Double-click `execute_convert_full.cmd`
3. The script runs automatically and processes all packs

#### Linux/Mac
```bash
cd /path/to/dev/env
python convert.py SLAL_Packs/ -o SLSB_Outputs/
```

### Step 3: Collect Output Files

After conversion completes, your `.slr` files are in `SLSB_Outputs/`:

```
SLSB_Outputs/
├── BillyyCreatures.slr
├── AnimationsByLeito.slr
├── [other converted packs].slr
└── manifest.txt              # Conversion report
```

### Step 4: Package for Users

Create a mod folder for distribution:

```
MyPack_SLSB_Conversion/
└── Data/
    └── SKSE/
        └── Plugins/
            └── SexLabRegistry/
                └── [PackName].slr
```

Users install this on top of the original SLAL pack (for meshes/assets).

---

## Understanding SLAL Structure

An SLAL pack contains both animation definitions and compiled registration scripts:

```
MyAnimPack/
├── meshes/                         # Animation files
│   └── actors/
│       └── character/
│           └── animations/
│               └── MyAnims/
│                   ├── anim001_s1_a0.hkx
│                   ├── anim001_s1_a1.hkx
│                   └── [animation files]
├── Source/Scripts/                 # Papyrus source (for reference)
│   └── SLAL_MyAnimPack.psc
└── scripts/                        # Compiled Papyrus (old system)
    └── SLAL_MyAnimPack.pex
```

For conversion, we need the JSON definitions that come from the pack's animation definitions.

### What Gets Converted

The converter extracts:
- **Animation names and IDs** - Display name and unique identifier
- **Gender/race tags** - Actor requirements (male, female, creature, etc.)
- **Interaction types** - Vaginal, anal, oral, etc.
- **Animation stages** - Which .hkx files play and timing
- **Actor positions** - Offsets and rotations for each participant
- **Creature info** - Race keys for creature animations
- **Tags/categories** - Aggressive, Loving, Foreplay, etc.

---

## Manual Adjustments

After conversion, you may need to make manual adjustments to the JSON or compiled `.slr` files.

### Common Adjustments

| Issue | Solution |
|-------|----------|
| Wrong animation tags | Edit JSON before recompiling |
| Incorrect gender tags | Update actor definitions |
| Timing issues | Adjust stage timer values |
| Missing interaction types | Add appropriate tags |
| Position offsets wrong | Manually adjust offset arrays |

### When to Adjust

Most conversions work automatically, but you may need to adjust for:
- Animations with non-standard gender combinations
- Creature animations with unusual race pairings
- Packs with missing metadata
- Legacy packs with incomplete definitions

### Testing Adjustments

After editing JSON:
1. Re-run the converter to compile updated JSON
2. Install the new `.slr` file
3. Launch game and verify animations appear correctly
4. Test with MatchMaker or console commands

---

## Creature Animations

Creature animation conversions require additional metadata for proper race matching.

### Creature Animation JSON Structure

```json
{
  "name": "Creature Animation Name",
  "id": "creature_anim_id",
  "tags": ["Aggressive", "Creature"],
  "actors": [
    {
      "gender": "female",
      "race": "human"
    },
    {
      "gender": "creature",
      "race": "Wolf",
      "race_key": "Wolves"
    }
  ],
  "stages": [...]
}
```

### Common Race Keys

When adding creature actors, use the correct `race_key`:

| Race | race_key | Examples |
|------|----------|----------|
| Canines | `Wolves` | Wolves, Dogs, Huskies |
| Bears | `Bears` | Black Bears, Cave Bears |
| Trolls | `Trolls` | Trolls, Ice Trolls |
| Giants | `Giants` | Giants |
| Spiders | `Spiders` | Frostbite Spiders, Giant Spiders |
| Draugr | `Draugrs` | Draugr, Draugr Lords |
| Falmer | `Falmer` | Falmer, Falmer Gloomstalkers |
| Dragons | `Dragons` | Dragons, Alduin |

### Human + Creature Format

For animations with both human and creature actors:

```json
"actors": [
  {
    "gender": "female",
    "race": "human"        // Human doesn't need race_key
  },
  {
    "gender": "creature",
    "race": "Wolf",
    "race_key": "Wolves"   // Creature needs both race and race_key
  }
]
```

---

## Testing Your Conversion

### Pre-Test Verification

Before testing in-game, verify your conversion:

1. Check output folder has `.slr` files created
2. Verify manifest shows no errors
3. Ensure output files are not empty (check file size > 1KB)

### In-Game Testing

1. Install the original SLAL pack (for mesh files)
2. Install your converted `.slr` files in `Data/SKSE/Plugins/SexLabRegistry/`
3. Launch game and load a save
4. Open SexLab MCM → Registrations
5. Check if your animations appear in the list

### Testing with MatchMaker

Use the MatchMaker tool to test animations without manual setup:

1. Open SexLab MCM
2. Go to MatchMaker
3. Select your animation pack
4. Choose actors and let MatchMaker start the scene
5. Verify animation plays correctly

### Debugging Failed Conversions

If animations don't appear:

| Issue | Check |
|-------|-------|
| `.slr` file not created | Verify SLAL_Packs folder structure |
| Manifest shows errors | Check FNIS tools are installed |
| No animations in MCM | Verify `Data/SKSE/Plugins/SexLabRegistry/` path |
| Animations crash | Check actor/creature count matches animation |
| Wrong animation plays | Verify JSON wasn't manually edited incorrectly |

### Papyrus Logging

For detailed debugging:

1. Edit `skyrim.ini`:
   ```ini
   [Papyrus]
   bEnableLogging=1
   bEnableTrace=1
   ```

2. Check logs at:
   ```
   Documents/My Games/Skyrim Special Edition/Logs/Script/
   ```

---

## Distribution

### Package Structure

Your conversion package should contain:
```
MyPack_SLSB_Conversion/
└── Data/
    └── SKSE/
        └── Plugins/
            └── SexLabRegistry/
                └── MyPack.slr
```

### Documentation

Include a README noting:
- Required base pack (SLAL version)
- Installation order
- Any known issues
- Credits to original author

### Permissions

Always check the original pack's permissions before distributing conversions. Many authors require permission for derivative works.

---

## Getting Help

- **Discord**: [#slsb-and-pack-dev](https://discord.gg/JPSHb4ebqj)
- **Tutorial**: [MissCorruption's Guide](https://gist.github.com/MissCorruption/fc62e0b46e3652ad6a85fe903a6a9b37)
- **Examples**: Check existing converted packs for reference
