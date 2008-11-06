[Practical Git for People and Corporations](../../README.md) - Introduction

# Why use a Distributed VCS?

What it really amounts to is *flexibility*.

* **To work together on stuff not yet ready to be committed.**
	* Let's say you need help with something that doesn't work:
        * **DVCS** You can share *commits* with someone *before* committing/pushing them to the official repository.
        * **VCS** ...Use some ingenious, time-eating way, like zipping some files, sending them by email, and knowing it won't be merge-able with your colleague's files. So I think you'll end up not doing much team work.

* **To gain incredible speedups because most operations are local.**
    * Consequently, one could argue that not waiting after operations helps keep one's concentration.

* **To have a more useful history of the project.**
    * **DVCS** You can *visualize* what really happened between different commits/merges. The history is a *tree/graph* structure.
    * **VCS** ...You'll have to read more commits because the history is a *list* structure.

* **To have lightweight branches *and* easy merges.**
    * **DVCS** Why are merges easier? Because of the better history understanding of DVCSes, they *know* if some parts of a change have already, or not, been merged unto your current branch.
    * **VCS** ...Gets lost easily, and more and more as time passes between the branch and the merge; and then, you get the dirty hand-work of fixing the merge.

* **To have *meaningful* commits.**
    * **DVCS** With a *bit* of discipline and a *lot* of flexibility, it's easy to locally organize/reorganize multiple commits before pushing them to the official repository, so that they convey a proper historical perspective.
        * Each part of your work can be done in a different, lightweight branch, so you don't mix and match, for example, new functionality changes with refactoring changes with documentation changes with code reformatting changes. See the point? Each one of these changes would have its own commit, so it'd be easy to see the point of each commit afterwards and only check the diffs relevant to a regression, for example.
        * Even if you didn't work in multiple, lightweight branches, you still can have multiple commits before pushing them to the official repository. And you can reorganize them on-the-fly, interactively, and easily, so they carry as much meaning to the team as you want.
    * **VCS** ...With a *lot* of discipline, and *no flexibility*.

# Why use Git?

To us, what makes Git more useful than other DVCSes like Mercurial and Bazaar is the following:

* Git is the most integrated with svn.
* Git is the most flexible.
    * Particularly, Git has the most complete support for rewriting local historical commits *before* pushing them to external repositories.
* Git has the most adoption.

Seeing Git is the leader on these fronts, it's easy to make a choice.

By the way, let's take a look at one Git objection:

* It's *no more* true that Git has almost no Windows support.
    * No open source DVCS yet has first-class, native Windows support.
    * Mercurial has TortoiseHg, which, AFAIK, is a work in progress.
        * Git's command line is so powerful, flexible and *starves* to be learned and used. TortoiseGit will be there later, but will I really want to use it but for the most basic operations?
    * *git-svn* isn't yet supported under Windows; both Mercurial and Bazaar don't have an equivalent to *git-svn* anyways.
        * If one is willing to use cygwin, then *git-svn* is 100% possible on Windows.

# Git pitfalls for SVN people

Git wasn't meant to use the same syntax as cvs/svn. It was meant to have the same syntax as BitLocker.

* `add` is not the same.
    * svn: enables tracking of newly added files.
    * git: enables tracking (staging, indexing) of new changes, either newly added files or modified files.
* `revert` is not the same at all (dangerous!).
    * svn: reverts local changes.
    * git: used to revert *old* checkouts, AFAIK. You'll probably more often use:
        * `checkout`
        * And in some rare cases, a flavor of `reset`. Be warned, though, that `reset` is too much publicized by the blog community. If I remember well, it's in *Git from bottom up (links at end of page)* that you see how `checkout` is preferable in most cases as a much less dangerous option and more natural one, too.
