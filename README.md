# Alpha

The purpose of creating project are exercises with [Angular tutorial](https://angular.io/tutorial).

This project was generated with
[Angular CLI](https://github.com/angular/angular-cli) version 1.6.6.

# Building development environment

Supported OS is Linux Ubuntu 16.04 LTS.

Supported code editor is Microsoft [Visual Studio Code](https://code.visualstudio.com/).
There are settings for this editor in directory `.vscode`,
including recommended extensions.

Recommended browser is latest Chrome.

To run application `node` and `npm` must be available
on your system. `node` should be version 6 LTS, npm should be version 5.3
or higher. I do not recommend to use system wide installed `node` and `npm`
on Ubuntu. You will have a lot of troubles with file permissions, due to use
of sudo, and you will not be able to switch between different versions of `node`.

On Ubuntu and OSX, the easiest way to install `node` is to use the
[Node Version Manager](https://github.com/creationix/nvm). To install NVM for
Linux/OSX, simply copy paste the following in a terminal:
```
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.30.2/install.sh | bash
```

Install node (we will use version 6, for now)
```
nvm install 6
```

`node` comes with npm. Check versions of node and npm:
```
node -v;
npm -v;
```

If `npm` version is below 5.3, run:
```
npm install npm@latest -g
```

Install Angular CLI globally
```
npm install -g @angular/cli
```

Clone this repository

`cd` to project directory and install node packages:
```
cd alpha;
npm install;
```

The development environment is ready now.

# Directions from Angular

## Development server

Run `ng serve` for a dev server. Navigate to `http://localhost:4200/`. The app will automatically reload if you change any of the source files.

## Code scaffolding

Run `ng generate component component-name` to generate a new component. You can also use `ng generate directive|pipe|service|class|guard|interface|enum|module`.

## Build

Run `ng build` to build the project. The build artifacts will be stored in the `dist/` directory. Use the `-prod` flag for a production build.

## Running unit tests

Run `ng test` to execute the unit tests via [Karma](https://karma-runner.github.io).

## Running end-to-end tests

Run `ng e2e` to execute the end-to-end tests via [Protractor](http://www.protractortest.org/).

## Further help

To get more help on the Angular CLI use `ng help` or go check out the [Angular CLI README](https://github.com/angular/angular-cli/blob/master/README.md).


# GIT work flow

Our git work flow is based on Gitlab issue-branches.
The code from issue-branches is rebased to branch `master`.
Note: it is not merged, but rebased.
Production ready code is kept in branches named `stable*`. These branches
are strongly protected. Only master can commit to them.

The details of our git work flow:

1.   Create the issue on Gitlab. The issue can be created by anybody.

2.   Master assigns the issue to developer.

3.   Developer creates `issue-branch` at Gitlab, using its UI.

4.   Developer gets remote `issue-branch` to his local
repo `git fetch -p; git checkout issue-branch;`
and puts the label `doing` on issue-branch at Gitlab.

5.   Developer codes in issue-branch, pushing commits to remote issue-branch.

6.   In long lasting branches, it may be necessary to rebase master on this branch.
This procedure is described
[here](https://github.com/edx/edx-platform/wiki/How-to-Rebase-a-Pull-Request#squash-your-changes) very well.
In short: squash all commits in issue-branch to one commit
and rebase master on issue-branch:
    ```
    git merge-base issue-branch master; # find the start commit for rebase
    git rebase -i ${HASH}; # replace ${HASH} with hash got from previous command
    ```

    One line equivalent of above commands:
    ```
    git rebase -i `git merge-base issue-branch master`
    ```

    Wiser one line equivalent of above commands (resolves current branch name):
    ```
    BRANCH="$(git rev-parse --abbrev-ref HEAD)";HASH="$(git merge-base ${BRANCH} master)"; git rebase -i ${HASH};
    ```
    And the smartest way: use script `scripts/squash`.

    After that local and remote issue-branches are diverged,
    so run `git push --force-with-lease origin issue-branch`.
    Warning: remote issue-branch will be completely overwritten
    with your local version of issue-branch.
    Do it only if you need the code from master for your work in issue-branch.

    The alternate way to squash all commits in issue branch:
    ```
    git checkout master;
    git branch my-merge-branch;
    git checkout my-merge-branch;
    git merge --squash issue-branch;
    ```
    Resolve conflicts, if any, and make commit with your new message.
    Everything should be done now. Check if you are happy with result.
    If yes, you can replace existing issue-branch with my-merge-branch,
    this way:
    ```
    git checkout my-merge-branch;
    git branch -D issue-branch;
    git branch issue-branch;
    ```
    Then push force issue-branch to origin and delete my-merge-branch locally.
    Described method can be easier to run, if you have a lot of conflicts
    in issue branch, especially caused by your own commits.

7.   When the code in issue-branch is ready or it needs review,
developer puts label `review` on issue-branch at Gitlab.
It is not necessary to squash any commits or rebase
master on issue-branch at this point.

8.   Master checks the code and puts the labels `approved` or `rejected`,
making comments related to this actions at Gitlab. Then he removes label `review`.

9.   If the code is `rejected`, developer goes to point 5 of this work flow, removing label `rejected`.

10.   If the code is `approved`, the issue-branch can be rebased to branch master.
All commits in issue-branch should be squashed to one. The commit message
should contain the string `close (or closes, fix, fixes etc.) #number_of_issue`.
Rebasing to master is done by master usually, because his knowledge of the work
in all feature branches, lets him resolve potential conflicts in code in
easier way. This is done with commands:
```
git checkout issue-branch;
git pull origin issue-branch;
git rebase master -i; # squash commits to one, put 'fix #number_of_issue' into commit message
git checkout master;
git merge issue-branch;
git push origin master;
git push --delete origin issue-branch  # delete remote issue-branch
git branch -d issue-branch   # delete local issue-branch
```
