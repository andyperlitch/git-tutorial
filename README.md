# Git Learning Session

Andy Perlitch (perlitch@cisco.com, andyperlitch@gmail.com)
andyperlitch.net

## Global vs. Local Config

Configuration of git can exist on a **global level** or a **repository level**. The two files that store the state of your config are `~/.gitconfig` (for global config) vs. `<repo directory>/.git/config`. You don't usually have to edit these files directly though.

To modify a config in the current repository's `.git/config` file:

    git config user.name 'Richard Hendricks'


To modify the global `~/.gitconfig` file:

    git config --global user.name 'Erlich Bachman'


## Globally-ignored Files

The `.gitignore` file of a git project should only be responsible for "ignoring" its own, known artifacts. It shouldn't be responsible for files that a user's OS or editor generates (like `.DS_Store` on mac, or `.editor` files from (ehem) editors. Therefore, a git user is responsible for defining _globally-ignored files_.

Add a file called `.gitignore_global` to your home directory, and put files like `.DS_Store` in it.
Then run this command:

```
git config --global core.excludesfile ~/.gitignore_global
```

## Aliases

Make your git command-line life easier with **aliases**! Open your `~/.gitconfig` file and add as many as you like, under the `[alias]` section:

```
[alias]
    s = status
    st = status
    ts = status
    ci = commit
    co = checkout
    b = branch
    di = diff --color
    dc = diff --cached
    fa = fetch --all
    amend = commit --amend
    aa = add -A
    au = add -u
    money = fetch --all
    bo = for-each-ref --sort=committerdate refs/heads/ --format='%(HEAD) %(color:yellow)%(refname:short)%(color:reset) - %(color:red)%(objectname:short)%(color:reset) - %(contents:subject) - %(authorname) (%(color:green)%(committerdate:relative)%(color:reset))'
    unstage = reset HEAD
    squash = !sh -c 'git checkout origin/master && git merge --no-commit --squash $0 && git checkout -B $0 && git commit -e'
```


## Autocompletion

1. Download [git-completion.bash](https://raw.githubusercontent.com/git/git/master/contrib/completion/git-completion.bash) and name it `.git-completion.bash` in your home directory. 
2. Then, modify (or create) your `~/.bash_profile` file to include `.git-completion.bash` on terminal start:

   ```bash
   # somewhere in your .bash_profile
   if [[ -f ~/.git-completion.bash ]]; then
     . ~/.git-completion.bash
   fi
   ```

3. Close your current terminal and open a new one.

Now <kbd>tab</kbd> will auto-complete git commands, git branches, remote names, and more! This makes git usage _so_ much easier.



## Staging (`git add` variations)

Staging is prepping files for getting committed.

Many people use `git add .`, but this is dependent on the current working directory. So if you have `cd`'ed to a subdirectory of the project and make changes there, `git add .` will only add changes and new files in that directory. I find that it's better to use more precise commands


### Stage Modified: `git add -u`

Only stages tracked files that have been modified, i.e. **it does not add untracked files**. Most of the time I use this instead of `git add .`.


### Stage all: `git add -A`

Adds all tracked files that have been modified AND untracked files. This is equivalent to being in the root directory of the project and running `git add .`.
With `git add -A`, you don't have to worry about what directory you are in.


### Stage and Commit Modified: `git commit -am 'my message'`

Short-form of:

```bash
git add -u
git commit -m 'my message'
```



 ### Interactive Staging: `git add -p`

 This mode goes through every "chunk" of changes and asks if you want to stage it or not. It's a good way to review your changes before committing them, and it also helps when you have to split some work into multiple commits


## Managing Remote Branches

### What does `git fetch` do?

It gets the state of all branches on the remote repository.



### What is the difference between origin/<branch> and <branch>

`origin/<branch>` is not actually a branch, but a reference to the current state of `<branch>` on `origin`. You can checkout `origin/<branch>` directly to enter a "DETACHED HEAD state":

```bash
git checkout origin/some-remote-branch
```

The resulting message provides a decent explanation:

> You are in 'detached HEAD' state. You can look around, make experimental
> changes and commit them, and you can discard any commits you make in this
> state without impacting any branches by performing another checkout.
>
> If you want to create a new branch to retain commits you create, you may
> do so (now or later) by using -b with the checkout command again. Example:
>
> Checking out new branches automatically vs. DETACHED HEAD STATE to "just peak around"


Assuming you hav fetched, and do not have a local branch by the same name, you can automatically create a new branch set to tracking the remote branch by running:

```bash
git checkout <some-branch-name-from-remote>
```


### No need to ever checkout a local master branch




### Setting the upstream

A local branch can have an "upstream branch" assigned, which means that when you have this branch checked out, you can run `git push` without arguments and git will know which branch to push to. Same goes for `git pull`.


## Branching Lifecycle

```bash
git checkout origin/master
git checkout -b [NEW_BRANCH_NAME]

# make changes, commits, stashes
git fetch
git rebase origin/master

# When ready to open PR, first squash your commits (many ways to do this, google is your friend)
# Be sure to fetch and rebase right before this
git reset origin/master
git add -A
git commit -m 'Fixes major bug blah blah'

# Push your new branch to the remote, set the newly created remote branch as this local branch's upstream with `-u`
git push origin [BRANCH_NAME] -u

# make changes as per PR comments/tasks, then:
git add -u            # add changes
git commit --amend    # amend the commit
git push -f           # since `git commit --amend` replaces the commit, you will need to force push
```

## Stashing

Stashing allows you to save some WIP without adding a commit. Keep in mind though that a temporary commit can sometimes be preferable.

### Stashing Changes

By default, `git stash` will store the modifications to tracked files. _It does not stash untracked files_. 

```bash
git stash
```

### Stashing Untracked Files As Well

To include untracked (read "new") files in a stash, add the `-u` option:

```bash
git stash -u
```

### Stashing Only Unstaged Changes

If you have staged a few (but not all) changes to tracked files and you want to stash the unstaged changes, use the `-k` option.

```bash
git stash -k
```

### Interactive Stashing

Similar to `git add -p`, you can interactively stash changes with:

```bash
git stash -p
```

### The Stash List

When you perform a `git stash`, a stash entry will be added to your list of saved stashes. This list is kept as a LIFO "stack". 



### "Unstashing"

After stashing, you will ultimately want to recall the changes you stashed. To do this, run:

```bash
git stash pop
```


## Merge Conflicts During Rebase





