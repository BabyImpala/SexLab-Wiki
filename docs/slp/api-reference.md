---
layout: default
title: API Reference
permalink: /slp/api-reference/
---

# SexLab Patch Plus API Reference

Complete API documentation for SexLab P+ Papyrus scripting. This reference covers public functions for starting scenes, managing threads, and querying system state.

---

## Quick Start: Getting the API

### Get the SexLab API

```papyrus
SexLabFramework SexLab = SexLabUtil.GetAPI()
```

### Check if SexLab is Ready

```papyrus
if SexLabUtil.SexLabIsReady()
  ; SexLab is ready to start scenes
endIf
```

### Check if Scenes are Running

```papyrus
if SexLabUtil.SexLabIsActive()
  ; There are currently active scenes running
endIf
```

---

## Starting Scenes

The main entry point for creating animations. All scene-starting functions return a `SexLabThread` object to control the animation.

### StartScene() - Basic Scene Start

**Recommended: This is the preferred way to start scenes.**

```papyrus
SexLabThread Function StartScene(Actor[] akPositions, String asTags, Actor akSubmissive = none, \
    ObjectReference akCenter = none, int aiFurniture = 1, String asHook = "")
```

**Parameters:**
- `akPositions` (Actor[]) - Array of actors to animate (1-5 actors)
- `asTags` (String) - Requested animation tags, comma-separated
  - Example: `"Aggressive, Vaginal"`
  - Prefix `~` for OR-conjunctions: `"~Doggy, ~Missionary"` (has either tag)
  - Prefix `-` to exclude: `"-Oral, -Anal"` (does not have these tags)
  - Example combined: `"~Doggy, ~Missionary, -Rough"` = (Doggy OR Missionary) AND NOT Rough
- `akSubmissive` (Actor) - Optional: Actor to treat as submissive for context
- `akCenter` (ObjectReference) - Optional: Position to place actors near
- `aiFurniture` (int) - Furniture preference: 0 = Disable, 1 = Allow (default), 2 = Prefer
- `asHook` (String) - Optional: Local hook name for thread-specific events

**Returns:**
- `SexLabThread` - Control object for the running scene
- `None` - If no valid animation found

**Example:**
```papyrus
Actor[] actors = new Actor[2]
actors[0] = PlayerRef
actors[1] = NPC

SexLabThread thread = SexLab.StartScene(actors, "Vaginal, Loving")
if thread
  ; Scene started successfully
endIf
```

---

### StartSceneEx() - Advanced Scene Start with Specific Animations

Start a scene with pre-selected animations instead of tag-based selection.

```papyrus
SexLabThread Function StartSceneEx(Actor[] akPositions, String[] asScenes, Actor akSubmissive = none, \
    String asContext = "", ObjectReference akCenter = none, int aiFurniture = 1, String asHook = "")
```

**Parameters:**
- `akPositions` (Actor[]) - Array of actors to animate
- `asScenes` (String[]) - Array of animation IDs to choose from
- `akSubmissive` (Actor) - Optional: Submissive actor
- `asContext` (String) - Optional: Custom context tags for mod communication
- `akCenter` (ObjectReference) - Optional: Center position
- `aiFurniture` (int) - Furniture preference
- `asHook` (String) - Optional: Local hook name

**Returns:** `SexLabThread` or `None`

**Example:**
```papyrus
String[] scenes = new String[1]
scenes[0] = "SomeAnimPack_Vaginal_01"

SexLabThread thread = SexLab.StartSceneEx(actors, scenes)
```

---

### StartSceneQuick() - Convenience Function

Quick scene start with actors passed individually instead of as an array.

```papyrus
SexLabThread Function StartSceneQuick(Actor akActor1, Actor akActor2 = none, \
    Actor akActor3 = none, Actor akActor4 = none, Actor akActor5 = none, \
    Actor akSubmissive = none, String asTags = "", String asHook = "")
```

**Parameters:** Individual actors plus optional submissive and tags

**Returns:** `SexLabThread` or `None`

---

## Thread Control

Once a scene is started, use `SexLabThread` to control and query the animation.

### Get Thread ID

```papyrus
int Function GetThreadID()
```

Returns the unique ID of this thread. Can be used to reference this thread throughout the framework.

### Thread Status

```papyrus
int Function GetStatus()
```

**Returns:**
- `STATUS_UNDEF` (0) - Undefined
- `STATUS_IDLE` (1) - Inactive
- `STATUS_SETUP` (2) - Preparing animation (data may be incomplete)
- `STATUS_INSCENE` (3) - Playing animation
- `STATUS_ENDING` (4) - Ending (data still available, most functions disabled)

### Scene Information

```papyrus
String Function GetActiveScene()        ; Currently playing scene
String Function GetActiveStage()        ; Currently playing stage
String[] Function GetPlayingScenes()    ; All available scenes for this animation
```

### Scene Control

```papyrus
bool Function ResetScene(String asScene)       ; Switch to different scene
Function BranchTo(int aiNextBranch)            ; Jump to next animation branch
Function SkipTo(String asNextStage)            ; Jump to specific stage
Function StopAnimation()                       ; End the scene immediately
```

### Stage Tracking

```papyrus
String[] Function GetStageHistory()            ; All played stages so far
int Function GetStageHistoryLength()           ; Number of stages played
```

**Note:** History resets when scene changes. May include duplicates if animation loops.

---

## Tags & Context

### Tag Checking

```papyrus
bool Function HasTag(String asTag)             ; Does thread have this tag?
bool Function HasSceneTag(String asTag)        ; Does active scene have this tag?
bool Function HasStageTag(String asTag)        ; Does active stage have this tag?
```

### Convenience Tag Functions

```papyrus
bool Function IsSceneVaginal()
bool Function IsSceneAnal()
bool Function IsSceneOral()
```

### Custom Context

Custom context tags allow mods to communicate with each other about scene intent.

```papyrus
bool Function HasContext(String asTag)         ; Does thread own this context?
Function AddContext(String asContext)          ; Add context tag
Function RemoveContext(String asContext)       ; Remove context tag

bool Function IsConsent()                      ; Is this consent scene?
Function SetConsent(bool abIsConsent)          ; Set consent status
```

**Example:**
```papyrus
thread.AddContext("MyMod_Rape")      ; Add custom context
if thread.HasContext("MyMod_Rape")   ; Check it
  ; React to context
endIf
```

---

## Interaction Detection

The framework uses 3D physics data to detect what actors are actually doing. This is more reliable than just looking at animation tags.

### Interaction Types

```papyrus
int CTYPE_ANY            = -1   ; Any interaction
int CTYPE_Vaginal        = 1    ; Being penetrated (vagina)
int CTYPE_Anal           = 2    ; Being penetrated (anal)
int CTYPE_Oral           = 3    ; Licking/sucking partner
int CTYPE_Grinding       = 4    ; Being grinded against
int CTYPE_Deepthroat     = 5    ; Penis deep in throat
int CTYPE_Skullfuck      = 6    ; Head penetrated
int CTYPE_LickingShaft   = 7    ; Licking partner's shaft
int CTYPE_FootJob        = 8    ; Using foot on partner
int CTYPE_HandJob        = 9    ; Using hand on partner
int CTYPE_Kissing        = 10   ; Kissing partner
int CTYPE_Facial         = 11   ; Face in front of penis
int CTYPE_AnimObjFace    = 12   ; Mouth near anim object
int CTYPE_SuckingToes    = 13   ; Mouth near partner's toes
```

### Interaction Functions

```papyrus
; Check if collision data is available
bool Function IsInteractionRegistered()

; Get all interaction types involving an actor
int[] Function GetInteractionTypes(Actor akPosition, Actor akPartner)

; Check if specific interaction exists
bool Function HasInteractionType(int aiType, Actor akPosition, Actor akPartner)

; Get partner(s) for a specific interaction type
Actor Function GetPartnerByType(Actor akPosition, int aiType)
Actor[] Function GetPartnersByType(Actor akPosition, int aiType)

; Reverse lookups (who is doing TO this actor)
Actor Function GetPartnerByTypeRev(Actor akPartner, int aiType)
Actor[] Function GetPartnersByTypeRev(Actor akPartner, int aiType)

; Get interaction data as string
string Function GetInteractionString(Actor akPosition)
  ; Returns comma-separated CTYPEs with prefixes:
  ; "a" = active (giving), "p" = passive (receiving)
  ; Example: "aVaginal,pOral" = giving vaginal, receiving oral

; Get velocity of interaction (may be negative)
float Function GetVelocity(Actor akPosition, Actor akPartner, int aiType)
```

**Example:**
```papyrus
Actor victim = thread.GetActors()[0]
if thread.HasInteractionType(CTYPE_Vaginal, victim, none)
  ; This actor is being penetrated vaginally
  Actor partner = thread.GetPartnerByType(victim, CTYPE_Vaginal)
endIf
```

---

## Thread Time & Actors

### Time Information

```papyrus
float Function GetTime()  ; Seconds since thread started
```

### Getting Actors

```papyrus
Actor[] Function GetPositions()     ; All participating actors
bool Function HasPlayer()           ; Is player in this scene?
bool Function HasActor(Actor akActor)  ; Is this actor in the scene?
int Function GetPositionIdx(Actor akActor)  ; Get actor's position index
int Function GetNthPositionSex(int n)       ; Get sex of actor at index n
int[] Function GetPositionSexes()   ; Get all actor sexes
```

---

## Hooks: Receiving Events

Hooks let your mod react to animation events asynchronously.

### Global Hooks (Mod Events)

Register for a mod event to receive all events of that type from all scenes:

```papyrus
RegisterForModEvent("HookAnimationStart", "OnAnimStart")

Event OnAnimStart(int aiThreadID, bool abHasPlayer)
  SexLabThread thread = SexLab.GetThread(aiThreadID)
  ; React to scene starting
EndEvent
```

### Local Hooks (Per-Scene)

Pass a hook name when starting a scene, then register for hook events with that name:

```papyrus
SexLabThread thread = SexLab.StartScene(actors, tags, asHook="MyHook")

RegisterForModEvent("HookAnimationStart_MyHook", "OnMySceneStart")

Event OnMySceneStart(int aiThreadID, bool abHasPlayer)
  ; This only fires for scenes started with asHook="MyHook"
EndEvent
```

### Available Hook Events

All hooks pass `(int aiThreadID, bool abHasPlayer)` as parameters:

- **HookAnimationStart** - Animation has started
- **HookAnimationEnd** - Animation fully terminated
- **HookLeadInStart** - LeadIn animation started
- **HookLeadInEnd** - LeadIn animation ended
- **HookStageStart** - Animation stage started
- **HookStageEnd** - Animation stage completed
- **HookOrgasmStart** - Actor reached final stage
- **HookOrgasmEnd** - Final stage completed
- **HookAnimationChange** - Animation changed via hotkey
- **HookPositionChange** - Actor positions changed
- **HookActorsRelocated** - Actors received new alignment
- **HookActorChangeStart** - ChangeActors() called
- **HookActorChangeEnd** - Actor replacement completed

---

## Blocking Hooks

For time-critical operations, use blocking hooks by extending `SexLabThreadHook`:

```papyrus
ScriptName MyBlockingHook extends SexLabThreadHook

; Implement required functions
Event OnAnimationStart(SexLabThread akThread)
  ; This blocks the thread until the event returns
  ; Use sparingly as it impacts gameplay
EndEvent
```

Then register and unregister:

```papyrus
SexLabFramework Property SexLab Auto
SexLab.RegisterHook(MyHook)
SexLab.UnregisterHook(MyHook)
```

---

## Actor Functions

Query and manipulate actor information.

### Actor Status

```papyrus
bool Function IsActorActive(Actor ActorRef)
  ; Is this actor currently in an animation?
```

### Actor Sex (Gender)

```papyrus
int Function GetSex(Actor akActor)
  ; Returns: 0=Male, 1=Female, 2=Futa, 3=CreatureMale, 4=CreatureFemale

int[] Function GetSexAll(Actor[] akPositions)
  ; Get sex array for multiple actors
```

**Force an actor's sex for SexLab purposes:**

```papyrus
Function TreatAsSex(Actor akActor, int aiSexTag)           ; 0=Male, 1=Female, 2=Futa
Function TreatAsMale(Actor ActorRef)
Function TreatAsFemale(Actor ActorRef)
Function TreatAsFuta(Actor ActorRef)
Function ClearForcedSex(Actor akActor)                     ; Reset forced sex
```

### Actor Counting

Get counts of different actor types in an array:

```papyrus
int[] Function CountSexAll(Actor[] akPositions)
  ; Returns [males, females, futas, crt_males, crt_females]

int Function CountMale(Actor[] akPositions)
int Function CountFemale(Actor[] akPositions)
int Function CountFuta(Actor[] akPositions)
int Function CountCreatures(Actor[] akPositions)
int Function CountCrtMale(Actor[] akPositions)
int Function CountCrtFemale(Actor[] akPositions)
```

### Actor Validation

```papyrus
int Function ValidateActor(Actor ActorRef)
```

**Returns:**
- `1` - Valid
- `-1` - Actor does not exist (is None)
- `-2` - Actor from disabled race
- `-10` - Already in a SexLab animation
- `-11` - Forbidden from SexLab animations
- `-12` - 3D not loaded
- `-13` - Dead
- `-14` - Disabled (model or AI)
- `-15` - Flying
- `-16` - On mount
- `-17` - Creature but creatures disabled
- `-18` - Unsupported creature race

---

## System Functions

### Get Framework Version

```papyrus
int Function GetVersion()              ; Returns version as integer
String Function GetStringVer()         ; Returns "X.Y.Z.W" format
```

### System Properties

```papyrus
bool property Enabled                  ; Is SexLab enabled?
bool property IsRunning                ; Are there active scenes?
int property ActiveAnimations          ; How many scenes running?
bool property AllowCreatures           ; Are creatures enabled?
bool property CreatureGenders          ; Use creature genders?
```

### Get Thread

```papyrus
SexLabThread Function GetThread(int aiThreadID)
  ; Get thread by ID

SexLabThread Function GetThreadByActor(Actor akActor)
  ; Get most recent thread for an actor
```

---

## Actor Control

Control and query specific actors within a running scene.

### Submissive Status

```papyrus
bool Function GetSubmissive(Actor akActor)       ; Is this actor submissive?
Function SetIsSubmissive(Actor akActor, bool abIsSubmissive)  ; Set submissive status
Actor[] Function GetSubmissives()                ; Get all submissive actors
```

### Enjoyment & Arousal

```papyrus
int Function GetEnjoyment(Actor ActorRef)       ; Get current enjoyment level
Function SetEnjoyment(Actor ActorRef, int aiSet)  ; Set enjoyment to value
Function AdjustEnjoyment(Actor ActorRef, int AdjustBy)  ; Adjust enjoyment
Function ModEnjoymentMult(Actor ActorRef, float afSet, bool bAdjust = false)
  ; Modify enjoyment rate (2.0 = double, 0.0 = stop)
```

### Orgasms

```papyrus
Function DisableOrgasm(Actor ActorRef, bool OrgasmDisabled = true)
Function ForceOrgasm(Actor ActorRef)
bool Function IsOrgasmAllowed(Actor ActorRef)
Actor[] Function CanBeImpregnated(Actor akActor, bool abAllowFutaImpregnation, \
    bool abFutaCanPregnate, bool abCreatureCanPregnate)
  ; Returns actors that had vaginal contact with this actor
```

### Voice & Expressions

```papyrus
Function SetActorVoice(Actor akActor, String asVoice, bool abForceSilent)
String Function GetActorVoice(Actor akActor)
Function SetActorExpression(Actor akActor, String asExpression)
String Function GetActorExpression(Actor akActor)
```

### Equipment & Stripping

```papyrus
Function SetCustomStrip(Actor akActor, int aiSlots, bool abWeapon, bool abApplyNow)
Function ResetCustomStrip(Actor akActor)
bool Function IsUndressAnimationAllowed(Actor akActor)
Function SetIsUndressAnimationAllowed(Actor akActor, bool abAllowed)
bool Function IsRedressAllowed(Actor akActor)
Function SetIsRedressAllowed(Actor akActor, bool abAllowed)
```

### Strapon Usage

```papyrus
Function SetStrapon(Actor ActorRef, Form ToStrapon)
Form Function GetStrapon(Actor ActorRef)
bool Function IsUsingStrapon(Actor ActorRef)
```

### Actor Positioning

```papyrus
int PATHING_DISABLE = -1   ; Always teleport
int PATHING_ENABLE = 0     ; User config (default)
int PATHING_FORCE = 1      ; Always try to walk

Function SetPathingFlag(Actor akActor, int aiPathingFlag)
  ; Set if actor walks or teleports to center
```

### Actor Status

```papyrus
int Function GetActorSex(Actor akActor)   ; Get sex of this actor in scene
```

### Equipment & Stripping

```papyrus
; Control which items are stripped
Function WriteStrip(Form akItem, bool abNeverStrip)
Function EraseStrip(Form akItem)
Function EraseStripAll()
int Function CheckStrip(Form akItem)   ; -1=Never, 0=No Info, 1=Always

; Convenience wrappers
Function MakeNoStrip(Form ItemRef)
Function MakeAlwaysStrip(Form ItemRef)
Function ClearStripOverride(Form ItemRef)
Function ResetStripOverrides()

bool Function IsNoStrip(Form ItemRef)
bool Function IsAlwaysStrip(Form ItemRef)
bool Function IsStrippable(Form ItemRef)

; Strip/unstrip actors
Form[] Function StripActor(Actor ActorRef, Actor VictimRef = none, \
    bool DoAnimate = true, bool LeadIn = false)
Function UnstripActor(Actor ActorRef, Form[] Stripped, bool IsVictim = false)
```

### Cum Effects

```papyrus
int FX_ALL = -1
int FX_VAGINAL = 0
int FX_ANAL = 1
int FX_ORAL = 2

Function AddCumFx(Actor akActor, int aiType)
  ; Add cum visuals to actor (type=-1 for all)

Function RemoveCumFx(Actor akTarget, int aiType)
  ; Remove cum visuals from actor

int Function CountCumFx(Actor akActor, int aiType)
  ; Get count of active cum effects
```

### Array Utilities

```papyrus
Actor[] Function MakeActorArray(Actor Actor1 = none, Actor Actor2 = none, \
    Actor Actor3 = none, Actor Actor4 = none, Actor Actor5 = none)
  ; Create actor array from individual actors

String Function ActorName(Actor ActorRef)
  ; Get friendly name of actor
```

---

## Constants & Properties

### Thread Status Constants

```papyrus
int STATUS_UNDEF = 0
int STATUS_IDLE = 1
int STATUS_SETUP = 2
int STATUS_INSCENE = 3
int STATUS_ENDING = 4
```

### Furniture Preference Constants

```papyrus
int FURNITURE_DISABLE = 0
int FURNITURE_ALLOW = 1       ; Default
int FURNITURE_PREFER = 2
```

### Interaction Type Constants

See Interaction Detection section above for all `CTYPE_*` constants.

---

## Best Practices

### 1. Always Check if API is Ready

```papyrus
if !SexLabUtil.SexLabIsReady()
  return
endIf
```

### 2. Check Return Values

```papyrus
SexLabThread thread = SexLab.StartScene(actors, tags)
if !thread
  ; No valid animation found
  return
endIf
```

### 3. Use Local Hooks for Better Performance

```papyrus
; Instead of checking every scene globally:
thread = SexLab.StartScene(actors, tags, asHook="MyHook")

; Only receive events for your scene
RegisterForModEvent("HookAnimationStart_MyHook", "OnStart")
```

### 4. Handle Status Changes

```papyrus
if thread.GetStatus() == thread.STATUS_INSCENE
  ; Safe to call most functions
endIf

if thread.GetStatus() == thread.STATUS_ENDING
  ; Most functions disabled, data still available
endIf
```

### 5. Cache Array Results

```papyrus
; Don't call repeatedly:
String[] scenes = thread.GetPlayingScenes()  ; Cache this
int i = 0
While (i < scenes.Length)
  ; Use cached scenes
  i += 1
EndWhile
```

---

## Common Tasks

### Start a Simple Two-Actor Scene

```papyrus
**Example:**
```papyrus
Actor player = Game.GetPlayer()
Actor npc = MyNPCRef
Actor npc2 = MyOtherNPCRef

SexLabThread thread = SexLabUtil.StartSceneQuick(player, npc, npc2)
```
```

### React to Scene Completion

```papyrus
RegisterForModEvent("HookAnimationEnd", "OnSceneEnd")

Event OnSceneEnd(int aiThreadID, bool abHasPlayer)
  if abHasPlayer
    Debug.MessageBox("Scene with player finished!")
  endIf
EndEvent
```

### Get Scene Details

```papyrus
SexLabThread thread = SexLab.GetThreadByActor(player)
if thread
  Debug.Notification("Current scene: " + thread.GetActiveScene())
  Debug.Notification("Stage: " + thread.GetActiveStage())
endIf
```

### Check Interaction Types

```papyrus
Actor[] actors = thread.GetPositions()
if thread.HasInteractionType(CTYPE_Vaginal, actors[0], none)
  Debug.Notification(actors[0].GetName() + " is being penetrated vaginally!")
endIf
```

---

## Additional Resources

- See [SexLabThreadHook.psc](https://github.com/Scrab-NSFW/SexLab/blob/animation/Source/Scripts/SexLabThreadHook.psc) for blocking hook implementation
- See [Converting Animations](/slsb/converting-animations/) for animation definitions
- Visit the [Discord](https://discord.gg/JPSHb4ebqj) for API support
