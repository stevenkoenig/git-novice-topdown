---
title: Exploring History
teaching: 25
exercises: 0
questions:
- "How can I identify old versions of files?"
- "How do I review my changes?"
- "How can I recover old versions of files?"
objectives:
- "Explain what the HEAD of a repository is and how to use it."
- "Identify and use Git commit numbers."
- "Compare various versions of tracked files."
- "Restore old versions of files."
keypoints:
- "`git diff` displays differences between commits."
- "`git checkout` recovers old versions of files."
---

As we saw in the previous lesson, we can refer to commits by their
identifiers.  You can refer to the _most recent commit_ of the working
directory by using the identifier `HEAD`.

We've been adding one line at a time to `ingredients.txt`, so it's easy to track our
progress by looking, so let's do that using our `HEAD`s.  Before we start,
let's make a change to `ingredients.txt`.

~~~
$ nano ingredients.txt
$ cat ingredients.txt
~~~
{: .bash}

~~~
4 avocados
salt
1/2 onion
cilantro
pepper
1/2 tomato
~~~
{: .output}

Now, let's see what we get.

~~~
$ git diff HEAD ingredients.txt
~~~
{: .bash}

~~~
diff --git a/ingredients.txt b/ingredients.txt
index a18d584..8b1782d 100644
--- a/ingredients.txt
+++ b/ingredients.txt
@@ -3,3 +3,4 @@ salt
 1/2 onion
 cilantro
 pepper
+1/2 tomato
~~~
{: .output}

which is the same as what you would get if you leave out `HEAD` (try it).  The
real goodness in all this is when you can refer to previous commits.  We do
that by adding `~1` to refer to the commit one before `HEAD`.

~~~
$ git diff HEAD~1 ingredients.txt
~~~
{: .bash}

If we want to see the differences between older commits we can use `git diff`
again, but with the notation `HEAD~1`, `HEAD~2`, and so on, to refer to them:


~~~
$ git diff HEAD~2 ingredients.txt
~~~
{: .bash}

~~~
diff --git a/ingredients.txt b/ingredients.txt
index 0c5ef26..8b1782d 100644
--- a/ingredients.txt
+++ b/ingredients.txt
@@ -1,3 +1,6 @@
 4 avocados
 salt
 1/2 onion
+cilantro
+pepper
+1/2 tomato
~~~
{: .output}

We could also use `git show` which shows us what changes we made at an older commit as well as the commit message, rather than the _differences_ between a commit and our working directory that we see by using `git diff`.

~~~
$ git show HEAD~2 ingredients.txt
~~~
{: .bash}

~~~
commit 29980dee91ca6b5588153a22598aafddc1a55bc2
Author: The Guacmaster <guacmaster@guac.guac>
Date:   Thu Aug 3 15:49:06 2017 -0400

    Add onions

diff --git a/ingredients.txt b/ingredients.txt
index 075e446..0c5ef26 100644
--- a/ingredients.txt
+++ b/ingredients.txt
@@ -1,2 +1,3 @@
 4 avocados
 salt
+1/2 onion
~~~
{: .output}

In this way,
we can build up a chain of commits.
The most recent end of the chain is referred to as `HEAD`;
we can refer to previous commits using the `~` notation,
so `HEAD~1` (pronounced "head minus one")
means "the previous commit",
while `HEAD~123` goes back 123 commits from where we are now.

We can also refer to commits using
those long strings of digits and letters
that `git log` displays.
These are unique IDs for the changes,
and "unique" really does mean unique:
every change to any set of files on any computer
has a unique 40-character identifier.
Our first commit was given the ID
172a2f0d1da2034b94fb3a47ad182bfc04172caf,
so let's try this:

~~~
$ git diff 172a2f0d1da2034b94fb3a47ad182bfc04172caf ingredients.txt
~~~
{: .bash}

~~~
diff --git a/ingredients.txt b/ingredients.txt
index 075e446..8b1782d 100644
--- a/ingredients.txt
+++ b/ingredients.txt
@@ -1,2 +1,6 @@
 4 avocados
 salt
+1/2 onion
+cilantro
+pepper
+1/2 tomato
~~~
{: .output}

That's the right answer,
but typing out random 40-character strings is annoying,
so Git lets us use just the first few characters:

~~~
$ git diff 172a2f0 ingredients.txt
~~~
{: .bash}

~~~
diff --git a/ingredients.txt b/ingredients.txt
index 075e446..8b1782d 100644
--- a/ingredients.txt
+++ b/ingredients.txt
@@ -1,2 +1,6 @@
 4 avocados
 salt
+1/2 onion
+cilantro
+pepper
+1/2 tomato
~~~
{: .output}

All right! So
we can save changes to files and see what we've changed—now how
can we restore older versions of things?
Let's suppose we accidentally add an ingredient we really don't want:

~~~
$ nano ingredients.txt
$ cat ingredients.txt
~~~
{: .bash}

~~~
4 avocados
salt
1/2 onion
cilantro
pepper
1/2 tomato
3 cups cayenne pepper
~~~
{: .output}

`git status` now tells us that the file has been changed,
but those changes haven't been staged:

~~~
$ git status
~~~
{: .bash}

~~~
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	modified:   ingredients.txt

no changes added to commit (use "git add" and/or "git commit -a")
~~~
{: .output}

We can put things back the way they were
by using `git checkout`:

~~~
$ git checkout HEAD ingredients.txt
$ cat ingredients.txt
~~~
{: .bash}

~~~
4 avocados
salt
1/2 onion
cilantro
pepper
~~~
{: .output}

As you might guess from its name,
`git checkout` checks out (i.e., restores) an old version of a file.
In this case,
we're telling Git that we want to recover the version of the file recorded in `HEAD`,
which is the last saved commit.
If we want to go back even further,
we can use a commit identifier instead:

~~~
$ git checkout 172a2f0 ingredients.txt
~~~
{: .bash}

~~~
$ cat ingredients.txt
~~~
{: .bash}

~~~
4 avocados
salt
~~~
{: .output}

~~~
$ git status
~~~
{: .bash}

~~~
# On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)
# Changes not staged for commit:
#   (use "git add <file>..." to update what will be committed)
#   (use "git checkout -- <file>..." to discard changes in working directory)
#
#	modified:   ingredients.txt
#
no changes added to commit (use "git add" and/or "git commit -a")
~~~
{: .output}

Notice that the changes are on the staged area.
Again, we can put things back the way they were
by using `git checkout`:

~~~
$ git checkout -f master ingredients.txt
~~~
{: .bash}

> ## Don't Lose Your HEAD
>
> Above we used
>
> ~~~
> $ git checkout 172a2f0 ingredients.txt
> ~~~
> {: .bash}
>
> to revert `ingredients.txt` to its state after the commit `172a2f0`.
> If you forget `ingredients.txt` in that command, Git will tell you that "You are in
> 'detached HEAD' state." In this state, you shouldn't make any changes.
> You can fix this by reattaching your head using ``git checkout master``
{: .callout}

It's important to remember that
we must use the commit number that identifies the state of the repository
*before* the change we're trying to undo.
A common mistake is to use the number of
the commit in which we made the change we're trying to get rid of.
In the example below, we want to retrieve the state from before the most
recent commit (`HEAD~1`), which is commit `172a2f0`:

![Git Checkout](../fig/git-checkout.svg)

So, to put it all together,
here's how Git works in cartoon form:

![http://figshare.com/articles/How_Git_works_a_cartoon/1328266](../fig/git_staging.svg)

> ## Simplifying the Common Case
>
> If you read the output of `git status` carefully,
> you'll see that it includes this hint:
>
> ~~~
> (use "git checkout -- <file>..." to discard changes in working directory)
> ~~~
> {: .bash}
>
> As it says,
> `git checkout` without a version identifier restores files to the state saved in `HEAD`.
> The double dash `--` is needed to separate the names of the files being recovered
> from the command itself:
> without it,
> Git would try to use the name of the file as the commit identifier.
{: .callout}

The fact that files can be reverted one by one
tends to change the way people organize their work.
If everything is in one large document,
it's hard (but not impossible) to undo changes to the introduction
without also undoing changes made later to the conclusion.
If the introduction and conclusion are stored in separate files,
on the other hand,
moving backward and forward in time becomes much easier.

> ## Recovering Older Versions of a File
>
> Jennifer has made changes to the Python script that she has been working on for weeks, and the
> modifications she made this morning "broke" the script and it no longer runs. She has spent
> ~ 1hr trying to fix it, with no luck...
>
> Luckily, she has been keeping track of her project's versions using Git! Which commands below will
> let her recover the last committed version of her Python script called
> `data_cruncher.py`?
>
> 1. `$ git checkout HEAD`
>
> 2. `$ git checkout HEAD data_cruncher.py`
>
> 3. `$ git checkout HEAD~1 data_cruncher.py`
>
> 4. `$ git checkout <unique ID of last commit> data_cruncher.py`
>
> 5. Both 2 and 4
{: .challenge}

> ## Reverting a Commit
>
> Jennifer is collaborating on her Python script with her colleagues and
> realizes her last commit to the group repository is wrong and wants to
> undo it.  Jennifer needs to undo correctly so everyone in the group
> repository gets the correct change.  `git revert [wrong commit ID]`
> will make a new commit that undoes Jennifer's previous wrong
> commit. Therefore `git revert` is different than `git checkout [commit
> ID]` because `checkout` is for local changes not committed to the
> group repository.  Below are the right steps and explanations for
> Jennifer to use `git revert`, what is the missing command?
>
> 1. ________ # Look at the git history of the project to find the commit ID
>
> 2. Copy the ID (the first few characters of the ID, e.g. 0b1d055).
>
> 3. `git revert [commit ID]`
>
> 4. Type in the new commit message.
>
> 5. Save and close
{: .challenge}

> ## Understanding Workflow and History
>
> What is the output of cat equipment.txt at the end of this set of commands?
>
> ~~~
> $ cd guac
> $ nano equipment.txt #input the following text: mixing bowl
> $ git add equipment.txt
> $ nano equipment.txt #add the following text: spoon
> $ git commit -m "Compile the utensils we need"
> $ git checkout HEAD equipment.txt
> $ cat equipment.txt #this will print the contents of equipment.txt to the screen
> ~~~
> {: .bash}
>
> 1.
>
> ~~~
> spoon
> ~~~
> {: .output}
>
> 2.
>
> ~~~
> mixing bowl
> ~~~
> {: .output}
>
> 3.
>
> ~~~
> mixing bowl
> spoon
> ~~~
> {: .output}
>
> 4.
>
> ~~~
> Error because you have changed equipment.txt without committing the changes
> ~~~
> {: .output}
>
> > ## Solution
> >
> > Line by line:
> > ~~~
> > $ cd guac
> > ~~~
> > {: .bash}
> > Enters into the 'guac' directory
> >
> > ~~~
> > $ nano equipment.txt #input the following text: mixing bowl
> > ~~~
> > {: .bash}
> > We created a new file and wrote a sentence in it, but the file is not tracked by git.  
> >
> > ~~~
> > $ git add equipment.txt
> > ~~~
> > {: .bash}
> > Now the file is staged. The changes that have been made to the file until now will be committed in the next commit.
> >
> > ~~~
> > $ nano equipment.txt #add the following text: spoon
> > ~~~
> > {: .bash}
> > The file has been modified. The new changes are not staged because we have not added the file.
> >
> > ~~~
> > $ git commit -m "Compile the utensils we need"
> > ~~~
> > {: .bash}
> > The changes that were staged (mixing bowl) have been committed. The changes that were not staged (spoon) have not. Our local working copy is different than the copy in our local repository.
> >
> > ~~~
> > $ git checkout HEAD equipment.txt
> > ~~~
> > {: .bash}
> > With checkout we discard the changes in the working directory so that our local copy is exactly the same as our HEAD, the most recent commit.
> >
> > ~~~
> > $ cat equipment.txt #this will print the contents of equipment.txt to the screen
> > ~~~
> > {: .bash}
> > If we print equipment.txt we will get answer 2.
> >
> {: .solution}
{: .challenge}

> ## Checking Understanding of `git diff`
>
> Consider this command: `git diff HEAD~3 ingredients.txt`. What do you predict this command
> will do if you execute it? What happens when you do execute it? Why?
>
> Try another command, `git diff [ID] ingredients.txt`, where [ID] is replaced with
> the unique identifier for your most recent commit. What do you think will happen,
> and what does happen?
{: .challenge}

> ## Getting Rid of Staged Changes
>
> `git checkout` can be used to restore a previous commit when unstaged changes have
> been made, but will it also work for changes that have been staged but not committed?
> Make a change to `ingredients.txt`, add that change, and use `git checkout` to see if
> you can remove your change.
{: .challenge}

> ## Explore and Summarize Histories
>
> Exploring history is an important part of git, often it is a challenge to find
> the right commit ID, especially if the commit is from several months ago.
>
> Imagine the `guac` project has more than 50 recipes that we've collected from friends and family.
> You would like to find a commit when specific text in `ingredients.txt` was modified.
> When you type `git log`, a very long list appeared,
> How can you narrow down the search?
>
> Recorded that the `git diff` command allow us to explore one specific file,
> e.g. `git diff ingredients.txt`. We can apply the similar idea here.
>
> ~~~
> $ git log ingredients.txt
> ~~~
> {: .bash}
>
> Unfortunately some of these commit messages are very ambiguous e.g. `update files`.
> How can you search through these files?
>
> Both `git diff` and `git log` are very useful and they summarize different part of the history for you.
> Is that possible to combine both? Let's try the following:
>
> ~~~
> $ git log --patch ingredients.txt
> ~~~
> {: .bash}
>
> You should get a long list of output, and you should be able to see both commit messages and the difference between each commit.
>
> Question: What does the following command do?
>
> ~~~
> $ git log --patch HEAD~3 *.txt
> ~~~
> {: .bash}
{: .challenge}
