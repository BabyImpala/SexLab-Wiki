---
layout: default
title: Tagging Reference
permalink: /slp/tagging-reference/
---

# Tagging Reference

Tags describe and categorize scenes, allowing mod authors and users to find appropriate animations. This reference covers official tags, query syntax, and contextual tags.

---

## Tag Query Syntax

Tags are passed as comma-separated strings. A simple query looks like:

```
"Facing, Loving"
```

### Tag Prefixes

SLP+ supports prefixes to create complex queries:

| Prefix | Meaning | Example |
|--------|---------|---------|
| *(none)* | Required tag | `"Vaginal"` — must have this tag |
| `-` | Excluded tag | `"-Oral"` — must NOT have this tag |
| `~` | Optional tag (OR) | `"~Doggy, ~Missionary"` — at least one must match |
| `!` | Contextual tag | `"!aggressive"` — adds context, not scene selection |

### Complex Query Examples

```papyrus
; Must be Facing, must have Loving OR Spooning, must NOT have Holding
"Facing, ~Loving, ~Spooning, -Holding"

; Vaginal scene that is not Rough
"Vaginal, -Rough"

; Either Doggy or Missionary position
"~Doggy, ~Missionary"

; Aggressive context with Forced tag
"Forced, !aggressive"
```

---

## Official Tags

These standardized tags ensure consistency between animators and mod authors. Animators should use these when tagging their work; mod authors should query for these tags.

> **Note:** Animators can also add custom unofficial tags for signatures or special use cases not covered by official tags.

### Position & Setting Tags (SFW)

| Tag | Description |
|-----|-------------|
| `Behind` | Actor positioned behind partner |
| `Facing` | Actors hold eye contact |
| `Holding` | One actor is physically held mid-air by their partner |
| `Kissing` | Kissing interaction |
| `Kneeling` | Actor is kneeling |
| `Loving` | Loving/romantic interaction |
| `Lying` | Actor is lying down |
| `Magic` | Any use of supernatural abilities (e.g., levitating) |
| `Sitting` | Actor is sitting |
| `Spooning` | Spooning position |
| `Standing` | Actor is standing |

### Sexual Activity Tags (NSFW)

| Tag | Description |
|-----|-------------|
| `Anal` | Anal penetration |
| `Asphyxiation` | Breath play |
| `Blowjob` | Oral performed on penis |
| `Boobjob` | Breast stimulation of genitals |
| `BreastSucking` | Sucking on breasts |
| `Buttjob` | Stimulation between buttocks |
| `Cowgirl` | Passive on top, facing active |
| `Cunnilingus` | Oral performed on vagina |
| `Deepthroat` | Deep oral penetration |
| `Doggy` | Passive on hands/knees. Use `ProneBone` if lying on chest |
| `DoublePenetration` | Two simultaneous penetrations |
| `FaceSitting` | Sitting on partner's face |
| `Facial` | Ejaculation on face |
| `Feet` | Foot-related activity |
| `Fingering` | Digital penetration |
| `Fisting` | Fist penetration |
| `FootJob` | Foot stimulation of genitals |
| `Grinding` | Rubbing/grinding contact |
| `HandJob` | Manual stimulation of genitals |
| `LeadIn` | Suitable as foreplay scene |
| `LotusPosition` | Seated face-to-face position |
| `Masturbation` | Solo self-stimulation |
| `Missionary` | Face-to-face with active on top |
| `Oral` | General oral activity (usually with `Blowjob` or `Cunnilingus`) |
| `Oviposition` | Egg laying |
| `Penetration` | General penetration |
| `ProneBone` | Passive lying flat on chest |
| `ReverseCowgirl` | Passive on top, facing away |
| `ReverseSpitroast` | Variant spitroast position |
| `Rimming` | Oral-anal contact |
| `SixtyNine` | Mutual simultaneous oral |
| `Spanking` | Hitting buttocks (requires submissive) |
| `Spitroast` | Oral and penetration simultaneously |
| `Teasing` | Playful/teasing behavior |
| `Toys` | Dildos, etc. (recommended with AnimObjects) |
| `Tribadism` | Female-female genital contact |
| `TriplePenetration` | Three simultaneous penetrations |
| `Vaginal` | Vaginal penetration |

### Dominance Tags

These tags require at least one actor to be flagged as submissive:

| Tag | Description |
|-----|-------------|
| `Dominant` | Features dominant behavior; submissive moves on their own accord |
| `Forced` | Submissive is actively forced through violence, threat, or aggressive guidance. Replaces legacy "aggressive" and "rough" tags |
| `Humiliation` | Degrading/humiliating behavior |
| `Ryona` | Combat-related violence (requires submissive) |
| `Gore` | Extreme violence (requires submissive) |

---

## Contextual Tags

Contextual tags are a distinct type that tag the **thread** (running scene) rather than the animation itself. They have no influence on animation selection.

### Adding Contextual Tags

**Method 1:** Prefix with `!` in StartScene call:
```papyrus
SexLab.StartScene(actors, "Forced, !aggressive")
```

**Method 2:** Add after scene starts via API:
```papyrus
thread.AddContext("aggressive")
; or
thread.SetConsent(false)
```

### Official Contextual Tag

| Tag | Description |
|-----|-------------|
| `aggressive` | Marks scene as non-consensual |

> **Important:** To flag a scene as non-consent, you must explicitly pass `"!aggressive"` in your scene call or call `SetConsent(false)` after the scene starts.

### Submissive vs Non-Consent

The `akSubmissive` parameter (previously "Victim") in StartScene can now be used to flag an actor as submissive **without** implying non-consent. This allows:

- Better actor placement in animations (submissive positions)
- Scenes that *look* forced but are consensual roleplay
- More accurate animation matching

Non-consent requires the separate `!aggressive` contextual tag.

### Custom Contextual Tags

Contextual tags are open-ended—any string can be used. This enables mod-to-mod communication:

**Example: Seduction System**
```papyrus
; Mod A: Player seduces a guard to enter a locked room
SexLab.StartScene(actors, "Loving", akSubmissive = none, \
    akCenter = none, aiFurniture = 1, asHook = "SeductionScene")
thread.AddContext("Sexual_Seduction")

; Mod B: Reacts to scene end event
Event OnSexLabEnd(int threadID, bool hasPlayer)
    SexLabThread thread = SexLab.GetThread(threadID)
    if thread.HasContext("Sexual_Seduction")
        ; Track seduction count, unlock dialogue, etc.
    endIf
EndEvent
```

**Example: Compatibility Flag**
```papyrus
; Tell other mods to ignore this scene's events
thread.AddContext("MyMod_InternalScene")
```

---

## API Functions

### Tag Checking

```papyrus
; Check thread-level tags
bool Function HasTag(String asTag)

; Check active scene tags
bool Function HasSceneTag(String asTag)

; Check active stage tags  
bool Function HasStageTag(String asTag)

; Convenience functions
bool Function IsSceneVaginal()
bool Function IsSceneAnal()
bool Function IsSceneOral()
```

### Context Management

```papyrus
; Check/modify contextual tags
bool Function HasContext(String asTag)
Function AddContext(String asContext)
Function RemoveContext(String asContext)

; Consent shorthand
bool Function IsConsent()
Function SetConsent(bool abIsConsent)
```

---

## For SLSB Pack Authors

When creating SLSB animation packs, use official tags consistently:

1. **Use official tags** from this reference for maximum compatibility
2. **Apply position tags** (`Standing`, `Lying`, `Kneeling`, etc.) accurately
3. **Mark dominance scenes** appropriately with `Dominant`, `Forced`, etc.
4. **Add `LeadIn`** for foreplay-suitable animations
5. **Combine tags** logically (e.g., `Oral, Blowjob` or `Oral, Cunnilingus`)

Custom tags can supplement official ones for pack-specific categorization or filtering.

---

## See Also

- [API Reference](../api-reference/) — Full Papyrus API documentation
- [Converting Animations](../../slsb/converting-animations/) — SLAL to SLSB conversion guide
- [Settings Reference](../settings-reference/) — Animation filter scoring settings
