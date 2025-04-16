# Zcash Dev Suite
> Unified development hub for Zcash ecosystem development

## Overview

The intention of this project is to create an environment where every commit reflects the full state of all components.
To this effect, we use a `git subtree` to merge the upstream repositories of each component into a single repository.

Since the subtree workflow differs from the standard git workflow, we have created a tool, `subtree`, to help manage the repository.

### Q: Why not submodules?

While submodules are a valid way to manage dependencies, they have some drawbacks:

- Require extra commands like `git submodule update --init`
- Split commit history across multiple repos
- Cause friction for contributors and CI systems
- Don’t behave like a true monorepo for testing, versioning, or tagging

Subtrees avoid all of this by embedding the full upstream code directly. The result is monorepo-style simplicity without losing upstream independence.

### Q: Why not a monorepo?

While Shielded Labs prefers the ergonomics of a monorepo, we recognize the importance of respecting upstream workflows. The subtree approach gives us monorepo-like coherence without requiring upstream projects to change their structure.

## Requirements

- Git
- Intermediate knowledge of git for surgical operations
- GitHub CLI (for creating pull requests from patch files)
- Whatever each component requires in terms of c headers, libraries, etc. An exhaustive list of these dependencies is not provided here.

## Usage

Use the `subtree` tool to manage the repository. The tool is a wrapper around `git subtree` that has two main responsibilities:
- mapping familiar git commands to the subtree workflow.
- maintaining the relationship between subtree prefixes (local directories) and git remotes

Incidentally, it also serves as a working reference to the subtree workflow itself.

### Just make commits.

Treat this repository like one big monorepo. Just make commits to the root. YOLO. `subtree split` and `subtree pr` will do the work of isolating those commits out based on prefix. Just go nuts.

```
$ ./subtree
Commands:
  add <prefix> <remote-url> <branch>  Add a new subtree and track remote
  ls                                  List subtree-related commits
  pull <prefix> <branch>              Pull updates for a subtree from its tracked remote
  diff <prefix> <branch>              Compare local subtree with remote
  branch <prefix> [branch-name]       Create a split branch for a subtree
  pr <prefix>                         Create a PR for a subtree update
  rm <prefix>                         Remove a subtree and its remote
```

### Adding a new subtree

```bash
$ ./subtree add cool_zcash_thing git@github.com:ShieldedLabs/cool_zcash_thing.git main
```

This squashes the entire history of the remote repository into a single commit and merges it into the current branch.

### Listing subtrees

```bash
$ subtree ls

Prefix               Remote URL                                              Subtree Split        Local Commit
------------------------------------------------------------------------------------------------------------------
zaino-devtool        git@github.com:ShieldedLabs/zaino-crosslink.git         b84847ab38           9c4d586d2e
zcash-devtool        git@github.com:ShieldedLabs/zcash-devtool-crosslink.git 382472c3b7           ec2ce2ca26
librustzcash         git@github.com:ShieldedLabs/librustzcash-crosslink.git  4130409eb0           46a0d729fb
zebra                git@github.com:ShieldedLabs/zebra-crosslink.git         93dc6a5bfe           a7d4b2f0ab
cool_zcash_thing     git@github.com:ShieldedLabs/cool_zcash_thing.git        1b2c3d4e5f           1234567890
```

Where:
- *prefix* is the local directory where the subtree is checked out
- *remote URL* is the remote repository URL
- *subtree split* is the remote commit hash of the subtree's origin
- *local commit* is the local commit where the subtree was merged in
```

### Pulling updates for a subtree

```bash
$ ./subtree pull cool_zcash_thing main
```

This fetches the upstream repository and merges the changes into the current subtree prefix. Roughly equivalent to `git pull --merge` for the subtree prefix.

### Comparing local subtree with remote

```bash
$ ./subtree diff cool_zcash_thing main
```

This compares the current state of the subtree directory against the last known upstream commit, based on the tracked remote and branch.

### Removing a subtree

```bash
$ ./subtree rm cool_zcash_thing
````

Deletes the subtree and the git remote.
