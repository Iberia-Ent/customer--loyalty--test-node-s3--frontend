## Overview

Find the details in order to contribute to the project.

## Getting started

This is mainly a documentary repository. Just use a Mardown friendly reader, or text editor.

## GitHub flow

We use GitHub flow as a strategy to manage branches.

`GitHub flow:` is a lightweight, branch-based workflow that supports teams and projects where deployments are made regularly. See more details about how to use [github workflow](https://github.com/Iberia-Ent/software-engineering--github-flow--template/blob/main/README.md).

This strategy is based on the following branches:

- main: is the main branch where the source code of HEAD always reflects a production-ready state.


## Conventional commits

The Conventional Commits specification is a lightweight convention on top of commit messages. It provides an easy set of rules for creating an explicit commit history; which makes it easier to write automated tools on top of. This convention dovetails with SemVer, by describing the features, fixes, and breaking changes made in commit messages.

The commit message should be structured as follows:

```
<type>[optional scope]: <description>

[optional body]

[optional footer(s)]
```

Where `<type>` is:

- `feat` - New functionality added
- `fix` - Broken functionality fixed
- `perf` - Performance improved
- `docs` - Documentation added/removed/improved/...
- `chore` - Package setup, CI setup, ...
- `refactor` - Changes in code, but no changes in behavior
- `test` - Tests added/removed/improved/...

In the rare cases when your changes break backwards compatibility, the message must include string `BREAKING CHANGE:`. See more detail about how to use [conventional commits](https://www.conventionalcommits.org/en/v1.0.0/).