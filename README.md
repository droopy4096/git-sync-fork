# git-sync-fork
Synchronize your git fork with upstream (bulk-processing every branch)

## Outline

main script is designed to pass-through CLI arguments to built-in functions for more flexibility.
barring the last 3 lines it is suitable for includes

## Usage

### Simple use (no PR branch generation):

```shell
  export SYNC_SAVED_BRANCHES=my_file
  export SYNC_UPSTREAM=upstream
  export SYNC_ORIGIN=origin
  sh -x git-sync-fork.sh save_upstream_branches
  # edit ${SAVED_BRANCHES}
  sh -x git-sync-fork.sh sync_next
  # do whatever needs to be done before next sync
  # ...
  # git branches -v
  sh -x git-sync-fork.sh sync_next
```

### More complex use:

```shell
  export SYNC_SAVED_BRANCHES=my_file
  export SYNC_PR_BRANCH_SUFFIX="-my-fix"
  sh -x git-sync-fork.sh save_upstream_branches
  # edit ${SAVED_BRANCHES}
  sh -x git-sync-fork.sh sync_next
  sh -x git-sync-fork.sh create_pr
  # do whatever needs to be done before next sync
  # ...
  # git branches -v
  # note that you have two branches checked out - original and <original>-my-fix
  sh -x git-sync-fork.sh sync_next
  sh -x git-sync-fork.sh create_pr
```

### Remove local branches that don't have corresponding remote branches:

```shell
SYNC_UPSTREAM=origin SYNC_SAVED_BRANCHES=branch_list.remote sh ~/work/git-sync-fork/git-sync-fork.sh save_upstream_branches
git branch --list | cut -c3- > branch_list.local
comm -23 <(sort branch_list.local) <(sort branch_list.remote) > branch_list.remove
for b in $(cat branch_list.remove) ; do git branch -d ${b} ; done
rm branch_list.{remote,local,remove}
```
