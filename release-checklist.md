Release Checklist
=================

Pre-Release
-----------

Follow this checklist in the few days or hours prior to a release:

  - Enter this command to freeze the versions of dependencies if this
    has not been done recently:

        make freeze

    This step may update version numbers of some dependencies which
    could break the tests in the steps coming up, so run this step only
    when we really want to update dependency versions.

  - Set version number in `cloudmarker/__init__.py` in `A.B.C.devN`
    format where `A.B.C` is the version of the next release and `N` is
    an incrementing integer that can be changed for every test
    distribution upload.

  - Build a test distribution, upload it to Test PyPI, and verify that
    it can be installed:

        make dist
        make test-upload
        make verify-test-upload

  - Ensure the release looks good at
    <https://test.pypi.org/project/cloudmarker/>.

  - Upload the test distribution to PyPI and verify that it can be
    installed:

        make upload
        make verify-upload

  - Ensure the release looks good at
    <https://pypi.org/project/cloudmarker/>.

  - Fix any issues found. Edit `pkg-requirements.txt` manually if
    required. Commit fixes.

  - Update changelog in `CHANGES.rst`.

  - Create pull requests for fixes and changelog update.

  - Merge pull requests.


Release
-------

  - Create a new branch for release activity:

        git checkout -b release

  - Update version in `cloudmarker/__init__.py` to a stable version in
    `A.B.C` format.

  - If the release version is less than `1.0.0`, review changelog and
    ensure that the new stable release version is correct. The stable
    version is arrived at from the last stable version according to
    these rules:

      - If the last release is `A.B.C` and only bug fixes have occurred
        since then, the new release is `A.B.(C+1)`.

      - If the last release is `A.B.C` and bug fixes and/or new features
        or improvements have been added, the new release is `A.(B+1).0`.

  - If the release version is `1.0.0` or later, review changelog and
    ensure that the new stable release version is correct. The stable
    version is arrived at from the last stable version according to
    these rules:

      - If the last release is `A.B.C` and only bug fixes have occurred
        since then, the new release is `A.B.(C+1)`.

      - If the last release is `A.B.C` and bug fixes and/or new features
        or improvements have been added such that no existing use cases
        are broken, the new release is `A.(B+1).0`.

      - If the last release is `A.B.C` and the new release breaks (even
        slightly) existing use cases, the new release is `(A+1).0.0`.

  - Create pull request:

        VERSION=A.B.C
        git add cloudmarker/__init__.py
        git commit -m "Set version to $VERSION"
        git push -u origin release

  - Ensure that the last commit looks good:

        git log
        make checks

  - Merge pull request:

        git checkout master
        git merge release
        git push origin master
        git branch -d release
        git push -d origin release

  - Build distribution, upload it to Test PyPI, and verify it:

        make dist
        make test-upload
        make verify-test-upload

  - Ensure the release looks good at
    <https://test.pypi.org/project/cloudmarker/>.

  - Upload the distribution to PyPI and verify it:

        make upload
        make verify-upload

  - Ensure the release looks good at
    <https://pypi.org/project/cloudmarker/>.

  - Tag the release:

        VERSION=A.B.C
        git tag "$VERSION" -m "Cloudmarker $VERSION"
        git push origin master "$VERSION"


Post-Release
------------

  - Go to PyPI and delete any test/dev releases.

  - Update version in `cloudmarker/__init__.py` to dev version again,
    i.e., `A.B.C.devN` format. The patch version, i.e., the `C` part in
    `A.B.C.devN` should be incremented by one while setting the dev
    version. For example, if the last release version was `1.2.0`, set
    the new version to `1.2.1.dev1`.
