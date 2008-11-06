[Practical Git for People and Corporations](../../README.md) - [Table of Contents](../README.md) - Administrators Guide

# Initial Setup

## Setting up a gitosis server

### Downloading and Installing gitosis

    $ git clone git://eagain.net/gitosis.git
    $ cd gitosis
TODO:
    
# Creating new repositories

* Boss: Suzy Kue, please set up a repository for me and John Doe. It'll be called -project-!
* Suzy: Boss, it'll be done in no time!

Suzy **adds** the following lines to `gitosis-admin/gitosis.conf`:

    [group -project-writers]
    members = boss
    writable = -path-/-project-

    [group -project-readers]
    members = sk@linux3          # Suzy Kue
    readonly = -path-/-project-

    [repo -project-]             # -project- matches 'writable' and 'readonly'
	daemon = no
	gitweb = yes
	owner = Boss
	description = Our blah bla blah

Then, she runs:

    $ git commit -a -m "Added -project-"
    $ git push

The `git push` makes gitosis aware of -project-'s repository/ies.

Suzy then goes on to create the official repository:

    $ mkdir -p -path-/-project-/official
    $ cd       -path-/-project-/official
    $ git init
    $ git remote add origin git@-host-:-path-/-project-/official.git
    
    $ touch README   # add at least one file to be able to commit

    $ git add .
    $ git commit -m "Created this repository"
    $ git push origin stable:refs/heads/stable

The `git push` makes gitosis create the served repository. It will later be referred to git as `-project-/official.git`

At the same time, Suzy also creates *fork* repositories for each member:

    $ mkdir -p -path-/-project-/boss
    $ cd       -path-/-project-/boss
    $ git init
    $ git remote add origin git@-host-:-path-/-project-/boss.git
    
    $ touch README   # add at least one file to be able to commit

    $ git add .
    $ git commit -m "Created this repository"
    $ git push origin stable:refs/heads/stable

Noticing this is becoming quite repetitive, Suzy writes herself the following [create-project-repo shell script](TODO:), and uses it to create the last *fork* repository:

    $ create-project-repo -host- -path-/-project-/sk

Now Suzy knows that in the future, after having added a new project to `gitosis-admin/gitosis.conf`, the following shell command is all she will need to use to create a few repositories:

    $ create-project-repo -host- -path-/-project-/official
    $ create-project-repo -host- -path-/-project-/boss
    $ create-project-repo -host- -path-/-project-/sk

# Adding blessed users

* Boss: Suzy, please set me up with `git push` permissions for -project-!
* Suzy: Boss, it'll be done in no time!

Boss is going to be the only one who has write (push) permissions on -project-.git.

Suzy gathers Boss's **public** SSH key and gives it to gitosis:

    $ mv id_rsa.pub gitosis-admin/keydir/boss.pub

Then, Suzy adds the following line to the `[group -project-]` section of `gitosis-admin/gitosis.conf`:

    members = boss

Finally, commit and push: 

    $ git add keydir/alice.pub keydir/bob.pub
    $ git commit -m "Granted Alice and Bob commit rights to -project-"
    $ git push

# References

* **Reference**
    * [Official - Man Pages](http://www.kernel.org/pub/software/scm/git/docs/)
    * [Official - FAQ](http://git.or.cz/gitwiki/GitFaq)

* **Serving** - Don't forget: touch proj.git/git-daemon-export-ok
    * Bare HTML read-only
    * [Git daemon](http://www.kernel.org/pub/software/scm/git/docs/git-daemon.html) (bare/manual, httpd, inetd)
    * [Gitosis](http://www.urbanpuddle.com/articles/2008/07/11/installing-git-on-a-server-ubuntu-or-debian)

* **Hosting**
    * [GitHub](http://www.github.com) - Commercial Web Front; Free only for Public-Small; Highly social site; Very useful; Pleasing to use
    * [Gitorious](http://www.gitorious.org) - Open Source Web Front; Free hosting
    * [Repo.or.cz](http://repo.or.cz) - Git Web Front - Free hosting
