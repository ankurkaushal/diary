---
title: Preserving new lines with onPaste
date: "2020-09-06"
description: Preserve new lines on copy-paste with onPaste event
---

For one of our internal admin UIs, I was tasked to implement a copy-paste feature that will let people copy from excel files specifically & paste it in a simple textbox & the individual values would show up in a nice UI.

While implementing that, I learned when you use `onChange`, it doesn't preserve new lines from the excel. So what do we now? Well, we can intercept the paste using `onPaste` and set the value in the paste handler.

I have made a [codesandbox app]( https://codesandbox.io/s/onchangeandpaste-h8fhz) to show that as well.