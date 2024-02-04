---
title: TIL sort mutates the array
date: "2020-12-27"
---

While working on a bug at work, I found out that `sort` mutates the array & was actually the cause of the bug. MDN actually mentions this [here](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/sort#Return_value) too.

So the trick to avoid any potential bugs is to make a copy of the array & then call `sort` on the copy of that array & use that for further manipulations.

Lucky for us in Javascript (or ES6), we can make a copy of an array like this:

```
const copyOfArray = [...sampleArray]
```