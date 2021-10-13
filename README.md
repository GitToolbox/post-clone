<p align="center">
    <a href="https://github.com/GitToolbox/">
        <img src="https://cdn.wolfsoftware.com/assets/images/github/organisations/gittoolbox/black-and-white-circle-256.png" alt="GitToolbox logo" />
    </a>
    <br />
    <a href="https://github.com/GitToolbox/post-clone/actions/workflows/pipeline.yml">
        <img src="https://img.shields.io/github/workflow/status/GitToolbox/post-clone/pipeline/master?style=for-the-badge" alt="Github Build Status">
    </a>
    <a href="https://github.com/GitToolbox/post-clone/releases/latest">
        <img src="https://img.shields.io/github/v/release/GitToolbox/post-clone?color=blue&label=Latest%20Release&style=for-the-badge" alt="Release">
    </a>
    <a href="https://github.com/GitToolbox/post-clone/releases/latest">
        <img src="https://img.shields.io/github/commits-since/GitToolbox/post-clone/latest.svg?color=blue&style=for-the-badge" alt="Commits since release">
    </a>
    <br />
    <a href=".github/CODE_OF_CONDUCT.md">
        <img src="https://img.shields.io/badge/Code%20of%20Conduct-blue?style=for-the-badge" />
    </a>
    <a href=".github/CONTRIBUTING.md">
        <img src="https://img.shields.io/badge/Contributing-blue?style=for-the-badge" />
    </a>
    <a href=".github/SECURITY.md">
        <img src="https://img.shields.io/badge/Report%20Security%20Concern-blue?style=for-the-badge" />
    </a>
    <a href="https://github.com/GitToolbox/post-clone/issues">
        <img src="https://img.shields.io/badge/Get%20Support-blue?style=for-the-badge" />
    </a>
    <br />
    <a href="https://wolfsoftware.com/">
        <img src="https://img.shields.io/badge/Created%20by%20Wolf%20Software-blue?style=for-the-badge" />
    </a>
</p>

## Overview

Add a bash override for git (.bash_profile, .bashrc or .bash_aliases)

```shell
function git()
{
    command git-wrapper "$@"
}
```

The cloner
```shell
#!/usr/bin/env bash

function get_git_root()
{
    local repo

    IFS=' ' read -r -a array <<< "$@"

    repo=${array[-1]}
    repo=${repo##*/}
    repo=${repo%%.*}
    realpath "${repo}"
}

function main()
{
    if [[ $1 == "clone" ]]; then
        shift

        if command git clone "$@"; then
            root=$(get_git_root "$@")
            if [[ -d "${root}" ]]; then
                command post-clone "$root"
            fi
        fi
     else
        command git "$@";
     fi
}

main "$@"
```

The post-clone
```shell
#!/usr/bin/env bash

cd $1
echo $PWD
```
