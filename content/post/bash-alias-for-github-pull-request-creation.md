{
  "title": "A bash alias for GitHub Pull Request creation",
  "description": "A bash alias for GitHub Pull Request creation.",
  "date": "2015-02-19",
  "url": "bash-alias-for-github-pull-request-creation/",
  "type": "post",
  "tags": ["git","bash"]
}

I am fond of [feature branch](https://www.atlassian.com/git/tutorials/comparing-workflows/feature-branch-workflow) git workflows. If the team prefers additional structure, [gitflow](https://github.com/nvie/gitflow) is also a great tool. In my current position, the bulk of my work takes place in repositories with few maintainers and a less-structured workflow is more comfortable.

Most of my work happens on GitHub, so the feature branch technique is mildly altered to correspond to some of the [GitHub specifics](https://guides.github.com/introduction/flow/index.html). This is almost exclusively the Pull Request; which is not a concept specific to GitHub, but creating a GitHub pull request is not something that git itself supports.

#### My usual workflow

Get current, pick an issue, make some updates, push:

````
git fetch -p
git checkout master
git checkout -B 19_some-issue-19-desc
# changes
git commit -am "Make some updates, closes #19."
git push origin 19_some-issue-19-desc
````

Hit [GitHub](http://github.com), find my branch, and create a Pull Request.

This is by no means painful, but it always left a sour taste in my mouth to be obligated to use the web UI for generating my pull requests.

#### My new workflow

Replace all of the browser usage with `pr`. After some one-time set up on your machine (setting an environment variable with an auth token and adding a bash alias function), `pr` will accomplish the same thing (with some constraints).

#### `pr`

Basic instructions are in the comments. It is Also available as a [gist](https://gist.github.com/imperialwicket/16ca80d37ef98da15d3b).

`pr` always considers a Pull Request as targetting master (variable is in the function if you are not using 'master'), and always assumes the Pull Request is from the current branch. `pr` asks for the issue number in question, and will try to pull it from the branch name as a default.

````
# Bash alias to generate pull requests on github.com 
#
# PreRequisites:
#   Create an auth token (bypasses 2 factor auth, when enabled):
#     https://github.com/settings/tokens/new
#
#     Use any name, something like 'pull requests' makes sense.
#     Allow permissions 'repo' and 'public repo'.
#     Create.
#
#   Store the auth token:
#     echo "export GITHUB_PR_TOKEN=<token>" >> ~/.bashrc
#
#
# Use:
#   `pr`
#
#   pr will attempt a regex capture on the branch name to determine a
#   target issue for the pull request. You can override with a `read`
#   prompt. 
#
#   The 'head' branch is your current branch.
#   
#   The 'base' branch is 'master'.
#
#
# Known issues:
#   - Titled and bodied pull requests are not supported.
#   - Issue candidate is not confirmed (weird results if you create a PR for
#     closed issues).
#   - Branch is not confirmed on origin, missing 'head' branch on origin
#     causes the PR to fail.
#
#
function pr {
    gh_root="https://api.github.com"
    gh_base="master"
    [ -z "$GITHUB_PR_TOKEN" ] && echo 'pr requires the $GITHUB_PR_TOKEN variable.' && return
    #[ -z "$1" ] && echo "pr requires an issue number." && return
    repoTest=$(git config --list | grep 'remote\.origin\.url' | grep 'github\.com' | awk -F ":" '{print $2}')
    [ -z "$repoTest" ] && echo "This doesn't seem like a github repository." && return
 
    cur=$(git rev-parse --abbrev-ref HEAD)
    userRepo=${repoTest/.git/}
    user=${userRepo%/*}
    repo=${userRepo#*/}
 
    # Try to retrieve the issue from the current branch name. Captures any digits.
    issueRegex='([a-zA-Z_/-]+)?([0-9]+)([a-zA-Z_/-]+)?'
    [[ $cur =~ $issueRegex ]] && issue="${BASH_REMATCH[2]}"
    echo -n "Provide an issue for $cur or press Enter to use [$issue]: "
    read userIssue
 
    #TODO validate that the issue is valid ?
    #TODO validate that the branch is pushed to origin ?
 
    postdata="{\"issue\":\"${userIssue:-$issue}\",\"head\":\"$cur\",\"base\":\"$gh_base\"}"
 
    #TODO trap/redirect the curl output
    curl -u $GITHUB_PR_TOKEN:x-oauth-basic -d $postdata "$gh_root/repos/$user/$repo/pulls"
}
````

#### Notes

 1. The regex and variable replacement are not comprehensive, but they work for me and should work for a number of use cases.
 1. Bash is not the ideal language for a tool like this (text parsing and JSON are not really Bash/Shell specialties). But...
 1. If you are using git, it is reasonable to expect that a bash alias will work in your environment  with no issues.
 1. It would be nice to support title/body PRs, but I do not need it.

