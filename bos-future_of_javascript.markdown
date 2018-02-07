What's New In Javascript?
-------------------------

_Wes Bos, February 7, 2018, 2:30pm EST_

### Introduction

Wes makes JS courses - on React, Node, Grid; he has a podcast at syntax.fm.

We'll focus on parts of the core JS language and a few new DOM items.

### Promises

A promise is an IOU for something that will happen in the future. A promise
kicks off the process and then moves along.  Why do we do this?

**Javascript waits for no one.**  Almost everything in JS is asynchronous.

- Make coffee
- Drink coffee
- Make breakfast
- Eat breakfast

Do these have to be completed in sequence?

```
const coffeePromise = makeCoffee();

coffeePromise.then(coffee => {
    drinkCoffee();
});
```

Most new browser APIs are built on promises or observables.

- fetch() (.then .then)
- axios.get()

Promises solve Christmas tree code / callback hell.  So they're great, right?
Well, _.then_ is still a sort of callback; this is where async / await comes in.

### async + await

This is basically a new syntax around promises.

```
async function animate() {
    await moveTo(50,50);
    await moveTo(50,50);
    await moveTo(50,50);
    await moveTo(50,50);
}

async function go() {
    await sleep(1000);
    const response = await sleep(750);
    console.log(response);
}
```

[More complex example of promises allowing simultaneous execution with await]

### Intersection Observer

This helps us to know when an element is on the screen.  (Animate elements in
'on scroll', play video on 'scroll in', lazy-load only when scrolled, record
views for ads beyond the fold, use with sticky headers)

1. Set up some options

```
const options = {
    root: document.querySelector('.scrollingDiv'),
    rootMargin: '100px',
    threshold: []
}
```

2. Create an empty observer
3. Give it a callback
4. Observe (boxes.foreach(box => observer.observe(box));

### Payment Request API

Every single online store needs to reinvent the checkout form.  This API is a
standardized platform for collecting billing and shipping information from your
users.

Does the browser charge your card?  No!  Is it secure?  Sure :)

It's a glorified autofill.

### getUserMedia()

This is not new.  But Safari didn't care.  But in 2017, with iOS11, we got it.
Along with 'playsinline'. getUserMedia() is promise-based (callbacks).

### Resize Observer

Like intersection observer, reports back when element sizes change.  And this is
the gateway drug to _Element Queries_.

### Support

async + await is available via Babel right now; resize observer is polyfillable
but expensive.

Intersection observer has an official polyfilled.

The web payment API is easily polyfillable, and you can fallback to a checkout
form.

getUserMedia has already been around for a long time.

Have fun!


