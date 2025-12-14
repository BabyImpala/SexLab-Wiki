---
layout: default
title: Why P+?
permalink: /slp/why-pplus/
---

# Why Choose SexLab P+?

Already using vanilla SexLab and wondering why you should switch? This guide explains the practical improvements and answers common migration concerns.

---

## Quick Comparison

For a detailed feature-by-feature comparison, see [Feature Comparison](../comparison/).

### The Main Differences

| Aspect | Vanilla SL | SLP+ |
|--------|-----------|------|
| **Animation Registration** | Manual in MCM, ~5-10 min per pack | Automatic at startup, instant |
| **Animation Limit** | 1000 human + 1000 creature | Unlimited |
| **Settings Persistence** | Per-save only | Save-agnostic (auto-loads all saves) |
| **Required Addon Mods** | SLSO, SLAS, SLGP, etc. | Built-in equivalents |
| **Setup Effort** | High (configure each save) | Low (configure once) |

---

## Common Migration Concerns

### "My current setup works fine"

It probably does! But you may be missing benefits:

- **Automation** - No more MCM registration clicking per save
- **Settings persistence** - Configure once, apply to all saves automatically
- **Unlimited content** - More animation packs without technical limits
- **Less addon dependency** - P+ includes features previously requiring 4+ separate mods

### "Will my other mods break?"

**Most mods work fine with P+.** Backwards compatibility is maintained for:
- Scene detection events (HookAnimationStart, HookAnimationEnd, etc.)
- API calls for starting scenes (StartSex, StartSexQuick, etc.)
- Animation tag filtering and selection
- Third-party mod compatibility hooks

**Exceptions exist** - See [Incompatible Mods](../incompatible-mods/) for specific conflicts and workarounds.

### "What about my animations?"

Your SLAL packs work with P+ using **SLSB conversion patches**:
- **Good news:** Most popular packs already have conversions available
- **How it works:** Install both the original SLAL pack (for meshes) and the conversion patch (for registration)
- **Easy:** Just drop files in your mod manager, restart game
- **Automatic:** No MCM registration needed

See [Animation Packs](../animation-packs/) for available conversions or [Converting Animations](../../slsb/converting-animations/) to create your own.

### "Will I lose my MCM settings?"

Yes, you'll need to reconfigure. However:
- P+ uses a different settings format (`Settings.yaml` *and* `SexLab.ini`)
    - `Settings.yaml` saves the user's MCM settings
    - `SexLab.ini` stores the more niche and .dll exclusive settings.
- **Better:** Your settings auto-save and persist across all saves automatically
- **No more setup per save:** Configure once, apply everywhere
- **Easier:** You won't have to redo settings for each new playthrough

See [Settings Reference](../settings-reference/) for all available options.

### "Is there a risk switching between versions?"

**Low risk if you follow migration steps:**

1. Complete your current playthrough on vanilla (or backup that save)
2. Create a completely new save after installing P+
3. Your existing vanilla saves will still work if you reinstall vanilla

We recommend **testing on a new character first** to ensure compatibility with your other mods before committing a main playthrough.

---

## What P+ Includes (Built-in)

P+ consolidates functionality that previously required separate mods:

| Feature | Vanilla SL | SLP+ | Previously Needed |
|---------|-----------|------|-------------------|
| Separate Orgasm System | Manual SLSO mod | Built-in | [SL Separate Orgasm](https://www.loverslab.com/files/file/5929-sexlab-separate-orgasm-se/) |
| Fast Animation Search | Manual SLAS mod | Made redundant | [SLAS](https://www.loverslab.com/files/file/25350-slas-sexlab-anim-searcher/) |
| Grass Clipping Fix | Manual SLGP mod | Made redundant | [SLGP](https://www.loverslab.com/files/file/25455-slgp-sexlab-grass-patch/) |
| Smooth Expressions | Manual mod | Built-in | [SL Smooth Expressions](https://www.loverslab.com/files/file/31585-sexlab-smooth-expressions/) |

---

## Getting Started with P+

Ready to switch? Follow these steps:

1. **Backup your saves** - Always keep backups before major mod changes
2. **Install P+** - See [Installation](../installation/) guide
3. **Convert your animations** - Install SLSB conversion patches (or convert them yourself)
4. **Configure MCM** - Set up your preferences (you only do this once!)
5. **Test on a new character** - Verify everything works before committing

---

## Still Have Questions?

- **General help** - Check [FAQ](../../faq/)
- **Technical issues** - See [Troubleshooting](../../troubleshooting/)
- **Live support** - Join the [Discord](https://discord.gg/JPSHb4ebqj)

---

## See Also

- [Feature Comparison](../comparison/) - Detailed feature-by-feature breakdown
- [Installation Guide](../installation/) - Step-by-step setup
- [Incompatible Mods](../incompatible-mods/) - Known conflicts


## What You Gain

**Performance** - Dramatically faster in all operations  
**Capacity** - Unlimited animation slots  
**Simplicity** - Fewer mods to manage  
**Modern features** - Rebuilt enjoyment/orgasm system  
**Advanced customization** - Fine-tune framework behavior via [Settings Reference](../settings-reference/)

---

## What You Might Lose

**Some mod compatibility** - A few older mods don't work (see [Incompatible Mods](../incompatible-mods/))  
**Voice pack compatibility** - New voice engine requires P+ compatible packs  
**SLAL direct support** - Need conversion patches for animation packs  

---

## Migration Checklist

1. Backup your current setup (MO2 profile or Vortex snapshot)
2. Check your critical mods against the [compatibility list](../incompatible-mods/)
3. Download SLSB conversion patches for your animation packs
4. Follow the [Installation Guide](../installation/)
5. Test on a new save first

---

## Still Unsure?

See the full [Feature Comparison](../comparison/) table, or join the [Discord](https://discord.gg/JPSHb4ebqj) to ask questions.
