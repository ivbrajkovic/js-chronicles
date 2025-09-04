<p align="center">
  <img src="./asset/Plain Black Paperback Book Lying On Archaic Tablet.png" width="800" />
</p>

# The Scroll of Many Runes: The Interface Segregation Principle ✨

##### In the realm of code, burdened tomes sow chaos. Give each class only the scroll it was meant to wield, and escape the curse of bloated interfaces.

### Table of Contents

- [A Whisper of Precision](#intro)
- [The Curse of the Overburdened Tome](#problem)
- [The Wisdom of Segregated Scrolls](#principle)
- [The Scribe’s Remedy of Lean Design](#refactor)
- [Harmony Restored with the Wisdom of ISP](#benefits)
- [The Developer’s Quest for Lean Design](#conclusion)

---

<a id="intro"></a>

## A Whisper of Precision 🪶

Within the constellation of **SOLID principles** there lies a star of quiet wisdom—the **Interface Segregation Principle (ISP)**, a gentle guide that whispers of clarity and graceful design.

> _“Clients should not be forced to depend upon interfaces that they do not use.”_ — [Robert C. Martin](https://en.wikipedia.org/wiki/Robert_C._Martin)

Ignoring this wisdom leads to systems bound by **large interfaces** that try to cover every possible role. It may seem convenient at first, but it forces classes to bear methods they will never use.

Imagine a master scribe chained to a tome containing every rune ever etched—_healing charms_, _dwarven forges_, _alchemists' brews_. To inscribe a single charm, he must bear the weight of all. Then the sage of ISP spoke: _"Give the scribe only the scroll he needs."_

---

<a id="problem"></a>

## The Curse of the Overburdened Tome 📜

Our tale begins with a single scroll, ambitiously crafted to bind all magical beings—_wizards_, _forgers_, and _druids_. But each was condemned to bear the whole tome, even when only one rune was needed.

```typescript
// A bloated interface trying to serve all magical beings
interface ICreature {
  castSpell(): void;
  forgeWeapon(): void;
  brewPotion(): void;
}
```

When an `ElvenMage` tries to cast a spell, she is burdened with methods for forging weapons and brewing potions that are irrelevant to her craft.

```typescript
class ElvenMage implements ICreature {
  castSpell(): void {
    console.log('✨ Casting arcane fireball...');
  }
  forgeWeapon(): void {
    throw new Error('Elves do not forge weapons! ❌');
  }
  brewPotion(): void {
    throw new Error('Only druids brew potions! ❌');
  }
}
```

Meanwhile, when a `DwarvenBlacksmith` forges a weapon, he is weighed down by spells and potions he was never meant to wield.

```typescript
class DwarvenBlacksmith implements ICreature {
  forgeWeapon(): void {
    console.log('⚒️ Forging a dwarven axe...');
  }
  castSpell(): void {
    throw new Error('Dwarves do not cast spells! ❌');
  }
  brewPotion(): void {
    throw new Error('Potions are no concern of dwarves! ❌');
  }
}
```

The `ElvenMage` and `DwarvenBlacksmith` are forced to wield runes they were never meant to master. Burdened by unused powers, they bring confusion and fragility to the realm of code. Each change to `ICreature` unleashes a ripple of chaos. This is _the curse of the bloated interface_.

---

<a id="principle"></a>

## The Wisdom of Segregated Scrolls 🔮

To lift the curse, ISP brings a clear remedy. Rather than binding every rune into one _heavy tome_, it guides us to inscribe many smaller, _focused scrolls_. Each scroll carries only what is needed for a **single purpose**.

> _"The dependency of a module on another should be based on the smallest possible interface."_ — a common interpretation of this principle’s wisdom.

At its core, ISP is about **clarity of contract**. A class should depend only on what serves its craft, _nothing more_. By doing so, we reduce unnecessary coupling and protect the system from _cascading side effects_.

### The Pillars of ISP 🏛️

To wield this principle with clarity, remember these guiding runes:

1. **One Rune, One Role**—each interface should serve a single purpose, as a scroll carries but one spell. Simplicity guards against confusion.
2. **No Empty Promises**—a class should never claim powers it cannot wield. False contracts weaken both trust and design.
3. **Clients Choose Their Scrolls**—take only the roles you need, as a mage takes only the charm fit for the spell. True freedom lies in precision.
4. **Combine, Don’t Inflate**—when many abilities are needed, weave scrolls together. Unity through composition is stronger than a bloated tome.

### The Overburdened Tome vs. The Focused Scrolls ⚖️

Behold the contrast between the cursed tome and the enlightened scrolls:

| Before ISP (The Bloated Tome) ❌            | After ISP (Focused Scrolls) ✅         |
| ------------------------------------------- | -------------------------------------- |
| One giant, unwieldy interface.              | Small, role-specific interfaces.       |
| Clients must implement unused methods.      | Clients only implement what they need. |
| Changes ripple across unrelated clients.    | Interfaces are stable and decoupled.   |
| Code is polluted with error-throwing stubs. | Contracts are honest and purposeful.   |

---

<a id="refactor"></a>

## The Scribe’s Remedy of Lean Design ✍️

To restore balance, we must craft our interfaces with care, ensuring that each class only bears the scrolls it truly needs. Let us rewrite our magical beings with the wisdom of ISP.

### Step 1: Unbind the Overburdened Tome 🔍

First, we identify the _distinct roles_ currently trapped within `ICreature` and segregate them into their own interfaces.

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

### Step 2: Forge Focused Roles 🧙‍♂️

With the new scrolls in hand, each class now implements only the methods true to its nature. By splitting the contract, the unnecessary weight is gone—_no false promises_, _no irrelevant powers_. Each being now acts with clarity.

```typescript
class ElvenMage implements ISpellCaster {
  castSpell(): void {
    console.log('✨ Casting arcane fireball...');
  }
}

class DwarvenBlacksmith implements IWeaponForger {
  forgeWeapon(): void {
    console.log('⚒️ Forging a dwarven axe...');
  }
}

class ForestDruid implements IPotionBrewer {
  brewPotion(): void {
    console.log('⚗️ Brewing a healing potion...');
  }
}
```

Now, each class implements only the interfaces true to its nature. The `ElvenMage` casts spells unburdened, the `DwarvenBlacksmith` forges freely, and the `ForestDruid` brews potions without distraction.

### Step 3: Weave Scrolls for Complex Beings 🧩

Some beings in our magical realm possess more than one talent. ISP shines here not by inflating a single scroll, but by weaving multiple focused scrolls together—_each one defining a distinct ability_.

```typescript
class DwarvenWarrior implements IWeaponForger, ISpellCaster {
  forgeWeapon(): void {
    console.log('⚒️ Forging a battle axe...');
  }
  castSpell(): void {
    console.log('✨ Unleashing a dwarven battle rune...');
  }
}
```

### Step 4: Grant Clients Only What They Need 🤝

Those who summon spells need not know of forges or potions. A client depends only on the `ISpellCaster` contract, never on burdens beyond its purpose.

```typescript
function summonSpell(caster: ISpellCaster) {
  caster.castSpell();
}

const mage = new ElvenMage();
const warrior = new DwarvenWarrior();

summonSpell(mage); // ✨ Casting arcane fireball...
summonSpell(warrior); // ✨ Unleashing a dwarven battle rune...
```

---

<a id="benefits"></a>

## Harmony Restored with the Wisdom of ISP 🌟

With the tome divided, our system shines again. The benefits of this newfound clarity are manifold:

1.  **Cohesion & Decoupling** 🔗  
    Classes depend only on the methods they truly need, reducing unnecessary coupling. Each interface holds only the runes of its own craft, never burdening others with foreign scripts.

2.  **Maintainability & Readability** 📜  
    A class’s responsibilities are clear at a glance, making code easier to read, maintain, and debug. Like labels on well-ordered scrolls, its purpose is never in doubt.

3.  **Flexibility & Growth** 🔄  
    Small, focused interfaces are simple to extend and recombine. New roles—such as `IHealer`—can be inscribed without disturbing the ancient archives.

4.  **Simpler Testing** ✅  
    Tests can focus on exactly what a class does, without stubbing out irrelevant methods. Only the needed scrolls are unrolled for practice, sparing the scribe from needless incantations.

---

<a id="conclusion"></a>

## The Developer’s Quest for Lean Design 🗺️

Mastering the ISP is like a scribe learning _brevity_—true strength comes not from writing every spell into one scroll, but from crafting the exact scroll needed for the task. It is the path from complexity to clarity.

### Wisdom for the Road Ahead 🧭

- **Design for Roles** — let each interface reflect the true nature of its class.
- **Favor Composition** — weave scrolls together to grant greater powers.
- **Stay Focused** — avoid inscribing methods that don’t serve the craft.
- **Refactor Fearlessly** — when you find a bloated tome, divide it into focused scrolls. The clarity is worth the effort.

Scribe your scrolls with intent, where each rune serves its purpose.

_May your interfaces stay lean and purposeful, guiding your creations toward greatness in the realm of code._ ✨🚀
