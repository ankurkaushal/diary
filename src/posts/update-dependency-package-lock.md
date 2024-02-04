---
title: Updating a package's dependency in package-lock.json
date: "2019-08-04"
description: "One of the few ways you can update the vulnerable package(s)."
---
If you ran `npm audit` on your project & it reported vulnerabilities in dependency of a package, there are a couple of ways to fix it:

- Run `npm audit fix` which is easiest solution but it depends on whether the vulnerable dependency has an update available or not.

- If an update is not available, create an issue in the repository of the vulnerable dependency (or package). This solution may take a *lot* of time depending on whether the project is actively maintained or not.

But what if an update is available but the package hasn't updated the dependency on their end & you don't want to wait? Luckily there's a way. And for those scenarios, `npm` will prompt you to manually review the package.

I will show you how by fixing a vulnerability that got reported in one of the projects at my work:

![vulnerability](/images/vulnerability.png)

As you can see in the above screenshot, `npm` is telling me to manually review the vulnerability myself.

Let's go into `package-lock.json` & use our old friend `cmd + f` or `ctrl + f` to find `uglifyify` since `extend` is a dependency of it.

Here's what the result looks like:

```json
{
  "uglifyify": {
    "version": "5.0.1",
    "resolved": "https://registry.npmjs.org/uglifyify/-/uglifyify-5.0.1.tgz",
    "integrity": "sha512-PO44rgExvwj3rkK0UzenHVnPU18drBy9x9HOUmgkuRh6K2KIsDqrB5LqxGtjybgGTOS1JeP8SBc+TN5rhiva6w==",
    "dev": true,
    "requires": {
      "convert-source-map": "~1.1.0",
      "extend": "^1.2.1", // The package we want to fix
      "minimatch": "^3.0.2",
      "terser": "^3.7.5",
      "through": "~2.3.4"
    },
    "dependencies": {
      "convert-source-map": {
        "version": "1.1.3",
        "resolved": "https://registry.npmjs.org/convert-source-map/-/convert-source-map-1.1.3.tgz",
        "integrity": "sha1-SCnId+n+SbMWHzvzZziI4gRpmGA=",
        "dev": true
      },
      "extend": {
        "version": "1.3.0", 
        "resolved": "https://registry.npmjs.org/extend/-/extend-1.3.0.tgz",
        "integrity": "sha1-0VFvsP9WJNLr+RI+odrFoZlABPg=",
        "dev": true
      }
    }
  }
}
```

If you look at the terminal screenshot, the version greater than or equal to `3.0.2` should have the fix. Let's modify the current version to that. Keep in mind we need to modify the versions in both `requires` & `dependencies` objects.

```json
{
  "uglifyify": {
    "version": "5.0.1",
    "resolved": "https://registry.npmjs.org/uglifyify/-/uglifyify-5.0.1.tgz",
    "integrity": "sha512-PO44rgExvwj3rkK0UzenHVnPU18drBy9x9HOUmgkuRh6K2KIsDqrB5LqxGtjybgGTOS1JeP8SBc+TN5rhiva6w==",
    "dev": true,
    "requires": {
      "convert-source-map": "~1.1.0",
      "extend": "^3.0.2", // Change the version here
      "minimatch": "^3.0.2",
      "terser": "^3.7.5",
      "through": "~2.3.4"
    },
    "dependencies": {
      "convert-source-map": {
        "version": "1.1.3",
        "resolved": "https://registry.npmjs.org/convert-source-map/-/convert-source-map-1.1.3.tgz",
        "integrity": "sha1-SCnId+n+SbMWHzvzZziI4gRpmGA=",
        "dev": true
      },
      "extend": {
        "version": "3.0.2", // We need to modify the version here too
        "resolved": "https://registry.npmjs.org/extend/-/extend-3.0.2.tgz", // Here too
        "integrity": "sha1-0VFvsP9WJNLr+RI+odrFoZlABPg=",
        "dev": true
      }
    }
  }
}
```

Let's run `npm ci` or `npm i` to change the make sure `npm` installs the updated version of the package. After its finished, it's a good idea to verify if the intended version has been installed by doing `npm list extend`. That should give us this output:

```bash
├─┬ request@2.88.0
│ └── extend@3.0.2
├─┬ supertest@4.0.2
│ └─┬ superagent@3.8.3
│   └── extend@3.0.2  deduped
└─┬ uglifyify@5.0.1
  └── extend@1.3.0
```

If you look closely `extend` dependency version for `uglifyify` is still the same. What!?

Reason for that is `integrity` for the package hasn't been changed. `npm` depends on it to ensure that the package hasn't been tampered with. I belive since the `integrity` was pointing to old version, `npm` decided to install the old version only. 

So how do we find out `integrity` for the updated version? Turns out `npm` has a very handy command called `npm view <package>` which gives us an output like this:

```bash
extend@3.0.2 | MIT | deps: none | versions: 13
Port of jQuery.extend for node.js and the browser
https://github.com/justmoon/node-extend#readme

keywords: extend, clone, merge

dist
.tarball: https://registry.npmjs.org/extend/-/extend-3.0.2.tgz
.shasum: f8b1136b4071fbd8eb140aff858b1019ec2915fa
.integrity: sha512-fjquC59cD7CyW6urNXK0FBufkZcoiGG80wTuPujX590cB5Ttln20E2UB4S/WARVqhXffZl2LNgS+gQdPIIim/g==
.unpackedSize: 23.5 kB

maintainers:
- justmoon <justmoon@members.fsf.org>
- ljharb <ljharb@gmail.com>

dist-tags:
backport: 2.0.2  latest: 3.0.2

published a year ago by ljharb <ljharb@gmail.com>
```

Voila! We have the `integrity` value for the updated package now. Let's change that as well & don't forget to do `npm ci` or `npm i` afterwards.

Now we if we check version for `extend` again using `npm list extend`, we will find out that `npm` has now installed the correct version:

```bash
├─┬ request@2.88.0
│ └── extend@3.0.2
├─┬ supertest@4.0.2
│ └─┬ superagent@3.8.3
│   └── extend@3.0.2  deduped
└─┬ uglifyify@5.0.1
  └── extend@3.0.2 // Correct Version 
```

So that is how you fix the vulnerable packages yourself if the maintainer of the package hasn't updated the dependencies in their package.
