---
id: exceptions
title: Exceptions catalog
sidebar_label: Exceptions catalog
---

import { PreviewSample } from '../src/PreviewSample'

## HOOKSTATE-101

Happens when new state is created with an initial value, which is a value of another state.

```tsx
const state1 = useState(...)
const state2 = useState(state1.value) // <== Error!
```

If you would like to create a state from a clone of a state, you may do something along this line:

```tsx
const state1 = useState(...)
const state2 = useState(
    // simplest way to clone an object
    JSON.parse(JSON.stringify(state1.value)))
```

## HOOKSTATE-102

Happens when state is set to a new value, which is a value of another state.

```tsx
const state1 = useState(...)
const state2 = useState(...)
state2.set(state1.value) // <== Error!
```

If you would like to set state value from a clone of a state, you may do something along this line:

```tsx
const state1 = useState(...)
const state2 = useState(...)
state2.set(
    // simplest way to clone an object
    JSON.parse(JSON.stringify(state1.value)))
```

## HOOKSTATE-103

Happens when state is read (used) when its underlying promise has not been resolved or rejected yet.

```tsx
const state = useState(new Promise(...))
state.value // <== Error!
state.keys // <== Error!
state.map(...) // <== Error!
state.map(...) // <== OK
```

More information about [asynchronous states](./asynchronous-state).

## HOOKSTATE-104

Happens when state is written (set or merged) when its underlying promise has not been resolved or rejected yet.

```tsx
const state = useState(new Promise(...))
state.set(...) // <== Error!
state.merge(...) // <== Error!
```

More information about [asynchronous states](./asynchronous-state).

## HOOKSTATE-105

Happens when nested state is set to a promise. Nested state does not support asynchronous state separately from a root state.

```tsx
const state = useState({ prop: ... })
state.prop.set(new Promise(...)) // <== Error!
```

More information about [asynchronous states](./asynchronous-state).

## HOOKSTATE-106

Happens when state is set after destroy. Typically it may happen when a component is unmounted but leaves asynchronous operations running, which trigger state updates when awaited finally.

```tsx
const state = createState(...)
state.destroy()
state.set(...) // <== Error!
```

```tsx
const state = useState(...)
React.useEffect(() => {
    setTimeout(() => {
        state.set(...)
    }, 5000)
    // <== Error is not cancelling the timeout when a component is unmounted!
})
```

## HOOKSTATE-107

Happens when state property is read when underlying state value is a primitive value.

```tsx
const state = useState(1)
state.prop // <== Error!

const state = useState({ child: 1 })
state.child.prop // <== Error!
```

## HOOKSTATE-108

Happens when state is serialized to JSON.

```tsx
const state = useState(...)
JSON.stringify(state) // <== Error!
```

You likely intended to serialize state value instead:

```tsx
const state = useState(...)
JSON.stringify(state.value)
```

## HOOKSTATE-109

Happens when state methods instance is serialized to JSON.

```tsx
const state = useState(...)
JSON.stringify(state) // <== Error!
```

You likely intended to serialize state value instead:

```tsx
const state = useState(...)
JSON.stringify(state.value)
```

## HOOKSTATE-120

Happens when a plugin attempts to get its own instance from a state, where the plugin has not been attached before.

Using [Initial](./extensions-initial) plugin as an example:

```tsx
const state = useState(...)
Initial(state) // <== Error!
```

Correct way:

```tsx
const state = useState(...)
state.attach(Initial)
Initial(state)
```

## HOOKSTATE-201

Happens when state property is set via direct assignment.

```tsx
const state = useState(...)
state.prop = 'some value' // <== Error!
```

Correct way:

```tsx
const state = useState(...)
state.prop.set('some value')
```

## HOOKSTATE-202

Happens when state value property is set via direct assignment.

```tsx
const state = useState(...)
state.value.prop = 'some value' // <== Error!
```

Correct way:

```tsx
const state = useState(...)
state.prop.set('some value')
```

## HOOKSTATE-203

Setting prototype for a state is not supported.

```tsx
const state = useState(...)
Object.setPrototypeOf(state, ...) // <== Error!
```

## HOOKSTATE-204

Setting prototype for a state value is not supported.

```tsx
const state = useState(...)
Object.setPrototypeOf(state.value, ...) // <== Error!
```

## HOOKSTATE-205

Preventing extensions for a state is not supported.

```tsx
const state = useState(...)
Object.preventExtensions(state) // <== Error!
```

## HOOKSTATE-206


Preventing extensions for a state value is not supported.

```tsx
const state = useState(...)
Object.preventExtensions(state.value) // <== Error!
```

## HOOKSTATE-207

Defining new property directly on a state is not supported.

```tsx
const state = useState(...)
Object.defineProperty(state, 'prop', ...) // <== Error!
```

You likely intended the following instead:

```tsx
const state = useState(...)
state.prop.set(...)
```

## HOOKSTATE-208

Defining new property directly on a state value is not supported.

```tsx
const state = useState(...)
Object.defineProperty(state.value, 'prop', ...) // <== Error!
```

You likely intended the following instead:

```tsx
const state = useState(...)
state.prop.set(...)
```

## HOOKSTATE-209

Delete operator is not supported for a state object.

```tsx
const state = useState(...)
delete state.prop // <== Error!
```

You likely intended the following instead:

```tsx
import { none, useState } from '@hookstate/core'
const state = useState(...)
state.prop.set(none)
```

## HOOKSTATE-210

Delete operator is not supported for a state value object.

```tsx
const state = useState(...)
delete state[self.value].prop // <== Error!
```

You likely intended the following instead:

```tsx
import { none, useState } from '@hookstate/core'
const state = useState(...)
state.prop.set(none)
```

## HOOKSTATE-211

New operator is not supported for a state object.

```tsx
const state = useState(...)
new state.prop // <== Error!
```

## HOOKSTATE-212

New operator is not supported for a state value object.

```tsx
const state = useState(...)
new state.value.prop // <== Error!
```

## HOOKSTATE-213

State object is not callable.

```tsx
const state = useState(...)
state() // <== Error!
state.prop() // <== Error!
```

## HOOKSTATE-214

State value object is not callable.

```tsx
const state = useState(...)
state.value() // <== Error!
state.value.prop() // <== Error!
```
