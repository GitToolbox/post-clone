<!-- markdownlint-disable -->
<p align="center">
    <a href="https://github.com/GitToolbox/">
        <img src="https://cdn.wolfsoftware.com/assets/images/github/organisations/gittoolbox/black-and-white-circle-256.png" alt="GitToolbox logo" />
    </a>
    <br />
    <a href="https://github.com/GitToolbox/post-clone/actions/workflows/cicd.yml">
        <img src="https://img.shields.io/github/actions/workflow/status/GitToolbox/post-clone/cicd.yml?branch=master&label=build%20status&style=for-the-badge" alt="Github Build Status" />
    </a>
    <a href="https://github.com/GitToolbox/post-clone/blob/master/LICENSE.md">
        <img src="https://img.shields.io/github/license/GitToolbox/post-clone?color=blue&label=License&style=for-the-badge" alt="License">
    </a>
    <a href="https://github.com/GitToolbox/post-clone">
        <img src="https://img.shields.io/github/created-at/GitToolbox/post-clone?color=blue&label=Created&style=for-the-badge" alt="Created">
    </a>
    <br />
    <a href="https://github.com/GitToolbox/post-clone/releases/latest">
        <img src="https://img.shields.io/github/v/release/GitToolbox/post-clone?color=blue&label=Latest%20Release&style=for-the-badge" alt="Release">
    </a>
    <a href="https://github.com/GitToolbox/post-clone/releases/latest">
        <img src="https://img.shields.io/github/release-date/GitToolbox/post-clone?color=blue&label=Released&style=for-the-badge" alt="Released">
    </a>
    <a href="https://github.com/GitToolbox/post-clone/releases/latest">
        <img src="https://img.shields.io/github/commits-since/GitToolbox/post-clone/latest.svg?color=blue&style=for-the-badge" alt="Commits since release">
    </a>
    <br />
    <a href="https://github.com/GitToolbox/post-clone/blob/master/.github/CODE_OF_CONDUCT.md">
        <img src="https://img.shields.io/badge/Code%20of%20Conduct-blue?style=for-the-badge" />
    </a>
    <a href="https://github.com/GitToolbox/post-clone/blob/master/.github/CONTRIBUTING.md">
        <img src="https://img.shields.io/badge/Contributing-blue?style=for-the-badge" />
    </a>
    <a href="https://github.com/GitToolbox/post-clone/blob/master/.github/SECURITY.md">
        <img src="https://img.shields.io/badge/Report%20Security%20Concern-blue?style=for-the-badge" />
    </a>
    <a href="https://github.com/GitToolbox/post-clone/issues">
        <img src="https://img.shields.io/badge/Get%20Support-blue?style=for-the-badge" />
    </a>
</p>

# WARNING

Implementing this is highly dangerous unless you fully control the repository and its contents. Proceed with caution.

## Purpose

1. Feasibility Test: Assess the practicality of implementing a post-clone action in Git repositories.
2. Tool Automation: Facilitate the automatic addition of tools (like git hooks) to repositories, enhancing our existing [setup-hooks](https://github.com/GitHooksToolbox/setup-hooks) script.

## Implementation

### Overload Git Command

Modify your shell profile to replace the native git command with a custom wrapper. This can be achieved by adding the following
to your .bash_profile, .bashrc or .bash_aliases.

```shell
function git()
{
    command git-wrapper "$@"
}
```

### Git Wrapper Script

Create a git-wrapper script that manages the cloning process and triggers a post-clone action. This scripts needs to be able to
run from anywhere, we recommend placing it in your ~/bin directory or any other directory that is part of your $PATH.

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

### Post-clone Script

Create a post-clone script which is executed by the git wrapper. This script also needs to be able to run from anywhere, we recommend
placing it in your ~/bin directory or any other directory that is part of your $PATH.

```shell
#!/usr/bin/env bash

cd $1
echo $PWD
```

> In this example we simply have the script echoing the current directory to show that it is indeed executing inside the newly cloned repository.

### Real World Example

Expand the post-clone script to execute additional scripts.

```shell
#!/usr/bin/env bash

cd $1
bash repo-post-clone.sh
```

> This is a very overly-simplified example!

### Security Measures

To prevent unauthorized modifications:

1. Repository Whitelist: Ensure the repository is in a list of approved repositories.
2. Ownership Verification: Extend checks to verify the repository's owning organization.

#### Example with Security Checks

```shell
#!/usr/bin/env bash

safe_repos=("GitToolbox/post-clone")

# Function to check if a value is in an array
function not_in()
{
  local array="$1[@]"
  local seeking="$2"
  local in=0

  for element in "${!array}"; do
      if [[ "$element" == "$seeking" ]]; then
        in=1
        break
      fi
    done
  return $in
}

cd $1

# Get the remote URL of the repository
remote_url=$(git config --get remote.origin.url)

# Check if remote_url is empty
if [ -z "$remote_url" ]; then
    echo "This directory is not a Git repository or has no remote origin set. Aborting!"
    exit 1
fi

# Extract the org and repo name from the URL
# Support different URL formats (HTTPS and SSH)

if [[ $remote_url =~ ^https:// ]]; then
    # HTTPS URL format: https://github.com/org/repo.git
    org_name=$(echo "$remote_url" | awk -F'/' '{print $(NF-1)}')
    repo_name=$(echo "$remote_url" | awk -F'/' '{print $NF}' | sed 's/.git$//')
elif [[ $remote_url =~ ^git@ ]]; then
    # SSH URL format: git@github.com:org/repo.git
    org_name=$(echo "$remote_url" | awk -F'[/:]' '{print $(NF-1)}')
    repo_name=$(echo "$remote_url" | awk -F'[/:]' '{print $NF}' | sed 's/.git$//')
else
    echo "Unsupported URL format. Aborting!"
    exit 1
fi

if not_in safe_repos "$org_name/$repo_name"; then
    echo "This is not from an a safe source - Aborting!"
    exit 1
fi

echo "Executing post-clone.sh in ${org_name}/${repo_name}"
bash repo-post-clone.sh
```

### Conclusion

Use these scripts with caution and implement security checks to avoid potential misuse. Ensure thorough testing in a controlled environment.

<br />
<p align="right"><a href="https://wolfsoftware.com/"><img src="https://img.shields.io/badge/Created%20by%20Wolf%20on%20behalf%20of%20Wolf%20Software-blue?style=for-the-badge" /></a></p>
