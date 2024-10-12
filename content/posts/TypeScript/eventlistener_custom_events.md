---
title: EventListener using custom events
description: How to use custom events with addEventListener in TypeScript
published: 2024-10-12
tags: ['TypeScript']
---
The type of parameter possible as type for the event using addEventListener is dependant on the event (the string) being used. 
If the event is not known, the basic type Event is used by default. This means trying to use a custom type will result in an error in the IDE. 
For example, the following would result in this error « TS2769: No overload matches this call. ».
``` typescript
elm.addEventListener('state-changed', (evt: AddressFormStateEventDetails) => {
  console.log('event details : ', evt);
});
```
The work around it to revert to an Event and cast to the proper type in the callback as follow:\

```typescript
elm.addEventListener('state-changed', (evt: Event) => {
  
  const myEvent = evt as CustomEvent<AddressFormStateEventDetails>;
  if (myEvent) {
    ...          
  }
});
```
### Declaring custom events
With typescript it’s possible to add custom event by augmenting the type « ElementEventMap » defined in the global scope.
\
```typescript
declare global {
    interface ElementEventMap {
        'state-changed': CustomEvent<AddressFormStateEventDetails>
    }
}
```
This code allow us to use the custom event type directly in the listener as follow without getting any error.
```typescript
elm.addEventListener('state-changed', (evt: CustomEvent<AddressFormStateEventDetails>) => {
  ...
});
```