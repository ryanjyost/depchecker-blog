---
title: How to analyze your npm dependencies for better, more maintainable code
date: "2020-01-28"
description: "A beginner-friendly guide to diving into your npm dependencies."
---

In this article, I’m going to walk you through the steps I take when assessing the state of my npm dependencies, with
the goal of improving my software overall and making it more maintainable.

## Why read this article?

With the growing sophistication and complexity of the JavaScript ecosystem comes an increased reliance on third party
[npm](https://npmjs.org) dependencies.

Back in the day, you used to be able to rely on vanilla JS or jQuery to handle the interactivity of your applications. But now, powerful frameworks like React, Vue, etc. and the myriad amazing libraries that work with them make it super beneficial to leverage open source solutions to our technical problems.

[npm](https://npmjs.org) makes it easy to incorporate these third party solutions into our code - **_so_** easy, that we can take our
deps for granted and forget that they aren’t just magical black-boxes that we can stop worrying about once
implemented.

**Lots can evolve - and devolve - about your dependencies that can bite your code in the ass if you aren’t paying
attention.**

## Why I wrote this

On multiple occasions, I’ve been tasked with updating a legacy codebase's dependencies.
Besides just checking how many versions behind the deps are, I do a deep dive to uncover changes or potential issues, which takes a lot of manual internet research.

To scratch my own itch and save time when assessing the current health of my dependencies, I built a simple tool that automatically gathers info about, and analyzes, the
dependencies in a `package.json` file.

The tool is called [DepChecker](https://depchecker.com) and I put it up at [DepChecker.com](https://depchecker.com).
Curious if it helps others! _Shameful self-promotion, but I really think it’s a useful tool, especially if you're
taking the time to read an article about this topic._
![Image](https://yosts-posts.s3.amazonaws.com/depchecker/depchecker_results_1.png)

## Ok, let's analyze some dependencies

The process for auditing your dependencies is basically like a nested `for` loop...

```javascript
for (let dependency of dependencies) {
  const infoAboutDependency = []

  for (let factor of factors) {
    const info = gatherInfo(dependency, factor)
    infoAboutDependency.push(info)
  }

  makeAssessment(infoAboutDependency)
}
```

There are a variety of useful factors that shed light on the state of your dependencies. The list we'll walk through
isn't exhaustive, but it's the one I use and is pretty thorough.

**For each factor, I'll break down the following things...**

- Exactly **what** it is
- **Where** to find info about it
- **How** to assess and make sense of the info you find
- **Why** you should care

### Factor 1: What does the dependency do?

Before anything, you need to understand what your dependencies do and their role in your project. I've definitely been
guilty of seeing a dependency name I don't recognize in my `package.json`.

#### Where to find info about it

A combination of...

- the package's page on npmjs.org (e.g. [React](https://www.npmjs.com/package/react))
- the GitHub repository (e.g. [Express](https://github.com/expressjs/express))
- and, if available, the project's site/documentation (e.g. [Vue.js](https://vuejs.org/))

...should be plenty to understand its purpose.

Manually searching for each dependency can be annoying, so you can use a tool like
[DepChecker](https://www.depchecker.com), which quickly gathers the description and all of the important links for you.

![Image](https://yosts-posts.s3.amazonaws.com/depchecker/depchecker_links.png)

#### How to assess the info

When just starting your dependency audit, it's probably sufficient to just read the description and main points.
Later on, if other factors warrant, you can dive deeper into the documentation to better understand it and help
determine to what extent it belongs in your project.

#### Why you should care

Even if not for dependency management/analysis purposes, it's always best to understand what dependencies are in your
project, what they do and why your project uses them. Otherwise, you can be ignoring old, unused or potentially
dangerous dependencies that'll bring nothing but confusion and headaches to your team down the road.

### Factor 2: Is the dependency still being used in your project?

It's quite common for a dependency to be installed and used in a project, then to have all uses of it
removed for various reasons but still leave the dependency in there. This is the low hanging fruit of dependency
management.

#### Where to find info about it

1. The easiest way to uncover _potentially_ unused dependencies, ironically, is another open source npm tool called
   [depcheck](https://www.npmjs.com/package/depcheck). Once installed, you can run a simple command to get a list of dependencies
   that it can't find references to in your project.
2. Perform a global text search for the dependency name and see if it's being used anywhere.
3. Sometimes, weird dependencies like babel plugins won't show up exactly in your project, but they are still being
   used. So one way to check whether it's needed is to remove it and run your app along with any tests.

#### How to assess the info

Corresponding to the numbered points above...

1. **Be careful!** depcheck is awesome, but it's not 100% accurate, so you should verify some other things before
   deleting it from your `package.json`.
2. If it's imported, is the module actually used in the code? If not, then maybe it's no longer needed.
3. If the tests pass and all looks good, it can be reasonably assumed that the dependency isn't needed anymore.

#### Why you should care

- Unused dependencies just add dead kilobytes to your bundles and bloat to your project, increasing load times.
- Future contributors to your codebase might think it's OK or even encouraged to use the dependency if it's already
  installed, which might not be ideal for your team and the code practices you try to implement.
- An unused dependency just sitting in your project can introduce a compiling/transpiling bug or security vulnerability down the road.

### Factor 3: Versions behind the latest release

All npm packages have a latest stable release version, whereas your `package.json` specifies a version of the package that it currently uses.
So your project's version can match the latest release, or be some version before it.

Here are some links for more background info...

- [npm project dependencies](https://docs.npmjs.com/files/package.json#dependencies)
- [npm package version](https://docs.npmjs.com/files/package.json#version)
- [official semver versioning docs](https://semver.org/)
- [npm's take on semver](https://docs.npmjs.com/about-semantic-versioning)

#### Where to find info about it

To find the **npm package's latest release version**, you can go to it's offical [npmjs.org](npmjs.org) page and find it in the details on the right side.
![Image](https://yosts-posts.s3.amazonaws.com/depchecker/depchecker_npm_version.png)

To find **your project's version**, go to your `package.json` and find the package name key in the object of `dependencies` or `devDependencies`, and the project version will be the value for that key.
![Image](https://yosts-posts.s3.amazonaws.com/depchecker/depchecker_npm_version.png)

#### Why you should care

This is the factor most commonly thought of when it comes to dependency management, for good reason!
Staying up-to-date gives you access to the latest and greatest, and helps avoid getting so far behind the current version that major bugs emerge or upgrading becomes a nightmare.

Most npm packages follow the semver versioning paradigm, making it pretty easy to compare your project’s package version with the latest released on npm.

Patch (x.x.0)
This denotes a "backwards compatible bug fixes."
