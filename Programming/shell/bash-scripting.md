# Bash scripting: day-to-day shell

The shell is the glue language of this whole KB and of OSS work — I spend more time in bash than in any editor command. These are the bits I actually use daily, and the pitfalls that burn everyone (including me).

## The basics that stick

- **Variables**: `name=value` (no spaces!), use `${name}` inside strings. Never `${name}` unquoted where a word boundary matters — always `"$name"` in arguments.
- **Conditionals**: `if [[ -f file ]]; then ...; elif [[ $x == "y" ]]; then ...; fi`. `[[ ]]` (bash) over `[ ]` — no word-splitting surprises, supports `&&`, `||`, regex `=~`, globs.
- **Loops**: `for i in *.md; do ...; done`, `for i in $(seq 1 10); do ...; done`, `while read -r line; do ...; done < file`.
- **Functions**: `myfn() { echo "$1"; }` — local vars with `local`, args via `$1`/`$2`/`$@`.

## Pipelines, exit codes, command substitution

- **Exit codes**: `0` = success, non-zero = failure. `$?` holds the last command's code. `&&`/`||` chain on it. The pipeline's exit code is the *last* command's — `set -o pipefail` fixes that (the real "why didn't my script stop" bug).
- **Command substitution**: `$(cmd)` captures stdout into a string — `files=$(ls *.txt)`. **Word-splitting gotcha**: unquoted `$files` splits on whitespace; quoted `"$files"` keeps newlines. Use `mapfile`/`readarray` for line lists.
- **Process substitution**: `diff <(cmd1) <(cmd2)` — treat a command as a file. Indispensable for comparing tool outputs.

## set -euo pipefail — the safety header

Put this on every script:

```
set -euo pipefail
```

- `-e`: exit on any failing command. `-u`: error on unset variables (catches typos). `-o pipefail`: a pipeline fails if any stage fails.
- Caveat: `-e` won't trigger in conditionals, `&&`/`||` chains, or `$(...)` on the LHS — so it's a safety net, not a guarantee. `set -x` (trace) is your debugging friend.

## Quoting pitfalls (the classics)

- `rm $files` with a filename containing spaces = multiple args. Always quote.
- Empty vs unset vs null: `"${var:-default}"` for default-on-unset, `"${var:=default}"` to assign it. `[[ -z $var ]]` for empty.
- Globs don't expand inside quotes; unquoted `*` can explode into "no match" (nullglob) or literal `*`.
- `find ... -exec` vs xargs: `find` + `-print0` + `xargs -0` is the only space-safe combo; plain `xargs` breaks on spaces.

## The common tools I reach for

- **jq** — the JSON Swiss army knife: `jq '.key' file.json`, `jq -r` for raw strings, `. | select(.x > 3)`, `--arg` to inject variables. Never `grep` JSON.
- **rg** (ripgrep) — `rg pattern dir` is fast and respects `.gitignore`; `rg -l` lists files, `rg -C 3` context. My default over `grep`.
- **find** — `find . -name '*.cpp' -not -path '*/build/*'`. For *content* search use rg; find is for name/path filtering.
- **xargs** — parallelize: `find ... -print0 | xargs -0 -P 8 cmd`. Know `-n1` (one arg per call) and `-I{}`.
- **awk/sed one-liners**: `awk '{print $2}'`, `awk -F, '{s+=$3} END{print s}'` (column math), `sed -i 's/foo/bar/g'` (edit in place). Both are line-based; awk for fields, sed for substitutions.

## Aliases vs scripts

- **Aliases**: interactive shortcuts only — not expanded in scripts, no arguments, no logic. `alias ll='ls -la'`.
- **Scripts**: when it takes args, does logic, or loops — put it in a file with `#!/usr/bin/env bash` and `set -euo pipefail`. The rule: if I'd reach for `$1`, it's a script.

## Related

- [version-control](version-control.md) — the git side of daily shell work.
- [PLAN](../) — the Programming area this belongs to.

## Why it matters

Every task in this KB (build commands, CI debugging, `find`/`rg` across a huge repo, driving pytorch CI) starts in bash. The difference between "script that half-works for me" and "script that works" is almost entirely exit codes and quoting.
