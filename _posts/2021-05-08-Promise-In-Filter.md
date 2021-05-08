---
title: How To Use Promise In Filters With JavaScript
author: waz
layout: post
---

So you're coding some JavaScript and you've learned about Asynchronous Promises, then some sorts and filters, but 
you've tried to combine the two and got stuck. If you search around, you'll find a lot of people saying you simply 
can't use Promises in filters. That's mostly true, but with some clever coding you can get it to work. This is how.

Here's our initial array that we want to filter:
```
let people = [{name: 'Fred', age: 32}, {name: 'Sandy', age: 24}]
```

Let's see a standard filter, for filtering only people older than 26
```
console.log("Sync")

function filterOldEnough(person) {
return person.age > 26;
}

console.log(people.filter(person => filterOldEnough(person)));
```
here the output is: `[ { name: 'Fred', age: 32 } ]`, which is what we expect

But what if we want to call an async operation in the filter?

Let's first create that function again, but make it asynchronous and return a Promise

```
console.log("Async")

async function asyncFilterOldEnough(person) {
    return new Promise(res => setTimeout(function () {
        res(person.age > 26);
    }, 2000));
}

```

Now, we can try to use a filter again, exactly as before but this time with the async function:

```
console.log(people.filter(person => asyncFilterOldEnough(person)));
```

Here the output is: `[ { name: 'Fred', age: 32 }, { name: 'Sandy', age: 24 } ]`

This is clearly wrong, and happens because the output of the function is a promise, which the filter just interprets 
as true for every record.

You could try putting an await in there, however, you'll get a runtime error:

```
console.log("await")

console.log(people.filter(person => await asyncFilterOldEnough(person)));
// This fails

```

Okay, so my next idea when finding this problem was to try to handle the promise in the filter:

```
console.log("chaining")

console.log(people.filter(person => asyncFilterOldEnough(person).then(e => e)));

```
This again seems not to work, again the filter fails and returns everything.
`[ { name: 'Fred', age: 32 }, { name: 'Sandy', age: 24 } ]`

So what's the solution?

We can wrap the async function in a syncrhonous function, then call that:

```
console.log("wrapper")

// Here we use Promise.all to get the returned values, then filter those results 
const filterPromise = (values, fn) => Promise.all(values.map(fn)).then(booleans => values.filter((_, i) => booleans[i]));

const filteredPeople = filterPromise(people, asyncFilterOldEnough);

filteredPeople.then(p => console.log(p));

```
Finally, this output is as we expect `[ { name: 'Fred', age: 32 } ]`.

Now do I recommend doing this? I'm not sure. It's hard to read, even when you know what the code is trying to do, a 
new developer to this piece of code might be very confused. But, if you need it to work, then this method achieves that.


