<h1 align="center">
    <a href="https://github.com/WolfSoftware">
        <img src="https://raw.githubusercontent.com/WolfSoftware/branding/master/images/general/banners/64/black-and-white.png" alt="Wolf Software Logo" />
    </a>
    <br />
    Post Clone Git Hook
</h1>

<p align="center">
    <a href="https://github.com/GitToolbox/post-clone/actions/workflows/pipeline.yml">
        <img src="https://img.shields.io/github/workflow/status/GitToolbox/post-clone/pipeline/master?logo=github&logoColor=white&style=for-the-badge" alt="Github Build Status">
    </a>
    <a href="https://github.com/GitToolbox/post-clone/releases/latest">
        <img src="https://img.shields.io/github/v/release/GitToolbox/post-clone?color=blue&style=for-the-badge&logo=github&logoColor=white&label=Latest%20Release" alt="Release">
    </a>
    <a href="https://github.com/GitToolbox/post-clone/releases/latest">
        <img src="https://img.shields.io/github/commits-since/GitToolbox/post-clone/latest.svg?color=blue&style=for-the-badge&logo=github&logoColor=white" alt="Commits since release">
    </a>
    <br />
    <a href=".github/CODE_OF_CONDUCT.md">
        <img src="https://img.shields.io/badge/Code%20of%20Conduct-blue?style=for-the-badge&logo=read-the-docs&logoColor=white" />
    </a>
    <a href=".github/CONTRIBUTING.md">
        <img src="https://img.shields.io/badge/Contributing-blue?style=for-the-badge&logo=read-the-docs&logoColor=white" />
    </a>
    <a href=".github/SECURITY.md">
        <img src="https://img.shields.io/badge/Report%20Security%20Concern-blue?style=for-the-badge&logo=read-the-docs&logoColor=white" />
    </a>
    <a href="https://github.com/GitToolbox/post-clone/issues">
        <img src="https://img.shields.io/badge/Get%20Support-blue?style=for-the-badge&logo=read-the-docs&logoColor=white" />
    </a>
    <br />
    <a href="https://github.com/TGWolf">
        <img src="https://img.shields.io/badge/Created%20by%20Wolf-black?style=for-the-badge" />
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

    if [[ ${#array[@]} == 1 ]]; then
        repo=${array[0]}
        repo=${repo##*/}
        repo=${repo%%.*}
    else
        repo=${array[1]}
    fi
    realpath "${repo}"
    echo "${repo}"
}

function main()
{
   if [[ $1 == "clone" ]]; then
      shift
      command git clone "$@"

      root=$(get_git_root "$@")
      command "post-checkout" "${root}"
   else
      command git "$@";
   fi;
}

main "$@"
```

The post-clone
```shell
#!/usr/bin/env bash

cd $1
echo $PWD
```

## Show Support

<p>
	<a href="https://ko-fi.com/wolfsoftware">
		<img src="https://img.shields.io/badge/Ko%20Fi-blue?style=for-the-badge&logo=ko-fi&logoColor=white" />
	</a>
</p>
