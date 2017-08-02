---
title: Tracking Changes
teaching: 20
exercises: 10
questions:
- "How do I record changes in Git?"
- "How do I check the status of my version control repository?"
- "How do I record notes about what changes I made and why?"
objectives:
- "Go through the modify-add-commit cycle for one or more files."
- "Explain where information is stored at each stage of Git commit workflow."
keypoints:
- "`git status` shows the status of a repository."
- "Files can be stored in a project's working directory (which users see), the staging area (where the next commit is being built up) and the local repository (where commits are permanently recorded)."
- "`git add` puts files in the staging area."
- "`git commit` saves the staged content as a new commit in the local repository."
- "Always write a log message when committing changes."
---

Let's create a file called `ingredients.txt` that contains some notes
about the ingredients we want. (We'll use `nano` to edit the file,
but you can use whatever editor you like.)

~~~
$ nano ingredients.txt
~~~
{: .bash}

Type the text below into the `ingredients.txt` file:

~~~
4 avocados
1/2 onion
cilantro
salt
pepper
~~~
{: .output}

`ingredients.txt` now contains several lines, which we can see by running:

~~~
$ ls
~~~
{: .bash}

~~~
ingredients.txt
~~~
{: .output}

~~~
$ cat ingredients.txt
~~~
{: .bash}

~~~
4 avocados
1/2 onion
cilantro
salt
pepper
~~~
{: .output}

If we check the status of our project again,
Git tells us that it's noticed the new file:

~~~
$ git status
~~~
{: .bash}

~~~
On branch master

Initial commit

Untracked files:
   (use "git add <file>..." to include in what will be committed)

	ingredients.txt
nothing added to commit but untracked files present (use "git add" to track)
~~~
{: .output}

The "untracked files" message means that there's a file in the directory
that Git isn't keeping track of.
We can tell Git to track a file using `git add`:

~~~
$ git add ingredients.txt
~~~
{: .bash}

and then check that the right thing happened:

~~~
$ git status
~~~
{: .bash}

~~~
On branch master

Initial commit

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)

	new file:   ingredients.txt

~~~
{: .output}

Git now knows that it's supposed to keep track of `ingredients.txt`,
but it hasn't recorded these changes as a commit yet.
To get it to do that,
we need to run one more command:

~~~
$ git commit -m "Start our shopping list"
~~~
{: .bash}

~~~
[master (root-commit) f22b25e] Start notes on conversion tools
 1 file changed, 5 insertion(+)
 create mode 100644 ingredients.txt
~~~
{: .output}

When we run `git commit`, Git takes everything we have told it to
 save by using `git add` and stores a copy permanently inside the
  special `.git` directory. This permanent copy is called a '`commit`'
 (or '`revision`') and its short identifier is `bb13e9f`
 (Your commit will have another identifier of similar length.)

We use the '`-m`' flag (for "message") to record a short, descriptive,
 and specific comment that will help us remember what we did and why.
 If we just run `git commit` without the `-m` option, Git will launch
 `nano` (or whatever other editor we configured as `core.editor`)
 so that we can write a longer message.

[Good commit messages][commit-messages] start with a brief
 (<50 characters) summary of changes made in the commit.  If you want
 to go into more detail, add a blank line between the summary line and
 your additional notes.

If we run `git status` now:

~~~
$ git status
~~~
{: .bash}

~~~
On branch master
nothing to commit, working directory clean
~~~
{: .output}

it tells us everything is up to date.
If we want to know what we've done recently,
we can ask Git to show us the project's history using `git log`:

~~~
$ git log
~~~
{: .bash}

~~~
commit bb13e9f40d12bddc307c7052e31ede7624dc9f4a
Author: Becca Love <rlove1@nd.edu>
Date:   Wed Aug 2 17:41:33 2017 -0400

    Start our shopping list
~~~
{: .output}

`git log` lists all commits  made to a repository in reverse
 chronological order. The listing for each commit includes
 the commit's full identifier (which starts with the same characters as
 the short identifier printed by the `git commit` command earlier),
 the commit's author, when it was created, and the log message Git was
 given when the commit was created.

> ## Where Are My Changes?
>
> If we run `ls` at this point, we will still see just one file called `ingredients.txt`.
> That's because Git saves information about files' history
> in the special `.git` directory mentioned earlier
> so that our filesystem doesn't become cluttered
> (and so that we can't accidentally edit or delete an old version).
{: .callout}

Now suppose we add more information to the `ingredients.txt` file.

~~~
$ nano ingredients.txt
$ cat ingredients.txt
~~~
{: .bash}

~~~
4 avocados
1/2 onion
cilantro
salt
pepper
1/2 tomato
~~~
{: .output}

When we run `git status` now,
it tells us that a file it already knows about has been modified:

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

The last line is the key phrase: "no changes added to commit".
 We have changed this file, but we haven't told Git we will want to
 save those changes (which we do with `git add`) nor have we saved
 them (which we do with `git commit`). So let's do that now. It is
 good practice to always review our changes before saving them. We do
 this using `git diff`. This shows us the differences between the
 current state of the file and the most recently saved version:

~~~
$ git diff
~~~
{: .bash}

~~~
diff --git a/ingredients.txt b/ingredients.txt
index 946aaff..a97a372 100644
--- a/ingredients.txt
+++ b/ingredients.txt
@@ -3,3 +3,4 @@
 cilantro
 salt
 pepper
+1/2 tomato
~~~
{: .output}

The output is cryptic because it is actually a series of computer
 readable commands describing how to reconstruct one file given the
 other. If we break it down into pieces:

1.  The first line tells us that Git is producing output similar to the
    Unix `diff` command comparing the old and new versions of the file.
2.  The second line tells exactly which versions of the file
    Git is comparing; `946aaff` and `a97a372` are unique
    computer-generated labels for those versions.
3.  The third and fourth lines once again show the name of the file
    being changed.
4.  The remaining lines are the most interesting, they show us the
    actual differences and the lines on which they occur. In particular,
    the `+` marker in the first column shows where we added a line.

After reviewing our change, it's time to commit it:

~~~
$ git commit -m "Add tomatoes to the shopping list"
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

Whoops:
Git won't commit because we didn't use `git add` first.
Let's fix that:

~~~
$ git add ingredients.txt
$ git commit -m "Add another desirable conversion tool"
~~~
{: .bash}

~~~
[master 36b7fd8] Add tomatoes to the shopping list
 1 file changed, 1 insertion(+)
~~~
{: .output}

Git insists that we add files to the set we want to commit
 before actually committing anything. This allows us to commit our
 changes in stages and capture changes in logical portions rather than
 only large batches. For example, suppose we're adding a few citations
 to our supervisor's work to our thesis. We might want to commit those
 additions, and the corresponding addition to the bibliography,
 but *not* commit the work we're doing on the conclusion
 (which we haven't finished yet).

To allow for this, Git has a special *staging area* where it keeps
 track of things that have been added to the current change set
 but not yet committed.

> ## Staging Area
>
> If you think of Git as taking snapshots of changes over the life of
> a project, `git add` specifies *what* will go in a snapshot
> (putting things in the staging area),
> and `git commit` then *actually takes* the snapshot, and
> makes a permanent record of it (as a commit).
> If you don't have anything staged when you type `git commit`,
> Git will prompt you to use `git commit -a` or `git commit --all`,
> which is kind of like gathering *everyone* for the picture!
> However, it's almost always better to
> explicitly add things to the staging area, because you might
> commit changes you forgot you made. (Going back to snapshots,
> you might get the extra with incomplete makeup walking on
> the stage for the snapshot because you used `-a`!)
> Try to stage things manually,
> or you might find yourself searching for "git undo commit" more
> than you would like!
{: .callout}

![The Git Staging Area](../fig/git-staging-area.svg)

Let's watch as our changes to a file move from our editor
to the staging area and into long-term storage.
First, we'll add another line to the file:

~~~
$ nano ingredients.txt
$ cat ingredients.txt
~~~
{: .bash}

~~~
4 avocados
1/2 onion
cilantro
salt
pepper
1/2 tomato
habaneros
~~~
{: .output}

~~~
$ git diff
~~~
{: .bash}

~~~
diff --git a/ingredients.txt b/ingredients.txt
index a97a372..2156a4f 100644
--- a/ingredients.txt
+++ b/ingredients.txt
@@ -4,3 +4,4 @@ cilantro
 salt
 pepper
 1/2 tomato
+habaneros
~~~
{: .output}

So far, so good:
we've added one line to the end of the file
(shown with a `+` in the first column).
Now let's put that change in the staging area
and see what `git diff` reports:

~~~
$ git add ToDo.txt
$ git diff
~~~
{: .bash}

There is no output: as far as Git can tell,
there's no difference between what it's been asked to save permanently
and what's currently in the directory. However, if we do this:

~~~
$ git diff --staged
~~~
{: .bash}

~~~
diff --git a/ingredients.txt b/ingredients.txt
index a97a372..2156a4f 100644
--- a/ingredients.txt
+++ b/ingredients.txt
@@ -4,3 +4,4 @@ cilantro
 salt
 pepper
 1/2 tomato
+habaneros
~~~
{: .output}

it shows us the difference between
the last committed change
and what's in the staging area.
Let's save our changes:

~~~
$ git commit -m "Add some heat to this guac"
~~~
{: .bash}

~~~
[master a6cca18] Add some heat to this guac
 1 file changed, 1 insertion(+)
~~~
{: .output}

check our status:

~~~
$ git status
~~~
{: .bash}

~~~
On branch master
nothing to commit, working directory clean
~~~
{: .output}

and look at the history of what we've done so far:

~~~
$ git log
~~~
{: .bash}

~~~
commit a6cca18f221f51403466a1bb80a6871a97bae355
Author: Becca Love <rlove1@nd.edu>
Date:   Wed Aug 2 17:47:08 2017 -0400

    Add some heat to this guac

commit 36b7fd8487274799927350c64ed4b28374cb9c40
Author: Becca Love <rlove1@nd.edu>
Date:   Wed Aug 2 17:45:32 2017 -0400

    Add tomatoes to the shopping list

commit bb13e9f40d12bddc307c7052e31ede7624dc9f4a
Author: Becca Love <rlove1@nd.edu>
Date:   Wed Aug 2 17:41:33 2017 -0400

    Start our shopping list

commit 311116e95259eb2fe5eec1622c4960dce6c9dbcd
Author: rrlove <rrlove@users.noreply.github.com>
Date:   Wed Aug 2 17:02:27 2017 -0400

    Initial commit
~~~
{: .output}

> ## Directories
>
> Two important facts you should know about directories in Git.
>
> 1. Git does not track directories on their own, only files within them.
> Try it for yourself:
>
> ~~~
> $ mkdir directory
> $ git status
> $ git add directory
> $ git status
> ~~~
> {: .bash}
> 
> Note, our newly created empty directory `directory` does not appear in
> the list of untracked files even if we explicitly add it (_via_ `git add`) to our
> repository. This is the reason why you will sometimes see `.gitkeep` files
> in otherwise empty directories. Unlike `.gitignore`, these files are not special
> and their sole purpose is to populate a directory so that Git adds it to
> the repository. In fact, you can name such files anything you like.
>
> {:start="2"}
> 2. If you create a directory in your Git repository and populate it with files,
> you can add all files in the directory at once by:
>
> ~~~
> git add <directory-with-files>
> ~~~
> {: .bash}
>
{: .callout}

To recap, when we want to add changes to our repository,
we first need to add the changed files to the staging area
(`git add`) and then commit the staged changes to the
repository (`git commit`):

![The Git Commit Workflow](../fig/git-committing.svg)

> ## Choosing a Commit Message
>
> Which of the following commit messages would be most appropriate for the
> last commit made to `ingredients.txt`?
>
> 1. "Changes"
> 2. "Added line 'habaneros' to ingredients.txt"
> 3. "Add habaneros"
>
> > ## Solution
> > Answer 1 is not descriptive enough,
> > and answer 2 is too descriptive and redundant,
> > but answer 3 is good: short but descriptive.
> {: .solution}
{: .challenge}

> ## Committing Changes to Git
>
> Which command(s) below would save the changes of `myfile.txt`
> to my local Git repository?
>
> 1. `$ git commit -m "my recent changes"`
>
> 2. `$ git init myfile.txt`  
>    `$ git commit -m "my recent changes"`
>
> 3. `$ git add myfile.txt`  
>    `$ git commit -m "my recent changes"`
>
> 4. `$ git commit -m myfile.txt "my recent changes"`
>
> > ## Solution
> >
> > 1. Would only create a commit if files have already been staged.
> > 2. Would try to create a new repository.
> > 3. Is correct: first add the file to the staging area, then commit.
> > 4. Would try to commit a file "my recent changes" with the message myfile.txt.
> {: .solution}
{: .challenge}

> ## Committing Multiple Files
>
> The staging area can hold changes from any number of files
> that you want to commit as a single snapshot.
>
> 1. Add some text to `ingredients.txt` with your favorite additional ingredient
> 2. Create a new file `methods.txt` with single step in it referring to that ingredient
> 3. Add changes from both files to the staging area, and commit those changes
>
> > ## Solution
> >
> > First we make our changes to the `ingredients.txt` and `methods.txt` files:
> >
> > ~~~
> > $ nano ingredients.txt
> > $ cat ingredients.txt
> > ~~~
> > {: .bash}
> >
> > ~~~
> > 4 avocados
> > 1/2 onion
> > cilantro
> > salt
> > pepper
> > 1/2 tomato
> > habaneros
> > parsley
> > ~~~
> > {: .output}
> >
> > ~~~
> > $ nano methods.txt
> > $ cat methods.txt
> > ~~~
> > {: .bash}
> >
> > ~~~
> > 1. Chop the parsley roughly.
> > ~~~
> > {: .output}
> >
> > Now you can add both files to the staging area. We can do that in one line:
> >
> > ~~~
> > $ git add ingredients.txt methods.txt
> > ~~~
> > {: .bash}
> >
> > Or with multiple commands:
> >
> > ~~~
> > $ git add ingredients.txt
> > $ git add methods.txt
> > ~~~
> > {: .bash}
> >
> > Now the files are ready to commit. You can check that using `git status`. 
> > If you are ready to commit use:
> >
> > ~~~
> > $ git commit -m "Add parsley"
> > ~~~
> > {: .bash}
> >
> > ~~~
> > [master c878256] Add parsley
> > 2 files changed, 2 insertions(+)
> > create mode 100644 methods.txt
> > ~~~
> > {: .output}
> >
> {: .solution}
{: .challenge}

[commit-messages]: http://chris.beams.io/posts/git-commit/
