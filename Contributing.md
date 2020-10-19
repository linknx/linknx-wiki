# Areas you might want to contribute to

There are many ways someone can help this project. Not all areas require development skills. Those areas can be broken into two main categories, with one dedicated Github project for each:
- development: see the [linknx source code repository](https://github.com/linknx/linknx)
- documentation, namely this wiki, whose source files are hosted in a [separate repository](https://github.com/linknx/linknx-wiki)

***

# Working in the wiki

## How it works

The main page of this wiki is accessible [here](http://github.com/linknx/linknx/wiki). Github Wikis are backed by a git repository, just like regular source code is. But unlike source code repositories which have a public-facing web interface simplifying collaboration with the community (through issues and pull requests, for instance), wiki repos are mostly hidden from visitors. For linknx, the URL of the git repo is https://github.com/linknx/linknx.wiki.git
This repo is publicly accessible for reading but only administrators are allowed to push changes.

To work around that limitation, we have created a separate Github project to host the source files for the wiki (markdown files and images). It is accessible [here](https://github.com/linknx/linknx-wiki). It behaves like any other Github project, allowing issue and pull request creation from any volunteer. When something is changed on its `main` branch, a [Continuous Integration build](https://github.com/linknx/linknx-wiki/blob/main/.github/workflows/wiki-sync.yml) automatically
pushes the latest version of this branch to the real linknx wiki.

## How to proceed to submit a change

You have spotted a mistake in the wiki? You would like to document a missing feature? Great! Let's see what steps to follow:

1. Review the [open issues](https://github.com/linknx/linknx-wiki/issues?q=is:open) in the wiki project. If an unassigned one corresponds to your plan, assign it to yourself. Otherwise, [create a new one](https://github.com/linknx/linknx-wiki/issues/new) and use it to describe what your suggestion is. That allows others to detect problems early and is very helpful to track history
2. [Fork the wiki source repo](https://github.com/linknx/linknx-wiki/fork) and make your changes in this fork
3. Submit a pull request to merge your changes in the main repo's `main` branch. Make sure to reference the issue you created in step 1 in this pull request.
4. You're done! An administrator will now review your PR.

## Let me know where to help

You want to help but don't know what to do? Review the open issues and pick one that is waiting for you.

## Guidelines

- The entire wiki must be written in US English.
- Abbreviations and acronyms are discouraged because their interpretation often depends on the reader. Their usage should be strictly limited to very common ones which are obvious to anyone reading the wiki.
- All pages are using a Markdown syntax. Code samples are surrounded with simple backticks for inline code `like this` and triple backticks to create a paragraph.

***

# Working in the source code

Whatever the reasons you are reading this section for, start by reviewing [issues](issues). Search for one already covering your remark / question / concern. If none helped to answer your case, continue reading.

## Bug reporting

If you think something is not working as expected, consider submitting a [new issue](issues/new). Make sure it contains the following items:
- the version of linknx you are running
- the options passed to `./configure`. You can review them in the `config.log` file generated at the root of the source code
- the steps to reproduce the problem
- the symptoms
- the expected behavior

If one item is missing, your issue is more likely to be misunderstood or closed without fixing it.

## Feature request

Ideas are welcome! Especially when they are well thought through. The clearer the specification, the simpler the implementation!

Like for reporting bugs, submit a new issue and make sure to describe your proposal. Adding new features takes time and requires extra care to maintain the overall stability of the software so please advocate for your request with a use case that explains why it would be useful.

If you are feeling adventurous, [fork](fork) the source code repo and submit your proposal via a pull request. 
