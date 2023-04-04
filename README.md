# Semantic-release running with squashed commits

Original [semantic-release](https://github.com/semantic-release/semantic-release/discussions) is a very powerful tool to operate semantic release. It automates the whole package release workflow including: determining the next version number, generating the release notes, and publishing the package.
This fork is largely based on the original project.

As we are using squash-and-merge strategy to keep a clean and lean history, we have to develop a tweak to read the message of squashed commits.
Our strategy is as follows :

- Creating a new branch to operate change. The branch name will be the message of the squashed commits.
- Do some commits on this branch (named inner commit) with valid conventionnal commit message.
- Create a pull request (PR) with a valid name like: `Issue to solve (#3)` where the number `3` is the number of the PR.
- Squash-and-merge the PR with the valid name.

## How it works

The [GitHub API](https://octokit.github.io/rest.js/v19#pulls-list-commits) is used to retrieve the inner commit message and to proceed the versioning on.

The table below shows which commit message gets you which release type when `semantic-release-squash` runs (using our configuration):

| Commit message                                                       | Release type |
| -------------------------------------------------------------------- | ------------ |
| `fix(pencil): stop graphite breaking when too much pressure applied` | Patch        |
| `feat(pencil): add 'graphiteWidth' option`                           | Minor        |
| `break(pencil): remove graphiteWidth option`                         | Major        |

### View of a squashed PR with inner commits

![Alt text](https://user-images.githubusercontent.com/15166875/229083489-82a73e59-7f64-468a-88f7-8714d0630e37.png "squashed commit")

## What has been modified

Some changes have been done in order to analyze inner commit's message. Here are the files that have been modified :

- Create the new file `lib/github.js` to introduce `getInnerSquashedCommits` and `getPullRequestNumber`.
- Modify the `lib/get-commit.js` to introduce an option in order to use `getInnerSquashedCommits` on demand.
- Modify the `cli.js` to introduce the option with the flag `-s` or `--squashMerge`.
- Create the new file `test/github.test.js` to proceed the test on the new part.
- Create the new file `test/helpers/github-utils.js` to introduce `cloneRemoteSquashMergeRepo`.
- update the `package.json` file.

## How to use

The analyze of inner commit's message is done by passing the option `-s` or `--squashMerge` to `semantic-release-squash` :

for example `./semantic-release-squash --squashMerge --debug`

The message of the squashed commits must be formed as :

`Issue to solve (#3)` with `3` the number of the PR.

And inner commit's message must be formed as :

`feat(scope): inner commit's message` or
`feat: inner commit's message`
To learn more about commit's message, the contributo's bible is here to help you.

If the message is malformed, even on the squashed or inner commit, the tool will ignored them.

Feel free to fork this repository and to submit issue.
