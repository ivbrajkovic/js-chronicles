<p align="center">
  <img src="./images/An Enchanted Spellbook Floating In Mid-air-compressed.png" width="800" />
    <p align="center" style="font-size: 0.8em; color: gray; margin-top: -10px;">
    Artwork created using CGDream
  </p>
</p>

# The Spellbook of Scalable Design: The Open-Closed Principle

### Table of Contents

- [A Doorway to Open-Closed Enchantment](#intro)
- [The Heart of the OCP](#core)
- [Implementing the OCP](#implementation)
- [The Apprentice’s First Spell](#example)
- [The Power of Composition](#benefits)
- [Beware of These Traps](#traps)
- [Beyond the Bookstore’s Magic](#beyond)
- [Guardians of the Principle](#guardians)
- [Wisdom for the Journey Ahead](#conclusion)

<a id="intro"></a>

## A Doorway to Open-Closed Enchantment ✨

In the grand tapestry of software design, there exists a constellation of guiding stars known as the **SOLID principles**. Among them, the **Open-Closed Principle (OCP)** rises as the second star, illuminating the realm of code.

> _"The Open-Closed Principle is what separates fragile systems that break with every change from robust systems that adapt and thrive over time."_ — [Robert C. Martin](https://en.wikipedia.org/wiki/Robert_C._Martin)

The OCP offers developers a simple promise—**adaptable**. It whispers, _"Your code should be open for extension but closed for modification."_ — a paradox? Perhaps. But one that unlocks the secret to bringing code to life.

Prepare to witness this principle turn rigid, fragile code into flexible, resilient masterpieces. Gather your wands—or rather, your favorite IDE—and embark on a journey to unravel the ancient magic of scalable code. 🪄

<a id="core"></a>

## The Heart of the OCP 🌟

Imagine a delicate clockwork machine 🕰️, where every gear moves in perfect harmony. Now, what if you wanted to add a second hand to enhance its functionality?

Without OCP, this might mean _dismantling the entire mechanism_, risking chaos and introducing unintended consequences. But with the right design, you can simply attach the new piece without disturbing the rest.

This is the essence of the OCP. It asks us to balance two critical goals:

1. **Be open for extension** — Embrace the ever-evolving needs of our software by allowing new features to be added gracefully.
2. **Be closed for modification** — Protect the sanctity of existing code, ensuring changes don’t ripple through the system like a chaotic spell.

<a id="implementation"></a>

## Implementing the OCP ✨

In TypeScript, embracing OCP means harnessing the power of **abstraction**. Instead of modifying existing code, we **extend functionality** by conjuring new components that seamlessly integrate with the system.

Here’s how TypeScript empowers us to wield this magic:

- **Interfaces** — Define contracts for behavior, allowing us to create multiple implementations that adhere to a common structure.
- **Abstract classes** — Provide reusable foundations, encapsulating common functionality while allowing for specialization through inheritance.
- **Generics** — Enable the creation of flexible, reusable structures that can adapt to different types, promoting code reuse and maintainability.

Through these tools, the OCP becomes more than a design ideal—it ensures that code evolves gracefully without breaking under the weight of change.

<a id="example"></a>

## The Apprentice’s First Spell

Let us begin with a tale of two realities—one where the OCP is but a distant dream, and another where its magic reshapes the very fabric of our code.

### The Problem: A Spellbook Without Structure

Imagine we’re building a magical bookstore that calculates discounts for different customer types: _wizards_, _alchemists_, and _apprentices_. Here’s our first approach, a spell that lacks the finesse of OCP:

```typescript
// 🚫 A rigid spell that requires modification for each new customer type
class DiscountCalculator {
  calculate(customerType: string, amount: number): number {
    if (customerType === 'wizard') {
      return amount * 0.9; // 10% off for wizards
    } else if (customerType === 'alchemist') {
      return amount * 0.85; // 15% off for alchemists
    } else if (customerType === 'apprentice') {
      return amount * 0.95; // 5% off for apprentices
    } else {
      return amount; // No discount
    }
  }
}
```

At first glance, this spell works. But what happens when we introduce new customer types, such as _sorcerers_ or _artificers_?

❌ We have to modify the class each time, violating OCP  
❌ The more conditions we add, the harder the code is to maintain

### The Solution: Casting the Open-Closed Spell 🪄

To wield the power of the OCP, we must design our code with change in mind, leveraging abstraction and polymorphism to create a system that welcomes extension but resists modification.

First, we create a **magical contract**—an interface that defines the behavior of our discount spells:

```typescript
interface DiscountStrategy {
  calculate(amount: number): number;
}
```

Each discount type becomes a **specialized spell** in our grimoire, ready to be invoked when needed:

```typescript
// 💡 Key Insight: Each discount strategy encapsulates its own logic.
// This isolation ensures changes to one discount don't impact others.

class WizardDiscount implements DiscountStrategy {
  calculate(amount: number): number {
    return amount * 0.9; // Mastery deserves recognition
  }
}

class AlchemistDiscount implements DiscountStrategy {
  calculate(amount: number): number {
    return amount * 0.85; // For those who transform knowledge
  }
}
```

Our `DiscountCalculator` now becomes a vessel for any discount strategy, adapting to new spells with ease:

```typescript
class DiscountCalculator {
  constructor(private strategy: DiscountStrategy) {}

  calculate(amount: number): number {
    return this.strategy.calculate(amount);
  }
}
```

Casting a discount spell becomes **effortless** and **elegant**:

```typescript
const wizardCalculator = new DiscountCalculator(new WizardDiscount());
console.log(wizardCalculator.calculate(100)); // Output: 90

const alchemistCalculator = new DiscountCalculator(new AlchemistDiscount());
console.log(alchemistCalculator.calculate(100)); // Output: 85
```

Need a new discount? Just add a new strategy. No modifications required—only the joy of extensions. That’s the **magic** of OCP.

<a id="benefits"></a>

## The Power of Composition 📚

Our magical bookstore has grown. We now offer seasonal promotions, loyalty bonuses, and bundle discounts, but with this expansion comes added complexity.

Rather than creating an ever-growing tangle of conditions, we embrace **composition**—the art of combining simple spells into complex enchantments.

### The Composite Spellbook 🔮

A composite strategy allows us to stack discounts, creating a symphony of savings:

```typescript
class CompositeDiscount implements DiscountStrategy {
  constructor(private strategies: DiscountStrategy[]) {}

  calculate(amount: number): number {
    return this.strategies.reduce(
      (total, strategy) => strategy.calculate(total),
      amount,
    );
  }
}
```

### Crafting New Spells 🪄

Each new discount becomes a self-contained spell, easy to understand and even easier to combine:

```typescript
class SeasonalDiscount implements DiscountStrategy {
  calculate(amount: number): number {
    return amount * 0.8; // 20% off seasonal sales
  }
}

class LoyaltyDiscount implements DiscountStrategy {
  calculate(amount: number): number {
    return amount * 0.95; // 5% off for loyal customers
  }
}
```

### Constructing Dynamic Discounts 🏗️

With the power of composition at our fingertips, we can mix and match strategies on the fly:

```typescript
const specialOffer = new DiscountCalculator(
  new CompositeDiscount([new SeasonalDiscount(), new LoyaltyDiscount()]),
);

console.log(specialOffer.calculate(100)); // Output: 76
```

Adding new discounts—like flash sales or limited-time offers—is as simple as crafting a new strategy and integrating it into `CompositeDiscount`, while the `DiscountCalculator` remains blissfully untouched.

<a id="traps"></a>

## Beware of These Traps 🎭

Even the most skilled wizards occasionally stumble. Here are some common pitfalls to watch for:

- **The Premature Abstraction Curse** — Not every piece of code needs to be extensible from day one. Let the need for flexibility emerge naturally through real requirements.
- **The Everything-Is-A-Strategy Hex** — Sometimes a simple if statement suffices. Don’t overcomplicate straightforward logic with unnecessary abstractions.
- **The Hidden Dependencies Trap** — Ensure your strategies are truly independent. Hidden dependencies between implementations can turn your elegant design into a tangled web.

<a id="beyond"></a>

## Beyond the Bookstore’s Magic 🌍

The patterns we’ve explored within the walls of our magical shop extend far beyond its threshold. In the everyday world, you’ll find OCP powering:

- **E-commerce platforms** that adapt to new payment methods without disrupting the core checkout process. Think Stripe adding Apple Pay support without changing existing payment flows.
- **Content management systems** that welcome new media types while keeping the content creation flow intact. Like WordPress seamlessly supporting new post formats.
- **API gateways** that handle diverse authentication strategies without modifying the underlying service logic. Consider how Auth0 can add social login providers without touching existing authentication flows

Each system stays stable while growing stronger—just like a well-crafted spellbook.

<a id="guardians"></a>

## Guardians of the Principle 🛡️

To truly harness OCP’s power, keep these wisdom scrolls close:

1. **Extend, Don’t Modify** 🌱 — Modifying existing classes instead of extending them can unleash unintended chaos. Always ask, “Can I achieve this by adding a new component instead of altering an existing one?”
2. **Avoid Over-Enchantment** 🌿 — Remember, even the most skilled wizards use their powers sparingly. Excessive abstraction can lead to a labyrinth of complexity, obscuring the very essence of your code.
3. **Favor Composition Over Inheritance** 🏰 — Just as a master alchemist combines simple ingredients to create potent elixirs, we should strive to build complex systems from simple, self-contained components.
4. **Write Clear Incantations** 📜 — Even the most elegant magic is useless if no one can decipher its secrets. Clear, well-documented code ensures that the wisdom of your system remains accessible to all who seek it.

By following these principles, your codebase will become a fortress of adaptability, standing strong in the face of changing requirements. 🏰

<a id="conclusion"></a>

## Wisdom for the Journey Ahead 🔮

As our exploration of OCP concludes, remember—great systems aren’t conjured overnight. They _grow_ through the careful cultivation of principles like OCP. Each feature you add, each pattern you implement, is another brick in the foundation of flexible, maintainable code.

The next time you face a changing requirement, remember the lessons of our magical bookstore. Ask yourself: _"Can I extend rather than modify? Can I create new components rather than alter existing ones?"_

In doing so, you’ll craft systems that don’t just work today but evolve gracefully with tomorrow’s needs. After all, isn’t that the true magic of software design? ✨

As you venture forth, armed with OCP wisdom, remember—you’re not just writing code, but shaping the future of software. May your journey be filled with the joy of extension and the wisdom to resist unnecessary modification. 📚🧙‍♂️
