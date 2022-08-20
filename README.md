Hello there!

I've moved to https://github.com/jan-matejka.

```
#!/usr/bin/env zsh

# SYNOPSIS
#
#   zsh $0 main <path>
#
# DESCRIPTION
#   Search <path> recursively for .git directories and print commands to rename all remotes
#   matching @github.com[:/]yaccz/ to @github.com[:/]jan-matejka.
#
# ENVIRONMENT
#   DRYRUN
#     Defaults to ``true``.
#     If ``false`` or evaluates as a shell command to ``false``, the commands to rename remotes will
#     be executed instead of printed.

SELF="${0##*/}"

die() {
  msg=$1
  shift
  printf >&2 -- "%s: $msg\n" $SELF $@
  exit 1
}

set -e
${XTRACE:-false} && set -x
: ${DRYRUN:=true}

cmd=$1
shift

case $cmd in
main)
find ${1:?} -type d -name .git | \
  xargs -r -n1 zsh $0 for-each-remote

;; for-each-remote)

git --git-dir=${1:?} remote -v \
  | awk '/@github.com[:/]yaccz/ { print $1 " " $2; }' \
  | uniq \
  | xargs -r -n2 zsh $0 rename ${1:?}

;; rename)

git_dir="$1"
r_name="$2"
r_url="$3"

new=$(echo $r_url | sed 's#yaccz#jan-matejka#')
$DRYRUN && cmd=(echo git) || cmd=git
$cmd --git-dir=${1:?} remote set-url $r_name $new

;; *)
  die "unknown command %s" $cmd

;; esac
```
<!---
yaccz/yaccz is a ✨ special ✨ repository because its `README.md` (this file) appears on your GitHub profile.
You can click the Preview link to take a look at your changes.
--->
