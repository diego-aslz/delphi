---
layout: post
title: "Git - The awesome version control manager system"
category: posts
---

Hi,

I had to make a little presentation about the Git version control manager system to my coworkers.
Using that, I decided to create a simple post to give readers a general idea of how this
amazing version manager works and give my coworkers a place to look at when they need to
remember some Git command. So, let's start.

## The system

Git is a distributed version control system that makes the creation of a repository and
the maintenance of it much easier than SVN does. To have it working in your (Linux)
system, you will have to:

{% highlight bash %}
sudo apt-get install git git-core
{% endhighlight %}

## Creating a repository

Now, let's create a repository. I'll do it in a `git` folder in my `home` directory:

{% highlight bash %}
cd
mkdir git
cd git
mkdir myproject
cd myproject
{% endhighlight %}

We are inside an empty folder, my project's base folder, named `myproject`. We want
to add it to our version control:

{% highlight bash %}
git init .
{% endhighlight %}

That's it, this folder is now a git repository. Yeah, simple as that. You can type this
command in a populate folder as well, but this just initialize the repository, we still
have to add some content in it.

## Your first commit

The repository is still empty. So, let's create some content to add:

{% highlight bash %}
touch application.rb
{% endhighlight %}

Ok, now we have a file to control. If you type now `git status`, you will see it under the
`Untracked files` session. That's because this file is not under version control. So, let's
add it to the git control:

{% highlight bash %}
git add application.rb
{% endhighlight %}

Now, if you type `git status`, you'll see:

{% highlight bash %}
# On branch master
#
# Initial commit
#
# Changes to be committed:
#   (use "git rm --cached <file>..." to unstage)
#
#	new file:   application.rb
#
{% endhighlight %}

Notice that, now, the file is in the `Changes to be commited` session. That means that
now git *knows* about the file. Nevertheless, the file is still pending for commit:

{% highlight bash %}
git commit -m "Adding my first file"
{% endhighlight %}

The `commit` command commits whatever changes are listed under the `Changes to be commited`
session. The `-m` option specifies the message of the commit that, in this case,
is "Adding my first file". If you type now `git log`, you will see:

{% highlight bash %}
commit 49785160cee943bfd0c5c53be1d4b798997d20a0
Author: Diego Aguir Selzlein <your@mail.com>
Date:   Fri Jun 28 14:02:54 2013 -0300

    Adding my first file

{% endhighlight %}

Now, if you enter `git status` again, it will say `nothing to commit`. If you are
used to SVN, notice that we didn't create any server, we didn't enter any URL of
a remote repository. That's because git is independent of that. This folder is
the repository. You can zip it, take to another computer, unzip, install git and
use it with no problem at all.

## New commits

Now, lets change our file to simulate some evolution in our project:

{% highlight bash %}
echo 123 > application.rb
{% endhighlight %}

After that, `git status` will show us that `application.rb` is modified, but it
will be under `Changes not staged for commit` section. That means that `git
commit` won't consider it yet. We could enter, again, `git add application.rb`
to add the file to our next commit, but there is a simpler way:

{% highlight bash %}
git commit -a -m "A message"
{% endhighlight %}

The `-a` option makes that, before the commit is done, our changed files get
added to the commit. It's important to know that this option doesn't consider
new files, you will have to add them with a `git add` command. If you created
a lot of files and don't want to add them one by one, just enter `git add -A`.
This will add them all at once.

## Branches

Git has a very good branching control. If you enter:

{% highlight bash %}
git branch
{% endhighlight %}

You'll see all the branches your repository has. By default, there will be
just one branch named `master`. This is the main branch, like the `trunk`
in SVN. I'm not going to teach about branches in Git here. Instead, I want
you to read [this article][1] before you continue.

## Remote server

Now let's talk about having a central repository (or remote repository). I won't
teach here how to install and configure a Git Server, maybe in another post.
Therefore, I'll assume you already have it somewhere (you've heard
about [Github][2], haven't you?) and you have the URL of an empty repository.

There is this command in git: `git remote`. Type it in the repository we've
created and you'll see:

{% highlight bash %}
{% endhighlight %}

Exactly, you will see nothing. That's because our repo doesn't have any remote
servers attached to it. `git remote` holds a bunch of commands that help you
configuring a remote server repository.

Assuming the URL of our remote repository is git@version.company.com:myproject.git
we add it this way:

{% highlight bash %}
git remote add origin git@version.company.com:myproject.git
{% endhighlight %}

The `add` command adds (don't you say) a new remote repository to our local one.
The `origin` text is just the name we give to this remote repository, so you
can reference it when sending and obtaining commits, that we'll see in a minute.
You can give any name you want, this name doesn't need to match anything, you can
call this `robert` if you will, but you'd better not do that. As a convention,
`origin` should be the name of the main remote repository and all projects that
have remote repositories should have one called `origin`.

After we added the remote repository, we can `push` our local one to it. When
you run the `push` command, all changes made in your local repository since
the last time you run this command will be sent to the remote one.

{% highlight bash %}
git push -u origin master
{% endhighlight %}

The `origin` parameter identifies the remote repository we want to push to.
The `master` one identifies what branch we want to use. If your repository is
an empty one, the branch `master` will be created and it'll receive all of what
is in your local `master` branch.

By default, every time you make a push, you have to type `origin master` after
it. The `-u` flag makes git keep track of this upstream, so that in the next
time you do a `push`, you will just have to type `git push`.

Alright. Let's suppose somebody made a change in your repo and you want to
update your local repository. You just need to run:

{% highlight bash %}
git pull
{% endhighlight %}

This will update your local files. In this command, you also can specify from
what repository and branch you want to pull changes with `git pull origin master`.
However, if you used the `-u` flag like I did before, that will not be necessary
since the `origin` will be the default remote.

## Cloning

Now, if you need to get this repository in another machine, you will use the
`git clone` command. All you have to do is go to the other machine, access
the folder you want the repository to be in and run this:

{% highlight bash %}
git clone git@version.company.com:myproject.git
{% endhighlight %}

*NOTE*: This may not work depending on how your git users are set, but my
focus here is to explain git and not how to setup a git server and users.

This will automatically create a folder with your repo name `myproject` in the
current folder. Inside of it, you'll have a regular local git repository.
You can make changes, commits, pushes and pulls. These last two you can run
without specifying the remote repo, since `origin` will already be created
and set as the default upstream. This is done by the `clone` command.

Well, I think that's more than enough for one post. I hope you enjoyed it.

[1]:http://git-scm.com/book/en/Git-Branching-Basic-Branching-and-Merging
[2]:http://github.com/
