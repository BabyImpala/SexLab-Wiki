---
layout: default
title: Environment Setup
permalink: /slsb/environment-setup/
---

# Environment Setup

Set up your development environment for creating and converting SLSB animation packs.

---

## Required Software

### Python

SLSB conversion tools require Python 3.10 or higher.

#### Windows

1. Download from [python.org](https://www.python.org/downloads/)
2. Run the installer
3. **Important:** Check "Add Python to PATH"
4. Click "Install Now"

Verify installation:
```powershell
python --version
```

#### Alternative: Using Scoop (Windows)

```powershell
scoop install python
```


## Conversion Tools

### Getting the Development Essentials

1. Download **SLSB.Convert.Dev.Essentials** from the [Discord](https://discord.gg/JPSHb4ebqj) #slsb-and-pack-dev channel
2. Extract to a short path without spaces (e.g., `D:/SkyrimMods/SLSB.Convert.Dev.Essentials`)

### Required FNIS Tools

You'll need FNIS tools for animation processing. Download these from Nexus Mods:

1. [FNIS Behavior SE 7.6 XXL](https://www.nexusmods.com/skyrimspecialedition/mods/3038?tab=files&file_id=124620)
2. [FNIS Creature Pack SE 7.6](https://www.nexusmods.com/skyrimspecialedition/mods/3038?tab=files&file_id=124621)
3. [Command Line FNIS for Modders](https://www.nexusmods.com/skyrim/mods/81882?tab=files&file_id=1000235248)

### Required SexLab Tools

1. [SexLab AnimStageLabels](https://www.loverslab.com/files/file/27407-sexlab-anim-stage-labels/)
2. [HentaiRim Tags](https://www.loverslab.com/files/file/43761-hentairim-p/)

### Setting Up Dev Tools

After downloading, extract tools to your dev environment:

1. Extract FNIS Behavior SE → `base_game_replica\Data`
   - Take the **"tools"** folder from `FNIS Behavior SE 7.6 XXL\Data`
2. Extract FNIS Creature Pack → `base_game_replica\Data`
   - Take the **"tools"** folder
3. Extract Command Line FNIS → `base_game_replica\Data`
   - Extract the **"tools"** folder
4. Extract AnimStageLabels → `slate_action_logs`
   - Take the **"SLATE"** folder contents from `SLAnimStageLabels\SKSE\Plugins`
5. Extract HentaiRim Tags → `slate_action_logs`
   - Take the **"SLATE"** folder contents

The dev environment should now look like:
```
[dev_env]/
├── base_game_replica/
│   └── Data/
│       ├── tools/          (from FNIS Behavior)
│       ├── tools/          (from FNIS Creature)
│       └── tools/          (from Command Line FNIS)
├── slate_action_logs/      (animation stage info)
├── updated_slsb_jsons/     (for hash maintenance)
├── SLAL_Packs/             (packs to convert)
├── SLSB_Outputs/           (converted .slr files)
├── convert.py              (conversion script)
├── execute_convert_full.cmd
└── execute_convert_test.cmd
```

---

## Setting Up Your First Conversion

### Step 1: Prepare the Dev Environment

Extract the downloaded **SLSB.Convert.Dev.Essentials** to a path without spaces:

```
D:/SkyrimMods/SLSB.Convert.Dev.Essentials/
```

### Step 2: Add FNIS Tools

Follow the "Required FNIS Tools" section above to add:
- FNIS Behavior tools
- FNIS Creature tools  
- Command Line FNIS
- AnimStageLabels (slate_action_logs)
- HentaiRim Tags (slate_action_logs)

### Step 3: Maintain Animation Hashes

**Important:** SLSB and SLP+ identify scenes by hash. To prevent users from losing animation toggles and customizations on updates:

1. Download the latest `Automated.SLSB.Conversions.v_.7z` from the mod page
2. Extract and search for `.json` files
3. Copy all JSON files to `[dev_env]\updated_slsb_jsons/`
4. The converter will use these hashes for new conversions

This ensures users' saved animation preferences persist across updates.

### Step 4: Convert SLAL Packs

1. Place SLAL packs in `SLAL_Packs/` with proper structure (see Project Structure above)
2. Run `execute_convert_full.cmd` (Windows) or equivalent script
3. Wait for conversion to complete
4. Check `SLSB_Outputs/` for generated `.slr` files

### Step 5: Verify Conversions

The output directory should contain:
```
SLSB_Outputs/
├── BillyyCreatures.slr
├── AnimationsByLeito.slr
└── [other converted packs].slr
```

These `.slr` files are what you distribute to users.

## Testing Environment

### Game Setup

For testing conversions:

1. **Separate Profile**: Create a dedicated MO2/Vortex profile for testing
2. **Minimal Setup**: Only essential mods + SL P+ + your conversion
3. **Test Save**: Keep a clean save for quick testing

### Quick Test Process

1. Install your conversion
2. Launch game, load test save
3. Open MCM → SexLab
4. Check if animations appear
5. Test with console commands or MatchMaker

### Debugging

Enable Papyrus logging to catch errors:

`skyrim.ini`:
```ini
[Papyrus]
bEnableLogging=1
bEnableTrace=1
```

Check logs at:
```
Documents/My Games/Skyrim Special Edition/Logs/Script/
```

---

## Batch Processing Multiple Packs

### Automated Full Conversion

The toolkit provides batch scripts to convert multiple SLAL packs at once.

#### Windows

Double-click one of:
- `execute_convert_full.cmd` - Full conversion of all packs in `SLAL_Packs/`
- `execute_convert_test.cmd` - Test conversion (limited, for verification)

The script will:
1. Process each pack in `SLAL_Packs/`
2. Generate JSON in intermediate files
3. Compile to `.slr` files
4. Place output in `SLSB_Outputs/`
5. Report completion and any errors


### Monitoring Conversions

While conversion runs, you'll see output like:

```
Processing: BillyyCreatures...
  ✓ Extracted animations (342 animations)
  ✓ Generated JSON
  ✓ Compiled .slr files
  
Processing: AnimationsByLeito...
  ✓ Extracted animations (156 animations)
  ✓ Generated JSON
  ✓ Compiled .slr files

Done! 498 total animations converted.
```

Check `SLSB_Outputs/` for the `.slr` files.

### Troubleshooting Batch Conversions

| Issue | Cause | Solution |
|-------|-------|----------|
| Script won't run | File permissions | Right-click → "Run as Administrator" |
| "Python not found" | Python not in PATH | Install Python with "Add to PATH" checked |
| Pack not converted | Wrong folder structure | Ensure `SLAL_Packs/PackName/SLAnims/json/` layout |
| Missing tools | Incomplete FNIS setup | Follow "Required FNIS Tools" section |
| Empty output | No JSON found | Check source packs contain json/ folder |

---

## Quick Reference: File Paths

**Critical paths to remember:**

| File/Folder | Purpose | Example |
|---|---|---|
| `SLAL_Packs/` | Input SLAL packs | `SLAL_Packs/BillyyCreatures/SLAnims/json/` |
| `SLSB_Outputs/` | Output .slr files | `SLSB_Outputs/BillyyCreatures.slr` |
| `updated_slsb_jsons/` | Hash reference | Copy latest Automated.SLSB.Conversions JSONs here |
| `base_game_replica/Data/` | FNIS tools | All FNIS tools go here |
| `slate_action_logs/` | Animation labels | SLATE folder contents from AnimStageLabels & HentaiRim |
| `convert.py` | Conversion script | Run this with packs in SLAL_Packs/ |

## Additional Resources

- **Discord**: [#slsb-and-pack-dev](https://discord.gg/JPSHb4ebqj) for help
- **Tutorial**: [MissCorruption's Guide](https://gist.github.com/MissCorruption/fc62e0b46e3652ad6a85fe903a6a9b37)
- **GitHub**: [SexLab Repository](https://github.com/Scrabx3/SexLab) for source reference
