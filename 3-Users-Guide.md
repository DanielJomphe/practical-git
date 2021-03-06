[Practical Git for People and Corporations](..) - Users Guide

# Setting up Git

* Windows - [Download msysGit](http://code.google.com/p/msysgit/) and install it:
    * Do not pick the option to add all unix tools to your path (the red one)
    * Pick OpenSSH

### Creating ssh keys

* Windows - In Windows' Explorer, right click on a folder where you would like a git-enabled shell and select *Git bash here*

Now, whatever you system may be, run this:
  
    $ ssh-keygen -C "john.doe@company.com" -t rsa

* Use the default file name **id_rsa**.
* **Make a backup copy of the generated keys**, which can be found in your home, under a `.ssh` directory.

## Configuring Git

This updates your `*~/.gitconfig` file:

    $ git config --global user.name     "John Doe"
    $ git config --global user.email    john.doe@company.com
    $ git config --global core.autocrlf false
    $ git config --global color.ui      auto
    $ git config --list

For project-based configuration options, remove the `--global` argument. This will update your project's `<path>/<project>/.git/.gitconfig` file instead, and will be shared with the team. Note that project configuration options win over global configuration options.

# Git Worst Practices

Let's try to debunk bad Git habits here.

* Watch out not to grow the habit of using the `-a` argument to `git commit` mechanically.
    * Let's say you do an interactive staging of some changes to commit, using the wonderful `git add -i`. Then, you `commit -a`. Instead of committing only what you had interactively selected to be committed, you end up committing everything!

# Project repository workflow

* John Doe: Boss, I need to work on something!
* Boss: John, you'll work on <project>!

> Best practice:
    **An official repository for a project contains at least two branches: one stable, and one for staging purposes.**
    The staging branch is where changes are tried before being merged into the stable branch.
    In this example, it's called *edge*.

                         |                      |           OFFICIAL
                         |                      |
                         |                      |             stable
                         |                      |               edge

## Setting up a local repository

* John Doe: Boss, I'm setting up for <project>!

### Checking out from a repository

> Best practice:
    **Clone/fetch/pull from the official repository.**
    Stick to this even if you forked the official repository into another public repository.

John runs one of the following options to checkout the project along with its history:

    1$ git clone  git://<host>/<path>/<project>/official.git
    1$ git clone http://<host>/<path>/<project>/official.git
    1$ git clone (SSH: XXXX to be documented later)

1- Checks out an initial local copy of the OFFICIAL repository.

    JD-LOCAL             |                      |           OFFICIAL
                         |                      |
                 stable <------------------------------------ stable
                   edge <------------------------------------   edge


If John already had a public fork of this project, he would have done the same:

    1$ git clone git://<host>/<path>/<project>/official.git

1- Note that the Git URL for the clone references the OFFICIAL repository and **not** JD-FORK.

    JD-LOCAL            |       JD-FORK       |          OFFICIAL
                        |                     |
                stable <---------------------------------- stable
                        |             stable  |
                  edge <----------------------------------   edge
                        |               edge  |

### Setting up tracking of a remote branch

> Best practice:
    **Automate the synchronization of official remote branches towards your local repository.**
    Stick to this even if you forked the official repository into another public repository.
    This way, your working copy will always be the integration point of what's coming from the official repository, and it won't hinder your fork repository in perpetually benefiting from the official changes to the project, although it might not be clear to you now.

    1$ cd <project>
    2$ git branch --track edge origin/edge

1- Gets inside the project's working tree to interact with git (and with the project, of course).  
2- Sets up automatic tracking of the OFFICIAL edge branch, for future easy syncing.

Note that we don't need to explicitly track the OFFICIAL stable branch; this is automatically, implicitly done when you `git clone`.

## Setting up topic branches

> Best practice:
    **Make a topic branch for each future feature pushed to the official repository.**
    Try to give the topic branch a name that properly synthesizes the nature of the change.

* John Doe: Hey Boss, I'll work on *topic-1* and *topic-2*!
* Boss: Fine, go ahead!

### Creating a local topic branch

> Best practice:
    **format-branch-names-this-way** and, if they're shared, prefix their name with a parent directory whose name is the initials of the branch's author: **jd/topic-description**

    1$ git branch   <topic> edge
    2$ git checkout <topic>

or, as a shortcut:

    1+2$ git branch -b <topic> edge

1- Creates a local branch for *topic*, based off of edge.  
2- Selects the local *topic* branch, to work on it.

    JD-LOCAL             |                      |           OFFICIAL
                         |                      |
                 stable  |                      |             stable
                   edge  |                      |               edge
                         |                      |
                topic-1  |                      |
                topic-2  |                      |

#### Setting up the branch name in shell prompt

TODO:[Try it](http://github.com/guides/put-your-git-branch-name-in-your-shell-prompt) and it works, document it :)

## Setting up a sharing repository

> Best practice:
    **Work on a local repository and frequently push your changes to another repository.**
    In this example, the second repository is called **JD-FORK**.
    It will serve you as a sharing access point for you towards your team, and as a redundant backup.

### Setting up a public fork

John sends his public RSA key to his sysadmin, and asks him to create a sharing repository for him.

    1$ cd <path>/<project>
    2$ git clone --bare git://<host>/<path>/<project>/official.git jd-fork.git

2- Makes a copy of OFFICIAL, without a working tree.

    JD-LOCAL             |        JD-FORK       |           OFFICIAL
                         |                      |
                 stable  |              stable <------------- stable
                   edge  |                edge <-------------   edge
                         |                      |
                topic-1  |                      |
                topic-2  |                      |

### Setting up a remote branch in the fork repository

    1$ git checkout   <topic>
    2$ git remote add <topic> JOE@<host>:<path>/<project>/jd-fork.git
    3$ git fetch      <topic>

1- Selects the local *topic* branch, to track the future changes.  
2- Sets up a tracking reference of the local *topic* branch unto JD-FORK, for future easy syncing.  
3- Syncs up the local *topic* branch *from* JD-FORK, as a safety measure. (Probably to see if there's already a shared branch of the same name.)

    JD-LOCAL             |        JD-FORK       |           OFFICIAL
                         |                      |
                 stable  |              stable  |             stable
                   edge  |                edge  |               edge
                         |                      |
                topic-1  |        (jd/topic-1)  |
                topic-2  |        (jd/topic-2)  |

### Syncing a local topic branch to a fork repository

    1$ git push <topic> JOE:refs/heads/<topic>

1- Syncs up the local *topic* branch towards JD-FORK, effectively creating it on JD-FORK.

    JD-LOCAL             |        JD-FORK       |           OFFICIAL
                         |                      |
                 stable  |              stable  |             stable
                   edge  |                edge  |               edge
                         |                      |
              topic-1 ------------> jd/topic-1  |
              topic-2 ------------> jd/topic-2  |

### Making future branch syncs automatic

    1$ git config branch.<topic>.remote            <john>
    2$ git config branch.<topic>.merge  refs/heads/<john>

1- Makes future `git push` commands automatically sync the topic branch to JD-FORK.  
2- Makes future `git pull` commands automatically sync the topic branch from JD-FORK to JD-LOCAL.

## Working on topic branches

* John: Let's work on *topic-1*!

### Making changes on a local topic branch

    1$ git checkout <topic>

1- Selects the local branch *topic*, to track the related changes.

> Git's lightweight operation of checking out branches is something that needs to be well understood.
  If you check out a branch, your working directory will need to look 100% like that branch.
  Otherwise, you couldn't say your working tree represents that branch.
  Therefore, Git will need to remove any difference in content that is currently in your working tree but is not in the branch's tree.
  Thankfully, Git doesn't want you to loose uncommitted changes, so it will only let you checkout another branch if everything in your current working tree is committed.
  This means that if you have uncommitted changes in your working directory and want to checkout another branch, you won't be able to do so until you commit or stash them away.

    JD-LOCAL             |        JD-FORK       |           OFFICIAL
                         |                      |
                 stable  |              stable  |             stable
                   edge  |                edge  |               edge
                         |                      |
                topic-1' |          jd/topic-1  |
                topic-2  |          jd/topic-2  |

### Backing up a local topic branch to the fork repository

* John: Let's call this a day; time to go home!

Run:

    1$ git commit -m "leaving home"
    2$ git push

2- Syncs everything towards JD-FORK, thanks to John's usage of best practices when he set up his repository. Note that at the same time, the stable and edge branches are automatically synced also. This behavior is automatic and implicit for the stable branch; any other branch that's synced at the same time is done because we have previously set it up this way.

    JD-LOCAL             |        JD-FORK       |           OFFICIAL
                         |                      |
                 stable --------------> stable  |             stable
                   edge -------------->   edge  |               edge
                         |                      |
                topic-1'----------> jd/topic-1' |
                topic-2 ----------> jd/topic-2  |

JD-FORK's stable and edge branches are now in sync *with the state of OFFICIAL at the time of the last sync from OFFICIAL to JD-LOCAL*. This is what we want. It clearly highlights on which official revision of the edge branch John's *topic* branches are based. (There's probably more reasons for using this approach, too.)

#### Restoring a local topic branch from the fork repository

* *Jack*: Yes! Now that John has left for home, it's time to make him pay for the last *public* humiliation he made me suffer!

Run:

    $ #hack hack hack...
    $ git branch -d topic-1    # -d means "delete"

* *Jack*: HahahahahahahaHAAAAAAAA!

The sun goes down and up, and John comes back to work. For *some* reason, he wonders what happened to his *topic-1* branch. But anyway, he doesn't care too much:

    1$  git pull

1-  Syncs everything from JD-FORK, thanks to John's usage of best practices when he set up his repository.

    JD-LOCAL             |        JD-FORK       |           OFFICIAL
                         |                      |
                 stable <-------------- stable  |             stable
                   edge <--------------   edge  |               edge
                         |                      |
                topic-1'<---------- jd/topic-1' |
                topic-2 <---------- jd/topic-2  |

## Asking a colleague for help

* John: Ouch, I can't believe how this HTML programming language is hard to understand!

Luckily for John, Suzy Kue knows quite a deal about HTML.

* Boss: John, I thought you might need help with this new HTML thingy. If that ever happens, I think Suzy Kue might be the good candidate to ask for help. Our office in Japan recruited her recently.
* John: Oh thank you boss, but I should be fine, you know.

John then waits for the good moment to send an email to *super Suzy*, when the boss won't be near his screen. And meanwhile, he pushes his latest changes to his public repository:

    $ git commit
    $ git push

Results in:

    JD-LOCAL             |        JD-FORK       |           OFFICIAL
                         |                      |
                 stable --------------> stable  |             stable
                   edge -------------->   edge  |               edge
                         |                      |
                topic-1'----------> jd/topic-1' |
                topic-2 ----------> jd/topic-2  |

Sometime later, *the* moment comes for John to write an email to Suzy, asking her to "take a look at this code if you don't mind: `git://<...>/jd-fork.git, *branch jd/topic-1*`".

Luckily  again for John, 5 minutes later, he receives an answer from Suzy, telling him to "have a look at my proposal for a fix, at: `git://<...>SK-FORK.git, *branch jd/topic-1*`". John eagerly takes a look at Suzy's changes:

    1$ git fetch jd/topic-1 git://<...>SK-FORK.git topic-1-sk
    2$ git diff  topic-1 topic-1-sk
    3$ git merge topic-1-sk

1- John doesn't use "git pull" because he's not sure yet if he likes Suzy's patch; so he fetches it as *topic-1-sk*.  
2- A quick `diff` makes sense.  
3- John likes Suzy's patch; therefore, he merges it into the current branch.

    JD-LOCAL             |        JD-FORK       |           OFFICIAL
                         |                      |
                 stable  |              stable  |             stable
                   edge  |                edge  |               edge
                         |                      |
                   /------------ jd/topic-1-sk  |
                   |     |                      |
                   V     |                      |
                topic-1' |          jd/topic-1' |
                topic-2  |          jd/topic-2  |

Now, what John doesn't know yet is that anyone may see that Suzy helped him with this patch.

## Testing development changes with Hudson

* John: Boss will be impressed seeing *my* mastery of HTML! Let's make sure, first, that our continuous integration tests won't fail.

John is confident his changes should work fine.

    $ git commit
    $ git push jd/topic-1 git://<host>/<path>/<project>-ci-dev.git

* John: Cool, tests pass!

## Requesting a pull from the official repository

Seeing *topic-1* is now feature-complete, John puts the finishing touches to the branch's code and prepares it to be integrated into OFFICIAL.

    $ git commit
    $ git push

    JD-LOCAL             |        JD-FORK       |           OFFICIAL
                         |                      |
                 stable --------------> stable  |             stable
                   edge -------------->   edge  |               edge
                         |                      |
                topic-1'----------> jd/topic-1' |
                topic-2 ----------> jd/topic-2  |

* John: Boss, *topic-1* is done!
* Boss: Great, John! Let's have a look at it! By the way, did you need Suzy's help, finally?
* John: What for!?

## Having a look at someone else's branch

Boss pulls *topic-1* on his BOSS-LOCAL from JD-FORK, and takes a look at it...

    1$  git stash save my-actual-work
    2$  git pull
    3$  git remote add jd git://<...>/jd-fork.git
    4$  git checkout -b edge<topic>1 edge
    5$  git pull jd jd/topic-1
    6$  git diff edge edge<topic>1
    7$  git branch
    8$  git checkout my-previous-branch
    9$  git stash list
    10$ git apply my-actual-work

1-  Puts away uncommitted changes temporarily  
.  
.  
.  
10- Restores some uncommitted changes that had been put away previously

* Boss: Good work, John!

## Pulling a topic branch on the official repository

* Boss: Let's edge John's *topic-1* branch on OFFICIAL!

Boss logs on OFFICIAL, pulls *topic-1* from JD-FORK, and waits for Hudson:<project>:edge to tell him all tests have passed. 

...All tests pass. Boss commits *topic-1* to edge.

    JD-LOCAL             |        JD-FORK       |           OFFICIAL
                         |                      |
                 stable  |              stable  |             stable
                   edge  |                edge  |              edge'
                         |                      |                ^
                         |                      |                |
                topic-1' |          jd/topic-1'------------------/
                topic-2  |          jd/topic-2  |

## Rinsing and repeating

Meanwhile, John finishes his work on *topic-2*...


    JD-LOCAL             |        JD-FORK       |           OFFICIAL
                         |                      |
                 stable <------------------------------------ stable
                         |              stable  |
                         |                      |
                  edge' <------------------------------------  edge'
                         |                edge  |
                         |                      |
                         |          jd/topic-1' |
               topic-2'  |          jd/topic-2  |

And then:

    JD-LOCAL             |        JD-FORK       |           OFFICIAL
                         |                      |
                 stable ------------->  stable  |             stable
                  edge' ------------->   edge'  |              edge'
                         |                      |
                         |                      |
                         |                      |
                       --------->               |
               topic-2'--------->   jd/topic-2' |

TODO:If the remote branch doesn't get removed like I showed in this graph (I'm yet to test this), try [this](http://github.com/guides/remove-a-remote-branch) and please update this section according to the truth.

Then Boss takes a look at *topic-2*; since it looks good to him and Hudson:<project>:edge is happy with it, Boss commits *topic-2* on OFFICIAL's edge.

    JD-LOCAL             |        JD-FORK       |           OFFICIAL
                         |                      |
                 stable  |              stable  |             stable
                  edge'  |               edge'  |             edge''
                         |                      |               ^
                         |                      |               |
               topic-2'  |         jd/topic-2'------------------/

## Making a new release

 * Boss: Congrats Team, OFFICIAL's edge is stable, let's call this a release!

Boss tries a merge of edge with stable on OFFICIAL, and waits for Hudson:<project>:*stable* to tell him all tests pass.

Boss commits and tags this new release.

    JD-LOCAL             |        JD-FORK       |           OFFICIAL
                         |                      |
                 stable  |              stable  |            stable'
                         |                      |               ^
                         |                      |               |  
                  edge'  |               edge'  |             edge''
                         |                      |
               topic-2'  |         jd/topic-2'  |

## Rinsing and repeating

Some time later, John starts work on *topic-3*...

    JD-LOCAL             |        JD-FORK       |           OFFICIAL
                         |                      |
                stable' <----------------------------------- stable'
                         |              stable  |
                 edge'' <-----------------------------------  edge''
                         |               edge'  |
                         |                      |
                         |         jd/topic-2'  |
                topic-3  |                      |

## Grab the following help scripts!

## Advantages of using this workflow

The advantages of this set of practices are many:

* Everyone works within their own repository
* Everyone works on their own schedule
    * There's no process waiting to be completed that blocks *me* from moving on to whatever *I* need/want to do next
    * *I'm* not forcing anyone to drop what they're doing right now to handle *my* pull requests

* Moreover:
    * Each repository is on an equal footing
        * In particular, we would like every fork to have the same stable branch, so that if the official repository should ever be lost, there would be plenty of redundant backups
    * We also want it to be easy for each developer to pull in changes from the official repository
    * Finally, it's a bad idea in general to work on the stable branch; experienced git users typically work on separate development branches and then merge those branches into stable when they're done

* The extra work is worth the effort, because with this configuration:
    * *My* changes will be easily identifiable in *My* named branch
    * *I* can easily get updates from the official repository
    * Any updates *I*'ve pulled into stable and edge are automatically pushed up to *my* fork on the server
    * The simple 'git push' command will push up changes for all local branches that have a matching branch on the remote
    * If *I* make it a point to pull in updates to my local stable and edge but not work directly on them, my fork will match up with the official repository

* So what is the benefit of all this to our team's projects?
    * The easier it is for *me* to pull in updates, the more likely it will be that the pull request will be for code that merges easily with the latest releases
    * *I* can tell if someone is pulling updates by looking at their stable and edge branches and seeing if they match up with the latest branches on the official repository
    * By getting *myself* in the habit of working on branches, the team is going to get better, more organized code contributions

# References

* **General**
    * [Git-SCM](http://www.git-scm.com) - Download, Community Book, Tutorials, Reference
    * [GitHub Guides](http://github.com/guides/home)

* **Cheat Sheets**
    * [Git Cheat Sheet](http://zrusin.blogspot.com/2007/09/git-cheat-sheet.html) - see this page's attachments for a cleanly resized version

* **Tutorials**
    * [Official - Tutorial](http://www.kernel.org/pub/software/scm/git/docs/gittutorial.html)
    * [Official - Git for SVN Users](http://git.or.cz/course/svn.html)
    * [Official - Everyday Git with 20 commands](http://www.kernel.org/pub/software/scm/git/docs/everyday.html)
    * [Git for The Lazy](http://www.spheredev.org/wiki/Git_for_the_lazy)
    * [A Tour of Git - The Basics](http://cworth.org/hgbook-git/tour)
    * [An introduction to git-svn for SVN deserters](http://utsl.gen.nz/talks/git-svn/intro.html)

* **Links**
    * [Git Resources](https://37s.backpackit.com/pub/1465067)
    * [Git-SCM's Documentation Links](http://www.git-scm.com)

* **Tutorials - More advanced stuff**
    * [Git Awesomeness - Git Rebase --interactive](http://blog.madism.org/index.php/2007/09/09/138-git-awsome-ness-git-rebase-interactive)
    * [The Thing About Git](http://tomayko.com/writings/the-thing-about-git)
    * [Official - Git How To](http://www.kernel.org/pub/software/scm/git/docs/howto-index.html) - Advanced stuff

* **Reference**
    * [Official - Man Pages](http://www.kernel.org/pub/software/scm/git/docs/)
    * [Official - FAQ](http://git.or.cz/gitwiki/GitFaq)
    * [Git for The Confused](http://www.gelato.unsw.edu.au/archives/git/0512/13748.html)
    * [Git-SCM's Documentation Links](http://www.git-scm.com)

* **Screencasts** - A great, quick way to understand Git
    * [Git Casts](http://gitcasts.com/)
    * Community Book (through Git-SCM site link upwards) - in HTML version only
    * [Peepcode's Git Screencast](http://peepcode.com/products/git) (Costs 9$)

* **Deep Understanding / Books**
    * [Git for Computer Scientists](http://eagain.net/articles/git-for-computer-scientists/) - small
    * [Git From Bottom Up](http://www.newartisans.com/blog_assets/git.from.bottom.up.pdf) (PDF) - Useful, deep understanding coverage
    * Git From Bottom Up (PDF with my yellow highlightings) - see this page's attachments to download it
    * [Git Magic](http://www-cs-students.stanford.edu/~blynn/gitmagic/)
    * [Git-SCM's Community Book](http://www.git-scm.com) - Always up-to-date; Wide coverage
    * [Peepcode's Git Internals Book](http://peepcode.com/products/git-internals-pdf) (Costs 9$) - Complementary; Wide coverage

* **Workflow**
    * [Our Git Deployment Workflow](http://www.brynary.com/2008/8/3/our-git-deployment-workflow)

* **Hosting**
    * [GitHub](http://www.github.com) - Commercial Web Front; Free only for Public-Small; Highly social site; Very useful; Pleasing to use
    * [Gitorious](http://www.gitorious.org) - Open Source Web Front; Free hosting
    * [Repo.or.cz](http://repo.or.cz) - Git Web Front - Free hosting

* **Java**
    * JGit - The most complete implementation of Git in any other language (Java).
    * [Maven-enabled project hosting with GitHub](http://www.jroller.com/mrdon/entry/maven_enabled_project_hosting_with)

* **Best Practices**
    * [Setting up your repositorys for shared projects](http://blog.insoshi.com/2008/10/14/setting-up-your-git-repositories-for-open-source-projects-at-github/)
