---
title: How to analyze your npm dependencies for better, more maintainable code
date: "2020-02-02"
description: "A complete, beginner-friendly guide to understanding the state of your npm dependencies."
---

In this guide, I’m going to walk you through the things I consider when assessing the state of my npm dependencies, with
the goal of improving my software and making it more maintainable.

## Why read this?

With the growing sophistication and complexity of the JavaScript ecosystem comes an increased reliance on third party
[npm](https://npmjs.org) dependencies.

Back in the day, you used to be able to rely on vanilla JS or jQuery to handle the interactivity of your applications. But now, powerful frameworks like React, Vue, etc. and the myriad amazing libraries that work with them make it super beneficial to leverage open source solutions to our technical problems.

[npm](https://npmjs.org) makes it easy to incorporate these third party solutions into our code - **_so_** easy, that we can take our
deps for granted and forget that they aren’t just magical black-boxes that we can stop worrying about once
implemented.

**Lots can evolve - and devolve - about your dependencies that can bite your code in the ass if you aren’t paying
attention.**

## Why I wrote this

On multiple occasions, I’ve inherited a legacy codebase and/or been tasked with updating a codebase's dependencies.
Besides just checking how many versions behind the deps are, I do a deep dive to uncover changes or potential issues, which takes a lot of manual internet research.

To scratch my own itch and save time when assessing the current health of my dependencies, I built a simple tool that automatically gathers info about, and analyzes, the
dependencies in a `package.json` file. It basically does what this article walks through.

The tool is called [DepChecker](https://depchecker.com) and I put it up at [DepChecker.com](https://depchecker.com).
Hope it helps folks!
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

#### The factors to consider are...

1. What does the dependency do?
2. Is the dependency still being used in your project?
3. Versions behind the latest release
4. When was the dependency last updated?
5. How many weekly downloads?
6. How many GitHub stars?
7. How many open issues and pull requests?
8. What’s the project's license?

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

When starting your dependency audit, it's probably sufficient to just read the description and main points.
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
![Image](https://yosts-posts.s3.amazonaws.com/depchecker/depchecker_package_version.png)

With tools like [npm-check](https://www.npmjs.com/package/npm-check) and [DepChecker](https://www.depchecker.com), you can get a nice, color-coded printout of the latest version, your version AND how far behind your version is.
![Image](https://yosts-posts.s3.amazonaws.com/depchecker/depchecker_versionsBehind.png)

#### How to assess the info

The vast majority of npm package versioning will use the [semver](https://semver.org/) `MAJOR.MINOR.PATCH` format,
which makes it pretty easy to figure out how far behind your project version is and how much potential risk there is to upgrading.

- **PATCH (x.x.0)** -
  This denotes "backwards compatible bug fixes." So if your version is just patches behind (like the lodash example in the screenshots above), you can pretty safely just bump the version.

- **MINOR (x.0.x)** -
  This denotes new functionality that's been added "in a backwards compatible manner". Again, probably safe to upgrade so long as tests all pass.

- **MAJOR (0.x.x)** -
  This denotes "incompatible API changes" and requires diving into the changelogs, release notes, and other documentation in order to figure out the implications of doing a `MAJOR` version upgrade.
  You'll probably need to do some manual refactoring with a major version upgrade!

**As a general note**, upgrading your project's dependency versions is safest with plenty of test coverage and a CI/CD pipeline.
Awesome free tools like [dependabot](https://dependabot.com/) can help automate this for you.

#### Why you should care

This is the factor most commonly thought of when it comes to dependency management, for good reason!
Staying up-to-date gives you access to the latest and greatest, and helps avoid getting so far behind the current version that major bugs emerge or upgrading becomes a nightmare.

### Factor 4: When was the dependency last updated?

Whenever an npm package has a new version, it needs to be uploaded and published to the npm repository.
The most recent time that this occurred is called the "Last Publish". So, you can say something like "React's last publish was 2 weeks ago."

#### Where to find info about it

You can quickly find how long it's been since the last publish on a package's npm page.
![Image](https://yosts-posts.s3.amazonaws.com/depchecker/depchecker_npm_lastPublish.png)

For more details about exact dates of version releases, you'll have to check out any releases on GitHub or use the npm repository API.

#### How to assess the info

Generally speaking, it's a great sign if an npm package is constantly being updated and has a recent last publish date - it signifies that the contributors are actively involved and care about the dependency.

If a package hasn't been updated for a year or more, on the other hand, that could signify that the package is no longer maintained and neglected.

How to interpret the exact time since last publish can vary from package to package depending on its nature.
For example, a library of basic statistics probably doesn't need to be updated that often.
Though in the modern age of JavaScript, not updating for a while has the potential for compiling issues or incompatible versions of other dependencies.

I have some general, subjective rules-of-thumb in my head for how long it's been since the last publish...

- less than 2 months - GOOD
- less than 6 months - FINE
- less than a year ago - WARNING
- more than a year ago - DANGER

#### Why you should care

The time since last publish is a very strong indicator of the care and attention that an open source npm package has.

If a dependency of yours is updated very often, it generally means you can confidently continue to use it and benefit from improvements in the future.
On the other hand, a neglected package threatens your codebase and likely needs to be replaced by a comparable dependency before it causes bugs and issues for your team.

### Factor 5: How many weekly downloads?

Whenever someone installs an npm package, it counts as a download. npm keeps track of these downloads and provides a weekly downloads metric for all packages.

#### Where to find info about it

It's easy to find on a package's npm page. You can also see a trend line there.
![Image](https://yosts-posts.s3.amazonaws.com/depchecker/depchecker_npm_downloads.png)

#### How to assess the info

This weekly downloads metric indicates to what extent that the dependency is being used by other developers.
_Of course a download doesn't 100% mean the developer ended up keeping the dependency, but it's still a useful indicator._

**Here are my subjective rules-of-thumb for weekly download counts...**

- more than 100,000 - GOOD
- less than 100,000 - FINE
- less than 10,000 - WARNING
- less than 1,000 - BAD

**Trends in downloads are also helpful.**

- If going up, it could mean that the low download count is due to the package being young but still valuable.
- If going down, it could mean that the package has a superior alternative, that it's neglected, that it isn't needed anymore, etc.

In addition to the tiny trend line on the npmjs.org site, there's a cool website called [npm trends](https://www.npmtrends.com/) where you can dive deep into npm download trends.

#### Why you should care

- How popular am npm package is can mean a lot for its future, and therefore the future of your software.
- When deciding on what npm package alternative (e.g. Angular vs. React vs. Vue) to use in your project, the download trends can be telling of what the community thinks is superior/inferior.
- Downward trends can warn you about a dying dependency and allow you to preemptively remove or replace it before it negatively impacts your project.

### Factor 6: How many GitHub stars?

Projects on GitHub, where the vast majority of npm package repositories are, have a star system, which is basically how people can "like" a project.

#### Where to find info about it

Pretty simple - just go to the GitHub repository for the npm package and the star count is on the top of the page.

#### How to assess the info

Obviously, the more stars a repo has, the more popular and beloved by developers it is. A high star count usually indicates that the project works well and serves the purpose that it says it does in a way that devs love.

**BUT BE CAREFUL**! A high star count is not enough to sign off on using/keeping a dependency, because stars accumulate over time. It's possible that the project has 20k stars, but the last star was a year ago, or it's just deprecated.

Like the weekly downloads, trends are just as important as the current total of stars. There's a couple cool websites like [Star History](https://star-history.t9t.io/) and [this one](https://stars.przemeknowak.com/) that show Github star trend lines.

**Here are my subjective rules-of-thumb for GitHub stars...**

- more than 1,000 - GOOD
- less than 1,000 - FINE
- less than 300 - WARNING
- less than 50 - BAD

#### Why you should care

Basically the same reasons as weekly downloads!

### Factor 7: How many open issues and pull requests?

The beauty of open source projects is that anyone can provide feedback and contribute in the form of GitHub issues, which can become pull requests for potentially incorporating new code into the master branch and ultimately releasing an updated version.

#### Where to find info about it

In addition to going directly to the source on GitHub, npm provides the breakdown.
![Image](https://yosts-posts.s3.amazonaws.com/depchecker/depchecker_npm_issues.png)

#### How to assess the info

The number of open issues and pull requests can indicate a few things...

- How active the open source community surrounding the project is.
- To what extent the project has bugs or lacks desired functionality
- How active the maintainers are

If the number of open pull requests is high, that could show the community is desperate for improvements but the maintainers are asleep at the wheel or not accepting outside help - not a good sign.

The absolute number of open issues and PRs can be misleading - bigger projects with more users should naturally have more issues on GitHub, and vice versa.

So you'll want to normalize the number of open issues in your head to account for this. While far from scientific, you could think of a rough ratio of open issues and PRs to weekly downloads. The smaller the ratio, the better.

#### Why you should care

- Lots of issues means an active user base for the dependency, which can mean it'll be maintained regularly and be safe to use long-term.
- Tons of bugs in the open pull requests isn't a great sign for a piece of software you want to include in your own.
- Good and bad signs can be gleaned from the current state of a dependency's open issues and pull requests - so it's valuable to pay attention!

### Factor 8: What's the project license?

Open source projects like npm dependencies have a license attached to them, which dictates how the project can be used and other legal stuff.

#### Where to find info about it

You can find an npm package's license on its npm page.
![Image](https://yosts-posts.s3.amazonaws.com/depchecker/depchecker_npm_license.png)

For details of particular licenses, Google and this [table-breakdown](https://choosealicense.com/appendix/) are helpful.

#### How to assess the info

I'm by no means a legal expert, but there's a spectrum of licenses that can be attributed to open source dependencies.

Most of them you don't have to worry about, but if an npm dependency of yours doesn't use the classic, free-reign MIT license, then it's prudent that you figure out the implications of the license for your software, especially if using in commercial software.

[Here's a great article with more on that.](https://medium.com/@vovabilonenko/licenses-of-npm-dependencies-bacaa00c8c65)

#### Why you should care

I'm sure the legal team at your company does! 
If you mistakenly use an npm dependency in a manner against the license, then you expose your software and company to legal issues and other avoidable problems!

## And that's it!

This is just one way to approach npm dependency management - hope it helps!

### List of tools to help you manage dependencies

- [DepChecker](https://depchecker.com)
- [depcheck](https://www.npmjs.com/package/depcheck)
- [npm-check](https://www.npmjs.com/package/npm-check)
- [dependabot](https://dependabot.com/)
- [npm trends](https://www.npmtrends.com/)
- [Star History](https://star-history.t9t.io/)
- [stars.przemeknowak.com](https://stars.przemeknowak.com/)
- [choosealicense.com](https://choosealicense.com/appendix/)

### Other links

- [npm](https://npmjs.org)
- [npm project dependencies](https://docs.npmjs.com/files/package.json#dependencies)
- [npm package version](https://docs.npmjs.com/files/package.json#version)
- [official semver versioning docs](https://semver.org/)
- [npm's take on semver](https://docs.npmjs.com/about-semantic-versioning)
