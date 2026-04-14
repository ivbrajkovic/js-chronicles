<p align="center">
  <img src="./images/Contrasting backpacks on wooden surface-compressed.png" width="800" />
    <p align="center" style="font-size: 0.8em; color: gray; margin-top: -10px;">
    Artwork created using ChatGPT
  </p>
</p>

# Your Component Has Too Many Props

##### Why prop-heavy React components become harder to read, harder to change, and easier to break.

### Table of Contents

- [A Reasonable Beginning](#a-reasonable-beginning)
- [How It Gets Out of Hand](#how-it-gets-out-of-hand)
- [The Problem Is Not “Too Many Props”](#the-problem-is-not-too-many-props)
- [What “Selfish” Components Actually Mean](#what-selfish-components-actually-mean)
- [From Configuration to Composition](#from-configuration-to-composition)
- [Why This Is Better (Even If It’s More Verbose)](#why-this-is-better-even-if-its-more-verbose)
- [This Pattern Scales](#this-pattern-scales)
- [Practical Heuristics](#practical-heuristics)
- [Closing Thought](#closing-thought)

We tend to equate flexibility with good component design.

In practice, it often leads to the opposite.

Most React components don’t become hard to maintain because they were written poorly. They become hard to maintain because they were made too flexible.

Every new prop feels harmless:

- _“just one more option”_
- _“just support this case”_
- _“just make it reusable”_

Until one day, the component has 30 props — and nobody really understands how it works anymore.

> Large prop APIs are usually a design failure.

Not because props are bad, but because they often hide responsibilities that should have been made explicit.

<a id="a-reasonable-beginning"></a>

## A Reasonable Beginning

Let’s start with something simple:

```tsx
<Avatar src={user.avatarUrl} />
```

Then a small improvement:

```tsx
<Avatar src={user.avatarUrl} size="md" />
```

Still fine.

The component is focused:

- it renders an avatar
- it controls its own presentation

Nothing unusual here.

<a id="how-it-gets-out-of-hand"></a>

## How It Gets Out of Hand

Now the component starts evolving.

```tsx
<Avatar
  src={user.avatarUrl}
  size="md"
  showStatus
  status="online"
  statusPosition="bottom-right"
  showBadge
  badgeContent="Admin"
  badgeColor="red"
  showTooltip
  tooltip={user.name}
  fallbackInitials
  shape="circle"
/>
```

This is not exaggerated. This happens.

At some point, the component stops being an avatar.

> It becomes a profile system controlled by props.

<a id="the-problem-is-not-too-many-props"></a>

## The Problem Is Not “Too Many Props”

> _“A class should have only one reason to change.”_
> — [Robert C. Martin](https://en.wikipedia.org/wiki/Robert_C._Martin)

The number of props is only the symptom.

The real problem is this:

> Props are being used to encode decisions instead of composing structure.

The component is no longer just rendering an avatar.
It is deciding:

- what metadata to show
- how presence is represented
- how content is composed

This leads to predictable issues:

- hidden complexity in prop combinations
- tight coupling between UI and domain logic
- fragile changes as new props get added
- unclear usage — you can’t tell what renders by reading JSX

And most importantly:

> Domain logic starts leaking into the component.

That’s the real failure.

<a id="what-selfish-components-actually-mean"></a>

## What “Selfish” Components Actually Mean

A better mental model is this:

> A component should be selfish.

Not in a negative sense, but in a protective one.

A selfish component:

- defines its responsibility
- protects its boundaries
- refuses decisions that don’t belong to it

For an avatar, that means:

- it owns structure and layout
- it does not decide content

Which leads to:

> Content belongs to callers. Structure belongs to components.

<a id="from-configuration-to-composition"></a>

## From Configuration to Composition

> _“Design means to break things apart in such a way that they can be put back together.”_
> — [Rich Hickey](https://en.wikipedia.org/wiki/Rich_Hickey)

Instead of teaching the component every possible variation, we keep it focused and compose content explicitly:

```tsx
<Avatar size="md">
  <img src={user.avatarUrl} alt={user.name} />

  {user.isOnline && <StatusIndicator />}

  {user.role === 'admin' && <Badge color="red">Admin</Badge>}
</Avatar>
```

What changed:

- no `showStatus`, `showBadge`, or `tooltip`
- no hidden rules inside the component
- no domain logic encoded in props

Everything is visible at the call site.

<a id="why-this-is-better-even-if-its-more-verbose"></a>

## Why This Is Better (Even If It’s More Verbose)

Yes, this is more code.

But it is also:

- more declarative
- easier to reason about
- easier to change
- easier to test

We write code for people to read and understand.

Not for the compiler.

> Even perfect code is worthless if it can’t be changed.

Composition makes change safer because it makes boundaries visible.

<a id="this-pattern-scales"></a>

## This Pattern Scales

This problem does not stop at small components like `Avatar`.

The same pattern shows up in lists, tables, forms, and feature components.

```tsx
<UserList
  users={users}
  showAvatar
  showEmail
  showRole
  selectable
  variant="compact"
/>
```

This looks convenient.

But it hides decisions.

Compare that to:

```tsx
<UserList>
  {users.map((user) => (
    <UserList.Item key={user.id} isSelected={selectedUserIds.has(user.id)}>
      <Avatar size="sm">
        <img src={user.avatarUrl} alt={user.name} />
        {user.isOnline && <StatusIndicator />}
      </Avatar>

      <span>{user.name}</span>
      <span>{user.email}</span>

      {user.role === 'admin' && <Badge>Admin</Badge>}
    </UserList.Item>
  ))}
</UserList>
```

The difference is not just style.

It’s visibility.

<a id="practical-heuristics"></a>

## Practical Heuristics

Some useful signals:

- adding features always means adding props → reconsider
- props start interacting → design smell
- domain logic appears in the component → extract it
- usage hides structure → prefer composition
- prop combinations need explanation → too much responsibility

A simple rule:

> If new use cases require new props, you’re probably solving the problem in the wrong place.

<a id="closing-thought"></a>

## Closing Thought

Good components are not the ones that handle everything.

They are the ones that make responsibilities obvious.

They don’t try to anticipate every variation.
They define a boundary and stay clear within it.

> A good component doesn’t try to do everything.
> It makes it easy to build anything.

Attribution

---

This article is inspired by Daniel Yuschick’s Smashing Magazine [article](https://www.smashingmagazine.com/2023/01/key-good-component-design-selfishness/) on _“selfish”_ component design.
