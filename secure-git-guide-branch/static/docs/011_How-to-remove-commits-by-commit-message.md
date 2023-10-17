# Git - How to remove commits by their commit message

![HEADER IMAGE](./media/HEADER/GitHub-Repo-SecureGitGuide-ART-011.jpg)

Recently we had to remove a large number of commits from our repository. We have implemented a job queue as a GitHub Action. The queue is implemented using empty Git commits to store the jobs information. So you do not need external services. We call it [Git-Queue](https://github.com/Nautilus-Cyberneering/git-queue).

Since we were still using a beta version we decided to make some changes that broke compatibility with the old version.
But we already had two demo repositories using the old version. In order to upgrade to the new version we have to remove thousands of commits related to the queue.

Here you can read the [original issue](https://github.com/Nautilus-Cyberneering/library-consumer/issues/28).

All the queue commits have a prefix, so the Git history looks like:

```s
* 0830b3c2 - 📝✅: library-update: job.id.1 job.ref.264657b51fbfcf63a0267fa425fd121f5f6781a0 (2022-06-13 10:43:37 +0000) <NautilusCyberneering[bot]>
* 155125d8 - library aaa synced to commit 13db6c9f7c2f9ca7d8ae57b80cbbcc97a51a28a8 (2022-06-13 10:43:36 +0000) <A committer>
* af02b696 - update library aaa to commit 13db6c9f7c2f9ca7d8ae57b80cbbcc97a51a28a8 (2022-06-13 10:43:33 +0000) <A committer>
* 2b110030 - 📝👔: library-update: job.id.1 job.ref.264657b51fbfcf63a0267fa425fd121f5f6781a0 (2022-06-13 10:43:33 +0000) <NautilusCyberneering[bot]>
* 264657b5 - 📝🈺: library-update: job.id.1 (2022-06-13 11:43:00 +0100) <NautilusCyberneering[bot]>
```

We wanted to remove all commits with the prefix `📝` and also a different prefix we were using before that.

After the first research we found a Stackoverflow question:

[Using git filter-branch to remove commits by their commit message](https://stackoverflow.com/questions/4558162/using-git-filter-branch-to-remove-commits-by-their-commit-message/9543606#9543606)

There are a couple of solutions but both of them required to write a little bit of shell script.

One of them uses `git filter-branch` but it seems that solution squashes the commits. That should not be a problem is our case because all the commits we wanted to delete are supposed to be empty. And we do not want to delete the commit changes.

The other solution uses `git rebase` an a custom shell script to filter commits and mark them to "drop".

Although those solutions were perfectly fine we decided to use [reposurgeon](http://www.catb.org/~esr/reposurgeon/) which is a more powerful solution.

## Install reposurgeon

If you are using Ubuntu I should be only:

```s
sudo apt-get install reposurgeon
```

Reposurgeon has two modes: interactive and non-interactive. If you execute reposurgeon without any argument you could see something like:

```s
$ reposurgeon
reposurgeon% help
6. The Command Interpreter                 
  1. Command syntax                        syntax*
  2. Finding your way around               help, history, shell, quit
  3. Regular Expressions                   regexp*
  4. Selection syntax                      selection*, functions*
  5. Redirection and shell-like features   redirection*
7. Import and Export                       
  1. Reading and writing repositories      read, write
  2. Repository type preference            prefer, sourcetype
  3. Rebuilds in place                     rebuild
  5. File preservation                     preserve, unpreserve
  6. Incorporating release tarballs        incorporate
  7. The repository list                   choose, drop, rename
8. Information and reports                 
  1. Reports on the DAG                    list, index, names, stamp, tags, inspect, graph, lint, when
  2. Statistics                            stats, count, sizes
  3. Examining tree states                 manifest, checkout, diff
9. Surgical Operations                     
  1. Commit deletion                       squash, delete
  2. Commit mutation                       merge, unmerge, reparent, split, add, remove, tagify, reorder
  3. Branches                              branch, branchlift, debranch
  4. Tags, resets, and blobs               tag, reset, blob, dedup
  5. Repository splitting and merging      divide, expunge, unite, graft
  6. Metadata editing                      msgout, msgin, setfield, attribution, append, gitify, filter
  7. Path reports and modifications        path, setperm
  8. Timequakes and time offsets           timequake, timeoffset
  9. Miscellanea                           renumber, transcode
10. Artifact handling                      
  1. Attributions                          authors
  2. Ignore patterns                       ignores
  3. Reference lifting                     references, legacy
  4. Changelogs                            changelogs
  5. Clique coalescence                    coalesce
11. Control Options                        options*, set, clear
12. Scripting and debugging support        
  1. Variables, macros, and scripts        assign, unassign, define, do, undefine, script, print
  2. Housekeeping                          gc
  3. Diagnostics                           log, logfile
  4. Debugging                             resolve, version, hash, sizeof, strip
  5. Profiling                             elapsed, timing, readlimit, memory, profile, exit
Starred topics are not commands.           
reposurgeon%  
```

You can also create your own script and execute is later. The simplest think you can do is:

```s
reposurgeon "read ." lint
```s

That command will execute to reposurgeon commands:

1. `read .`: it reads the current folder into memory. The current folder must be a repository.
2. `lint`: it checks the repository for errors.

You can also enter is the interactive mode and execute both commands manually.

Reposurgeon is a very big tool with a lot of options. You should read the basic documentation to understand how it works. Basically it imports any kind or repo and creates an internal representation of the repo.

The you can execute some commands to change that representation and finally you can export again the internal representation into a different repo. One of the common tasks is used for is converting from different repositories formats. For example from [SNV](https://subversion.apache.org/) to [Git](https://git-scm.com/).

We are going to explain only a use case here: how to remove commits that start with a given prefix.

## Removing commits that start with a given prefix

Let's first create an empty repo:

```s
mkdir /tmp/remove-commits-example
cd /tmp/remove-commits-example
git init
```

Now we can add some commits. In order to simplify the example the commits we want to delete start with the prefix `drop`.

```s
echo "hello world!" > README.md
git add .
git commit -m "add README"
git commit --allow-empty -m "drop: empty commit"
```

After executing those command we will have two commits:

```s
* 9f606d6 - (HEAD -> main) drop: empty commit (2022-06-13 15:08:01 +0100) <Jose Celano>
* 7533b82 - add README (2022-06-13 15:06:27 +0100) <Jose Celano>
```

Now we want to remove the commit starting with `drop`.

We can create a new file called: `remove-commits.rs` with this content:

```bash
# Load the project into main memory
read /tmp/remove-commits-example

# Commit deletion
/drop/c delete

# We want to write a Git repository
prefer git

# Do it
rebuild /tmp/new-remove-commits-example
```

Then you can run the script with:

```s
reposurgeon "script remove-commits.rs"
```

All the lines are self-explaining except maybe for the commit deletion one: `/drop/c delete`.

The deletion command format is: `{SELECTION} delete` where [SELECTION](http://www.catb.org/~esr/reposurgeon/repository-editing.html#selections) defines what you want to delete. That is very common for reposurgeon commands. The selection argument allows you to define which internal objects you want to act on. There are different types of selections. One of them  it a "text search" which is a regular expression.

>A text search normally matches against the comment fields of commits and annotated tags, or against their author/committer names, or against the names of tags; also the text of passthrough objects.

In our case the selection `/drop/` means that we want to search for all objects containing the word `drop`.

Since we allow want to delete commits, we can add what reposurgeon calls a "qualifier letter". The final command contains a `c` character after the regular expression: `/drop/c`. That changes the the scope of the search to only the comment text of commit or tag.

After executing our script you can go to the newly generated repo and execute a `git log` command. You will see the commit starting with `drop` that was removed:

```s
cd new-remove-commits-example/
* 3d7da50 - (HEAD -> main) add README (2022-06-13 15:06:27 +0100) <Jose Celano>
```

You can also notice that hte commit hash has for the remaining commit changed from `7533b82` to `3d7da50`. That means you have to force push the new repo version to the remote repo.

Another side effect you might have is losing the commit signature.

## Links

- [reposurgeon](http://www.catb.org/~esr/reposurgeon/)
- [reposurgeon - commit deletion](http://www.catb.org/~esr/reposurgeon/repository-editing.html#deletion)

[Back to home](./index.md)
