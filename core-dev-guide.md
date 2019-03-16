Core Developer Guidelines
=========================
These guidelines apply to the core developers, i.e., those with write
(push) access to the [cloudmarker/cloudmarker][repo] repo.

[repo]: https://github.com/cloudmarker/cloudmarker


Contents
--------
* [Develop](#develop)
* [Review](#review)
* [Merge](#merge)


Develop
-------

  - Clone the repository:

        git clone https://github.com/cloudmarker/cloudmarker.git

  - Create a new *topic branch* to develop a new feature or bug fix. We will
    denote this as `topic-branch` in the commands below:

        cd cloudmarker
        git checkout -b topic-branch

    The `topic-branch` here is only an example name to act as a
    placeholder. Please name your topic branch with a meaningful name.

  - Work on your feature or bug fix in the topic branch.

        git add .
        git commit
        git push -u origin topic-branch

  - Go to the [project repository][repo] on GitHub, switch to the topic
    branch, and click *Compare & pull request*.

  - If you think anyone in particular should review the pull request
    (PR), go to the PR page, then in the right sidebar under
    *Reviewers* and select appropriate reviewers.


Review
------

  - Anyone can review the pull request (PR), comment on it, reject it,
    or approve it.

  - While reviewing, please post comments on appropriate lines of code
    in the *Files changed* tab.

  - The author of the PR (PR author) may address the issues raised in
    the comments either in the same PR or in a future PR. If the PR
    author decides to address some issues in a future PR, he/she should
    say so in the comments section and justify the decision if
    necessary.

  - A reviewer should approve the PR only if he/she is okay with the
    current changes and the PR author's decision of addressing pending
    issues in a future PR.

  - After a PR is approved by at least one core developer, anyone may
    merge it to `master`. The PR author himself/herself may also
    merge the PR to `master`.


### Review Expectations

  - It is not necessary to review every line of code carefully while
    reviewing. Reviewing is volunteer work. Spend only as much time as
    you reasonably can on reviews. Use your limited time wisely. Pick
    the interesting parts, review what you can, and *approve* the pull
    request (PR) or *request changes* depending on how you feel about
    it.

  - The intention of the review is not to catch every bug but to be able
    to understand and critique the design, discuss any interesting
    decisions that went into the PR, and spot obvious mistakes.

  - Real world testing and usage will catch the issues that affect us in
    actual usage of the software. It is okay to wait until real world
    usage of a change to find obscure issues.

  - The ease of future development and the ease of extending the
    software to handle new use cases is the ultimate test of the design.
    It is okay to wait until future development to recognize design
    issues.

  - It is good but not necessary to be pedantic about coding
    conventions, English grammar, typos, etc. in a PR. Just to be clear:
    You are encouraged to be pedantic but if you are not, that's fine
    too.


Merge
-----

  - Enter the following commands to merge the pull request (PR) into
    `master`:

        git checkout topic-branch
        git checkout master
        git merge topic-branch

  - We try to keep the commit history as linear as possible, so if the
    `git merge` command is about to create a merge commit that could be
    avoided by rebasing the topic branch on `master` and the PR author
    belongs to our organization on GitHub, is easily reachable, and is
    likely to respond within 48 hours, then consider aborting the
    commit. Check [orgs/cloudmarker/people][people] to see who belongs
    to our organization on GitHub. We want to maintain a linear commit
    history but not at the cost of slowing down the pace of the project.

    To abort the merge commit when it is about to be created, after the
    editor comes up with a merge message file, delete all lines from the
    merge message file, and save the file.

    To abort the merge commit just after it has been created, enter the
    following command:

        git reset HEAD^

  - After aborting the merge commit, ask the PR author to rebase the
    topic branch on `master` and update the PR. The PR author then needs
    to enter the following commands:

        git checkout topic-branch
        git rebase master
        git push -f origin topic-branch

    Note once again that we should ask the PR author to rebase the
    PR on `master` only if the PR author also belongs to our
    organization on GitHub, is easily reachable, and is likely to
    respond within 2 days. If one or more of these conditions are not
    met, then proceed with the merge even if it creates a merge commit.

    If the PR author does not within 2 days, i.e., 48 hours, merge the
    PR with a merge commit.

    Especially, note that if the PR author does not belong to our
    organization on GitHub, merge the PR even if it creates a merge
    commit. An external contributor taking time out of his/her life to
    send us a PR is already pretty generous. We do not want to impose
    additional work to maintain a linear commit history on such external
    contributors.

  - After merging the PR, enter the following command to verify that all
    tests and checks pass:

        make checks

  - Enter the following command to push the updated `master`:

        git push origin master

[people]: https://github.com/orgs/cloudmarker/people
