<p align="center">
  <img src="./assets/Plain Black Paperback Book Lying On Archaic Tablet.png" width="800" />
    <p align="center" style="font-size: 0.8em; color: gray; margin-top: -10px;">
    Artwork created using CGDream
  </p>
</p>

# The Scroll of Whispered Runes: The Interface Segregation Principle

##### Bloated interfaces force classes to carry methods they’ll never use. Trade the overweight grimoire for lean scrolls — let every class wield only the runes it was born to cast.

### Table of Contents

- [A Whisper of Precision](#intro)
- [The Curse of the Overburdened Tome](#problem)
- [The Wisdom of Segregated Scrolls](#principle)
- [The Scribe’s Remedy—A Candle-Lit Refactor](#refactor)
- [Harmony Restored with the Wisdom of ISP](#benefits)
- [The Developer’s Quest for Lean Design](#conclusion)

---

<a id="intro"></a>

## A Whisper of Precision 🪶

Among the five stars of **SOLID** constellation, the **Interface Segregation Principle (ISP)** glimmers with quiet power. It murmurs a single, uncompromising truth: _“The smaller the interface, the better.”_

> _“Clients should not be forced to depend upon interfaces that they do not use.”_
> — [Robert C. Martin](https://en.wikipedia.org/wiki/Robert_C._Martin)

_Ignore the whisper_, and classes stagger beneath the weight of irrelevant runes—healing charms mixed with dwarven forge songs, alchemist brews tangled in druidic chants. _Heed the whisper_, and every creature carries only the scrolls it can truly wield.

---

<a id="problem"></a>

## The Curse of the Overburdened Tome 📜

Once upon a refactor, in a kingdom _impatient for speed_, a single unbreakable scroll aspired to bind every magical being.

```typescript
interface ICreature {
  castSpell(): void;
  forgeWeapon(): void;
  brewPotion(): void;
}
```

The `ElvenMage`, graceful beneath moonlit spires, was forced to reject anvil and cauldron, her slender fingers trembling at the touch of alien runes.

```typescript
class ElvenMage implements ICreature {
  castSpell() {
    console.log('✨ Arcane fireball!');
  }
  forgeWeapon() {
    throw new Error('Elves do not forge!');
  }
  brewPotion() {
    throw new Error('Elves do not brew!');
  }
}
```

Meanwhile, the `DwarvenBlacksmith`, soot-covered and proud, had to wave away wand and vial, calloused hands recoiling from foreign incantations.

```typescript
class DwarvenBlacksmith implements ICreature {
  forgeWeapon() {
    console.log('⚒️ Dwarven axe ready!');
  }
  castSpell() {
    throw new Error('Dwarves do not cast!');
  }
  brewPotion() {
    throw new Error('Dwarves do not brew!');
  }
}
```

Every new disciple of `ICreature`—be it _sprite_, _golem_, or _dragon_—was shackled to the same bloated tome. A single errant quill-stroke anywhere in the manuscript rippled outward, breeding shards of fragility. Thus the curse: _the heavier the grimoire, the louder the crash when it falls._

---

<a id="principle"></a>

## The Wisdom of Segregated Scrolls 🔮

ISP teaches us to cleave the colossal grimoire into lean scrolls, each inscribed with but **one honest rune**—the moment the parchment is slit, the ink itself sighs, and the world rearranges _before the candle flickers_.

| Before ISP (Bloated Tome) ❌ | After ISP (Focused Scrolls) ✅ |
| ---------------------------- | ------------------------------ |
| One giant interface          | Many small, role-based ones    |
| Classes littered with stubs  | Classes implement only truth   |
| Changes cascade              | Changes stay local             |
| Runtime errors await         | Compile-time safety reigns     |

## The Scribe’s Remedy—A Candle-Lit Refactor 🕯️

Night settles over the scriptorium. Moths dance around a single candle while the master-scribe unfurls the offending parchment. Quill in hand, he begins the **ritual of segregation**—_each stroke a small liberation_.

### Step 1—Unbind the Roles

With a gentle slice, the bloated contract falls into three slender scrolls.

```typescript
interface ISpellCaster {
  castSpell(): void;
}
interface IWeaponForger {
  forgeWeapon(): void;
}
interface IPotionBrewer {
  brewPotion(): void;
}
```

The ink glows—runes now sing, no longer shout.

### Step 2—Grant Only the Scrolls That Fit

The `ElvenMage` returns, robes whispering, and claims but one scroll:

```typescript
class ElvenMage implements ISpellCaster {
  castSpell() {
    console.log('✨ Arcane fireball!');
  }
}
```

In the forge across town, the `DwarvenBlacksmith` wipes soot from his brow and lifts his own single parchment:

```typescript
class DwarvenBlacksmith implements IWeaponForger {
  forgeWeapon() {
    console.log('⚒️ Dwarven axe ready!');
  }
}
```

No `Error` escapes their lips—their ink is _truthful_.

<p align="center">
  <img src="./asset/Two Photographs Of A Woman And A Man With Long Hai.png" width="800" />
  <p align="center" style="font-size: 0.8em; color: gray; margin-top: -10px;">
    Artwork created using CGDream
  </p>
</p>

### Step 3—Weave Scrolls Together for Complex Heroes

A gust of wind; moon-moths scatter. The `DwarvenRunesmith` enters, rune-hammer slung across his back. He bears two scrolls, yet the candle does not flicker—**many contracts are welcome when every one is slender**.

```typescript
class DwarvenRunesmith implements IWeaponForger, ISpellCaster {
  forgeWeapon() {
    console.log('⚒️ Rune-etched hammer!');
  }
  castSpell() {
    console.log('✨ Rune of fire!');
  }
}
```

### Step 4—Let Clients Depend Only on What They Need

The scribe lifts a final parchment—**the client invocation**—and breathes a spell of _type safety_. Thus no call is made that should not be.

```typescript
function summonSpell(caster: ISpellCaster) {
  caster.castSpell();
}
summonSpell(new ElvenMage()); // ✔️
summonSpell(new DwarvenRunesmith()); // ✔️
// summonSpell(new ForestDruid()); // ❌ Compile-time guardian intervenes
```

The candle steadies; the moth settles. _Fragility banished_, harmony restored.

## Harmony Restored — Gifts of ISP 🌟

The scrolls now rest on their rightful shelves, behold the gifts that follow.

1. **Cohesion & Decoupling**
   Each class knows its craft; dependencies shrink, coupling loosens.
2. **Readability & Maintainability**
   A glance at the `implements` clause tells a **complete**, **truthful story**.
3. **Fearless Refactoring**
   Add `IHealer` or `ITeleporter` tomorrow—no existing scroll is torn, no merge conflicts, no re-compile of unrelated modules.
4. **Effortless Testing**
   Mocks implement only the targeted interface — no dummy stubs, no `throws new Error("not implemented")`, just precise, fast unit tests.
5. **Composable Magic**
   Build legends by composing small scrolls, not inflating monoliths.

## Carrying the Light Forward 🚀

ISP is not cast once, but practiced, etched into every design decision.

- **Design for roles**, not crowds.
- **Favour composition** of interfaces over inheritance of giants.
- **Question every method**: _“Does the client truly need this rune?”_
- **Refactor mercilessly** when you smell the must of a swollen tome.

May your interfaces stay lean, your contracts honest, and your codebase a library with each scroll upon its rightful shelf.

_Scribe on, wizard—one focused rune at a time._ ✨🪶
