+++
title = "Getting Started With a New Codebase"
date = 2019-08-04T19:54:24+05:30
featured_image = "/images/codebase.png"
+++

Developers spend more time reading and editing the code that others have written than whipping out new code. This becomes exponentially harder if the codebase you are working with is unfamiliar to you.  Some codebases can be pretty huge and unwieldy.
    
I have been working on my [Google Summer of Code](https://summerofcode.withgoogle.com/) [project](https://summerofcode.withgoogle.com/projects/#5444359158235136) for the past 3 months. But before I was selected for GSoC, I had to dig through the GSoC website for projects that suited me. Once I found something interesting, I would clone their repository to try and get started with their codebase, just to see if the project suited me, and if I could pull it off. After going through this exercise a fair number of times, I think I have gathered a few tips on how to do this as efficiently as possible.


![Codebase](/images/codebase.png)


Let's start with the obvious ones first...

### 1. Read the README
Okay. This is the most obvious one. I don't even know why I am mentioning this one. But it's not just the README.md, there might also be a **CONTRIBUTING.md** (or something equivalent.. a wiki maybe). I think this is more useful for someone looking to contribute to the project. This file can tell you things like the directory structure, project architecture, **git workflow**, etc. The git workflow is especially important. The project might have specific instructions on how to format your commit messages or how to name your branches. Readme will have information on how to build the project. So, as soon as I finished reading the Readme I would **clone it, build it, run it**.

### 2. Be the user before being the developer
I admit that this one is quite hard to do if you are planning to contribute to something like Kubernetes. But if it is a web app or something, **try it out** before you start hacking on it. It is hard to fix something if don't know what that thing does. Let's just leave it at that.

### 3. Find a surface point and dive deep
By "surface point", I mean a **user-facing interface**. This might be a UI element like a form or server interface like a REST API endpoint. Once such an interface is found, try and understand how it works by **diving deeper into its inner workings**. If it is an API endpoint, find the handler function for the HTTP request. Then see what that function does. So on and so forth.

### 4. Use global search
Searching is much better than poking around the directory structure. I found that searching is especially useful for **finding "surface points"**. Say you are looking for a specific HTML form, it is important to know what to search. Searching for input boxes might yield a hundred results. It is better to search for unique strings like the placeholders of the input boxes. In the case of server codebases, searching for endpoints like "/posts" should work. Any text editor worth its salt will have a global search feature; if not, use tools such as grep, silver searcher, or [ripgrep](https://github.com/BurntSushi/ripgrep).

### 5. Start with the tests
I think writing and fixing tests are the best way to get started with a new codebase. This helps me understand what the code is doing and gives me enough **exposure to the codebase** to later start contributing code. **Integrations tests** are the best to **understand how the whole thing comes together**. The **unit tests** are a great **source of documentation** to understand how to use the units of code they are testing.

### 6. Use good dev tools
This one deserves sub-points.

**a. Using [sourcegraph](https://about.sourcegraph.com/) -** Trying to read code on GitHub is like navigating a maze blindfolded. Sourcegraph is a cool tool that makes reading code in the browser bearable by giving you powerful IDE-like features right in the browser. They even have a [chrome extension](https://chrome.google.com/webstore/detail/sourcegraph/dgjhfomjieaadpoljlnidmbgkdffpack?hl=en) that gives you some of these features on GitHub. 

![Sourcegraph](/images/sourcegraph.png)

**b. Using a good editor/IDE -** Features like "go to definition" and "hover documentation" are insanely helpful when navigating a new codebase.

**c. Using a debugger -** Debuggers are handy when you need to understand the behavior of a piece of code. Stepping through code in great editors like VSCode is much better than littering the code with print statements (No judgments). Debuggers also give me the call stack which is a great way to analyze the control flow.

**d. Using static code analyzers -** These tools usually give you a good idea about the architecture of the codebase. For example, [go-guru](https://docs.google.com/document/d/1_Y9xCEMj5S-7rv2ooHpZNH15JgRT5iM742gJkw5LtmQ/edit) is a code analysis tool for golang that can answer questions such as "Which concrete types implement this interface?" or "Where might a value sent on this channel be received?". These are questions that an editor like VSCode will not be able to answer. Tools that can generate UML diagrams or API structures for languages like C++ or Java also exist.


### 7. Silly one - knowing English helps when reading Shakespeare
Ignoring the bad analogy for a moment, what I am trying to say is, I found that knowing at least the basics of the programming language will be of great help when you are trying to understand a codebase. I used to think that if you know one language you kind of know them all. I was sorely proven wrong by my discovery of Haskell. This is not meant to be a rap on Haskell. For instance, I found it hard to understand the concurrency concepts in golang just by reading code that uses these distinctive concepts. Understanding codebases is hard as is, not knowing the language primitives just makes it harder.

**P.S.** I don't think it is vital to know the entire codebase to become productive with it. My GSoC project had 5 different components that run as separate processes and I needed to only work with one of them and be aware of the API for others. Also, stupid pen-paper diagrams are handy to break down complex architectures.

Happy Hacking !!