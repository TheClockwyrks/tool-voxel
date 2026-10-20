# history

The record of how this repository's history was extracted from the monorepo,
[The Test Cabinet](https://github.com/TheClockwyrks/TheTestCabinet), where the
crates here lived until the extraction. Nothing builds or tests against these
files; they are kept so the provenance outlives the scratch clone the
extraction ran in.

## The extraction

The history below the scaffold commit is the monorepo's `issue/repos-4` at
`25923d22c5ec8eabf96f1b97f21a546c1ac3db25`, the branch whose commit
`77c83390e` moved the crates' model-core dependency onto the tool-model-core
repository, filtered to the paths in [`paths.txt`](paths.txt) by git
filter-repo 2.47.0:

```sh
git clone -q --no-local --single-branch --branch issue/repos-4 --no-tags \
  <the-test-cabinet> x
cd x
uvx --from git-filter-repo==2.47.0 git-filter-repo --force \
  --paths-from-file ../paths.txt --prune-empty always --preserve-commit-hashes
```

Of the 6,976 commits reachable from that commit, 23 touched a path in the list
and were kept (2 of them merges); the rest were pruned. No tag was carried. The
tip tree of every moved directory hashes the same as the monorepo's at that
commit:

```text
crates/voxel/       3ae6204811724c93d1e444e402899d115af580f0
crates/voxel-anim/  33216a54fdb9eda2c12d4b97d175618a33eae5d5
```

The paths are kept where they were in the monorepo, so the filter needed no
rename and the path dependency of `voxel-anim` on `voxel` works as it did.

## The files

- [`paths.txt`](paths.txt) is the list handed to `--paths-from-file`
  (sha256 `6c311ef4d970face133a30a6e8bba9fb64e9dce065727cff80980b279c7e958c`).
  It is generated, not hand-written: the two moved directories
  (`crates/voxel/`, `crates/voxel-anim/`), and every path a tracked file under
  them had before a rename or copy, collected by
  `git log --follow -M -C --name-status` over each of the 12 files. The one
  rename found, `crates/voxel/src/bin/voxel-anim.rs` to
  `crates/voxel-anim/src/main.rs`, stays inside the two directories, so the
  list is those two directories.
- [`commit-map`](commit-map) is filter-repo's map from each monorepo commit (left)
  to the commit it became here (right), all zeros for a pruned one.
  `--preserve-commit-hashes` left the commit ids quoted in the messages as they
  were, so an id in a message below the scaffold commit names a monorepo commit,
  and this map translates it.

## The live-path review

The list names no path outside the two moved directories, so no live file of
the monorepo came along and the scaffold commit deletes nothing. The files the
voxel crate held before the model core was split out of it
(`crates/voxel/src/color.rs`, `rig.rs`, `raster.rs` and `raster.test.rs`) are
in this history up to the commit that removed them, `3ff51fa2c`, and are absent
from its tip.
