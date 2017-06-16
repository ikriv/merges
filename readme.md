# Making Git merge do what _you_ want

This repository demonstrates results of merging two branches, `ReleaseX` and `ReleaseY`, with different Git merge strategies. 

Most of the time git default merge strategy works just fine, but when you need something different, the waters get murky. E.g. `git merge -s ours...` is not the same as `git merge -X ours...`. There is `git merge -X theirs...`, but there is no `git merge -s theirs`. The purpose of this repository is to remove the mystery via example.

![Revision Graph](https://raw.githubusercontent.com/ikriv/merges/master/RevisionGraph.png)

`master` branch contains initial version of three files: `a.txt`, `b.txt`, and `c.txt`.

`ReleaseX` branch changes `a.txt`, `b.txt`, and adds `d.txt`.

`ReleaseY` branch changes `a.txt`, `c.txt`, and adds `e.txt`.

`Merge*` branches were created from `ReleaseX` and then merged with `ReleaseY` using different merge options.

`MergeDefault` branch: `git merge ReleaseY`.  
This creates a conflict for `a.txt`, that needs to be resolved.

`MergeOursStrategy`: `git merge -s ours ReleaseY`.  
This uses  _ours_ merge strategy, which basically ignores the contents of `ReleaseY`.  The merge commit has two parents, but its contents are identical to `ReleaseX`. This may be useful to "merge" failed bug fix branches if you don't want them to pollute the branch space and if deleting history is not allowed (`receive.denyNonFastForwards`, `receive.denyDeletes`).

`MergeOursOption` branch: `git merge -X ours ReleaseY`.  
This uses default (recursive) strategy with the "ours" _option_. It means that "our" branch (ReleaseX) is preferred to "theirs" (ReleaseY) _when a conflict occurs_. This merge keeps ReleaseX version of `a.txt`, but includes updated `c.txt` and new `e.txt` from ReleaseY. Therefore, the result is quite diferent from `MergeOursStrategy`.

`MergeTheirsOption` branch: `git merge -X theirs ReleaseY`.  
This uses default (recursive) strategy with "theirs" _option_, meaning that ReleaseY version is preferred when a conflict occurs. In our case the only conflict is `a.txt`, so this merge differs from `MergeOursOption` in that it takes `a.txt` from `ReleaseY`. It does take `b.txt` and `d.txt` from `ReleaseX`.

Unfortunately, Git does not provide `git merge -s theirs...` command, which would create a merged commit with the contents of the second parent and move current branch to that commit. This would be quite useful to simulate force pushes without losing history. Fortunately, this can be done by using `git merge -s ours` and a temporary branch, as explained in [Paul Pladijs's answer (not the accepted answer!)](https://stackoverflow.com/questions/173919/is-there-a-theirs-version-of-git-merge-s-ours) on Stack Overflow.
