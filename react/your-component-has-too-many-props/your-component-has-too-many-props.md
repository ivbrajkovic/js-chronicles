<p align="center">
  <img src="./images/Contrasting backpacks on wooden surface-compressed.png" width="800" />
    <p align="center" style="font-size: 0.8em; color: gray; margin-top: -10px;">
    Artwork created using ChatGPT
  </p>
</p>

# Your React Component Has Too Many Props

##### **A love letter to selfish components, strategic laziness, and saying no to prop-driven chaos.**

### Table of Contents

- [The One That Started It All](#the-one-that-started-it-all)
- [The Slow Death by a Thousand Props](#the-slow-death-by-a-thousand-props)
- [Uncle Bob Walks Into a React Component](#uncle-bob-walks-into-a-react-component)
- [What "Selfish" Actually Means (It's Not Therapy)](#what-selfish-actually-means-its-not-therapy)
- [The Great Unlearning: From Configuration to Composition](#the-great-unlearning-from-configuration-to-composition)
- [But It's More Code! (And Why That's Fine, Actually)](#but-its-more-code-and-why-thats-fine-actually)
- [This Party Scales, I Promise](#this-party-scales-i-promise)
- [The "Call an Ambulance" Checklist](#the-call-an-ambulance-checklist)
- [The Part Where We Feel Things](#the-part-where-we-feel-things)

## The One That Started It All

Let me tell you about the time I almost quit React.

There I was, 2 AM, coffee cold, eyes burning, staring at a component with **65 props**.

**Sixty‑five.**

Some of them were booleans that controlled other booleans. Some were strings that only made sense if another prop was a specific value. One poor prop — I'll never forget this — was called `displayVariant` and accepted 12 different values, each triggering a completely different layout.

But the real masterpiece was the _family_ of props. You know the pattern:

```tsx
const StreamingPlatformSelector = ({
  platformName,
  searchQuery,
  setSearchQuery,

  huluSearchResults,
  huluTotalCount,
  selectedHuluBrands,
  setSelectedHuluBrands,
  huluOffset,
  setHuluOffset,
  isHuluNextPageLoading,

  peacockSearchResults,
  peacockTotalCount,
  selectedPeacockBrands,
  setSelectedPeacockBrands,

  // ...repeat for six more platforms (Paramount, Discovery, Apple, Flickster, Netflix, Amazon)
  // Each with its own search results, total count, selected items, offset, and loading state

  isAdvancedMode,
  regionCode,
  isEditFlow,
  selectedDataSourceRadio,
  setSelectedDataSourceRadio,
}: TStreamingPlatformSelectorProps) => {
  // Component implementation...
};
```

The real version had 65 props. I counted twice. Then I stared at the wall for a while.

That component worked. I'll give it that. It rendered everything from user avatars to full‑page dashboards. It was flexible. It was powerful. It was also unholy.

> **The worst part?** It wasn't written by a junior developer who didn't know better. It was written by someone experienced, under real deadlines, solving real problems one reasonable prop at a time.

And that's when I discovered something counterintuitive:

> **Flexibility is not a virtue. It's a liability waiting to happen.**

## The Slow Death by a Thousand Props

It never starts with 65 props. It starts so innocently.

```tsx
<Avatar src={user.profilePic} />
```

Beautiful. Clean. This component has _no opinions_ about your life choices. It just renders a dang picture.

Then someone says: "Can we make it a little bigger?"

```tsx
<Avatar src={user.profilePic} size="md" />
```

Fine. Whatever. Size prop is reasonable. We're still friends.

Then product chimes in: "We need to show online status."

```tsx
<Avatar src={user.profilePic} size="md" showStatus status="online" />
```

Okay, getting a little crowded, but I see where this is going.

Then design: "Admins should have a badge."

```tsx
<Avatar src={user.profilePic} size="md" showStatus status="online" showBadge badgeContent="Admin" />
```

Then engineering: "Can we add tooltips?"

Then QA: "What about fallback initials?"

Then marketing: "We need different badge colors for different roles. Also the status dot should be movable. Also can it animate? Also—"

And suddenly, you're here:

```tsx
<Avatar
  src={user.profilePic}
  size="md"
  showStatus
  status="online"
  statusPosition="bottom-right"
  showBadge
  badgeContent="Admin"
  badgeColor="red"
  showTooltip
  tooltip={user.displayName}
  fallbackInitials
  shape="circle"
  animateOnHover
  ringColor="blue"
  ringWidth={2}
/>
```

**This is not an avatar anymore.**

This is a Swiss Army knife that's pretending to be an avatar. It's a tiny rendering engine with its own internal rulebook. It's making decisions it has no business making:

- Whether to show status → **product decision**
- How roles are displayed → **domain logic**
- Where the badge goes → **layout concern**
- What the tooltip says → **content decision**

Your avatar component now knows more about your business rules than your product manager does. That's not flexibility. That's **responsibility creep**.

And here's the worst part: nobody did anything wrong. Every single change was reasonable. But reasonable changes, stacked on top of each other, create unreasonable complexity.

## Uncle Bob Walks Into a React Component

Remember the Single Responsibility Principle? From Uncle Bob? The one that says:

> _“A class should have only one reason to change.”_ — [Robert C. Martin](https://en.wikipedia.org/wiki/Robert_C._Martin)

Yeah, that applies to components too.

Let's think about our `Avatar` monstrosity. How many reasons does it have to change?

1. **Avatar styling changes** — sure, that's its job.
2. **Product decides online status should look different** — wait, why does Avatar care about that?
3. **Admin badge gets a new color** — oh no.
4. **Tooltip positioning changes across the app** — we're in trouble.
5. **Business decides users can have multiple roles** — 🔥

Every time a _business rule_ changes, your UI component changes. That's the opposite of separation of concerns. That's **concern entanglement**. That's how you spend your Friday night debugging why changing a badge color broke the tooltip animation.

## What "Selfish" Actually Means (It's Not Therapy)

When I say your component should be _selfish_, I don't mean it should refuse to share its toys or talk behind other components' backs.

I mean:

> **A selfish component protects its boundaries like a dragon protects its hoard.**

A selfish component:

- **Knows what it is** — "I render avatars. That's it. That's the whole thing."
- **Refuses outside opinions** — "You want a badge? Put it next to me. I'm not your parent."
- **Stays in its lane** — "I handle layout and structure. Content is YOUR problem, caller."

For an avatar, selfishness looks like:

- **It owns** — the image tag, the sizing, the border radius, the alt text fallback
- **It does NOT own** — status indicators, badges, tooltips, business logic, or your life choices

Here's the rule I tattooed on my brain (metaphorically, I'm scared of needles):

> **Content belongs to callers. Structure belongs to components.**

Say it with me now.

## The Great Unlearning: From Configuration to Composition

Here's where we unlearn everything React tutorials taught us about "flexible components."

Most developers think:

```
Flexible component = many props = good
```

But that's wrong. That's **configuration thinking**. You're building a component that _configures itself_ based on flags.

Instead, think **composition**:

```tsx
<Avatar size="md">
  <img src={user.profilePic} alt={user.displayName} />

  {user.isOnline && <StatusIndicator />}

  {user.role === 'admin' && <Badge color="red">Admin</Badge>}
</Avatar>
```

Look at what disappeared:

- ❌ `showStatus` — gone. Just put the status inside or don't.
- ❌ `showBadge` — gone. Just render the badge or don't.
- ❌ `tooltip` — gone. Wrap the whole thing in a tooltip component.
- ❌ `statusPosition` — gone. That's the status component's problem now.
- ❌ All those conditional rules — gone. They're now _visible at the call site_.

**This is not less code. It's more honest code.**

Every decision is right there, in plain sight, not hidden behind a prop API that nobody understands without reading component internals.

Rich Hickey (Clojure legend, smarter than all of us) said:

> _“Design means to break things apart in such a way that they can be put back together.”_ — [Rich Hickey](https://en.wikipedia.org/wiki/Rich_Hickey)

We broke it apart. Now let's put it back together.

## But It's More Code! (And Why That's Fine, Actually)

I can hear you already:

> _"But the composition version has more lines! That's less efficient!"_

Let me tell you a secret: **the compiler doesn't care about your line count**. Your colleagues do. Your future self does. The person debugging a production incident at 11 PM on a Saturday? They **really** care.

Code is read far more often than it's written. We optimize for _write-time convenience_ at our peril.

Here's what you actually get with composition:

| Concern             | Configuration (many props)                    | Composition (children) |
| ------------------- | --------------------------------------------- | ---------------------- |
| **Discoverability** | Hidden behind props                           | Visible in JSX         |
| **Change impact**   | Touches component internals                   | Touches only call site |
| **Testing**         | Test combinatorial explosion                  | Test isolated pieces   |
| **Reasoning**       | "What does `displayVariant="detailed"` mean?" | "Oh, it's right there" |

The configuration version _feels_ efficient because you typed less. But you didn't _reduce_ complexity. You **deferred** it. You packed it into a prop API where it will haunt you forever.

As my wise colleague once said: _"Even perfect code is worthless if it can't be changed."_

## This Party Scales, I Promise

You might be thinking: _"Okay, fine for an avatar. But what about REAL components — like that 65‑prop streaming beast?"_

This is where the article becomes something you can actually use tomorrow.

**The Configuration Disaster (what you're probably maintaining right now):**

```tsx
<StreamingPlatformSelector
  platformName={platformName}
  searchQuery={searchQuery}
  setSearchQuery={setSearchQuery}
  huluSearchResults={huluSearchResults}
  huluTotalCount={huluTotalCount}
  selectedHuluBrands={selectedHuluBrands}
  setSelectedHuluBrands={setSelectedHuluBrands}
  huluOffset={huluOffset}
  setHuluOffset={setHuluOffset}
  isHuluNextPageLoading={isHuluNextPageLoading}
  // ... repeat for Peacock, Paramount, Discovery, Apple, Flickster, Netflix, Amazon
  isAdvancedMode={isAdvancedMode}
  regionCode={regionCode}
  isEditFlow={isEditFlow}
  // ... and 30 more props
/>
```

What does this actually _render_? No idea. The props tell me _what data exists_ but not _how it looks_. I have to go read the component to understand the layout.

**The Composition Clarity (how to fix it):**

First, make a **selfish** component for each platform:

```tsx
const PlatformSection = ({ platform, searchQuery, regionCode }) => {
  // All the offsets, loading states, and selection logic live HERE
  // Not leaking into the parent form
  const { results, totalCount, isLoading, selectedItems, toggleItem, loadMore } = usePlatformSearch(
    {
      platform,
      searchQuery,
      regionCode,
    },
  );

  return (
    <Card title={`${platform} Results`}>
      {results.map((item) => (
        <Checkbox
          key={item.id}
          checked={selectedItems.has(item.id)}
          onChange={() => toggleItem(item.id)}
        >
          {item.name}
        </Checkbox>
      ))}
      {isLoading && <Spinner />}
      {totalCount > results.length && <Button onClick={loadMore}>Load more</Button>}
    </Card>
  );
};
```

Then compose them:

```tsx
<StreamingPlatformSelector>
  <SearchInput value={searchQuery} onChange={setSearchQuery} />

  {/* Always-visible platforms */}
  <PlatformSection platform="hulu" searchQuery={searchQuery} regionCode={regionCode} />
  <PlatformSection platform="peacock" searchQuery={searchQuery} regionCode={regionCode} />
  <PlatformSection platform="paramount" searchQuery={searchQuery} regionCode={regionCode} />
  <PlatformSection platform="discovery" searchQuery={searchQuery} regionCode={regionCode} />
  <PlatformSection platform="apple" searchQuery={searchQuery} regionCode={regionCode} />

  {/* Feature-flagged platforms */}
  {isBetaFeatureChecked && (
    <PlatformSection platform="flickster" searchQuery={searchQuery} regionCode={regionCode} />
  )}
  {isNetflixEnabled && (
    <PlatformSection platform="netflix" searchQuery={searchQuery} regionCode={regionCode} />
  )}
  {isAmazonEnabled && (
    <PlatformSection platform="amazon" searchQuery={searchQuery} regionCode={regionCode} />
  )}

  <AdvancedModeToggle />
  <RegionSelector />
</StreamingPlatformSelector>
```

The difference isn't style. It's **visibility**.

I can _see_ what's rendered. I can _see_ the conditionals (Flickster only if beta flag is on). I don't need to open another file. I don't need to remember what `isPeacockNextPageLoading` does. It's just... right there inside its own component.

And when product says "add a new platform"? I write one new `<PlatformSection platform="..."/>` line. I don't add **8 new props** to the parent. I don't touch the parent component at all.

That's not laziness. That's **strategic laziness**. And it's beautiful.

## The "Call an Ambulance" Checklist

Here's how to know your component has gone to the dark side:

🚨 **Adding features always means adding props** — You've built a prop-driven machine, not a component.  
🚨 **Props start interacting** — When `showBadge` only works if `displayVariant="compact"`, you've created a state machine. In props. Help.  
🚨 **Domain logic appears in the component** — If your avatar knows what an admin is, your boundaries have failed.  
🚨 **Usage hides structure** — When you can't visualize the output without reading component code, you've lost.  
🚨 **Prop combinations need explanation** — If you need documentation for "valid prop combinations," you've built something too complicated.  
🚨 **You see repeated clusters of props** — `XSearchResults`, `XTotalCount`, `selectedXItems`, `setSelectedXItems`, `XOffset`, `setXOffset`, `isXNextPageLoading` for eight different X? That's a **reusable abstraction screaming to be born**.

A simple rule to live by:

> **If new use cases require new props, you're solving the problem in the wrong place.**

You shouldn't be teaching your component about the world. The world should be teaching your component — by composing it with other components.

## The Part Where We Feel Things

Here's the thing that took me years to learn:

**Good components aren't the ones that handle everything.**

Good components are the ones that make responsibilities obvious. They don't try to anticipate every variation. They define a boundary and stay clear within it. They say "no" so that your application can say "yes."

**A selfish component isn't a bad teammate.** It's a focused one. It does one job, does it well, and trusts the rest of the system to do its part.

> A good component doesn't try to do everything. It makes it easy to build anything.

Now go forth. Write selfish components. Practice **strategic laziness**. And for the love of all that is holy, when you pass 10 props, at least stop and ask what kind of creature you are creating.

_P.S. — If you're currently maintaining a 65‑prop component, I'm not saying you should quit. But I'm not saying you shouldn't. Stay strong. Refactor one platform at a time. You've got this._

---

_This article was inspired by Daniel Yuschick's brilliant piece on ["selfish" component design](https://www.smashingmagazine.com/2023/01/key-good-component-design-selfishness/) — go read it. Then come back and write better components._

_All code examples in this article are fictional and for educational purposes. Any resemblance to actual codebases — living or dead — is purely coincidental._
