## Open GEE Release Process

This document describes the process for creating an Open GEE release.

### A Note on Version Numbering

Open GEE version numbers are designed to work well with package managers like
yum and apt. In yum and apt, `5.2.5-714.9` comes *after* `5.2.5`. Thus, all
version numbers for Open GEE have a dash followed by a build number.  This
build number can be prepended with a `b` to indicate the build is not a
releasable build (was not built from a properly created release branch).

### Version string command

To find the version string associated with the head of a particular branch
check out the branch you want to get the version string from and run the following command:

    ```bash
    $ git checkout release_5.2.5
    $ ./earth_enterprise/src/scons/getversion.py -l
    5.2.5-714.9
    ```
You will need to get the version string of a release branch to perform some of the steps below

### Release Process

Once work on a release has been completed:

1. **Select the new release number**

    Use the principles of [semantic versioning](http://semver.org/) to select
    the new release number.

2. **Create the first release candidate tag**

    Create the first release candidate tag from latest commit on master.
    The tag should be `X.Y.Z-RC1` where `X.Y.Z` is the version number.

    Use git annotated tags for creating tags, so we can keep track of who made
    the tag. (If you don't use annotated tags, then GitHub will report
    the author of the tag as the author of the last commit, rather than the
    user who created the tag.)

    Example:
    ```bash
    $ git checkout master
    $ git tag -a 5.2.5-RC1 -m "Open GEE 5.2.5 Release candidate 1"
    $
    ```

3. **Create release branch**

    Create a release branch named `release_X.Y.Z` where `X.Y.Z` is the
    release number. This branch should be based on the RC1 tag you just
    created in the previous step.

    Example:
    ```bash
    $ git checkout -b release_5.2.5 5.2.5-RC1
    $
    ```

4. **Update version number**

    The following files need to be updated with the latest version number.
    Commit these updates to the release branch.

    - earth_enterprise/src/support/geecheck/geecheck.pl (2 places)
    - earth_enterprise/src/fusion_version.txt
    - earth_enterprise/src/fusion/portableglobe/cutter/cgi-bin/geecheck_tests/common.py
    - earth_enterprise/BUILD.md 
    - earth_enterprise/Upgrade.md (if applicable)
  
    Note: many times this is done in the master branch once the next version
    number is known.  If this is the case then you may not have to change these
    files on the release branch.  Also, if you know what the next version number
    will be it is a good idea to change these files in master after the release
    branch is created.

5. **Update documentation**

    Make the following documentation updates and then commit them to the release branch.

    1. Create release notes for the new release. Pattern it after the
       [existing release notes](http://www.opengee.org/geedocs/answer/7160000.html).
    2. Update all the existing release notes pages with a link to the new
       release notes in the right sidebar.
    3. Update _earthenterprise/docs/geedocs/&lt;version_number&gt;/index.html_:
        1. Update the release notes link to point to the new release notes.
        2. Update the text with the new version number.
    4. Update earthenterprise/docs/geedocs/index.html to indicate that the
       new version is now the current version.
    5. Update all edited or new docs to include the current year in the
       copyright notice. Example: if the year of the update is 2018 and copyright
       year notice was '2016-2017' change it to '2016-2018'.

6. **Update Supported Platforms**

    If necessary, update the supported platforms and versions in _src/fusion/portableglobe/cutter/cgi-bin/geecheck_tests/user_tests/common.py_.

7. **Create the first pre-release in Github**

    Then follow the instructions, at <https://help.github.com/articles/creating-releases/>. Be
    sure to specify the release candidate tag you just created and indicate
    that it is a pre-release.

8. **Create a source archive artifact for the release**

    The default GitHub release process is not really compatible with git-lfs,
    which is used by the earthenterprise repo. It will create source archive
    files which do not include the actual git-lfs files, instead just the
    links to them.

    Also, version files that are needed for the build process are generated
    by tags within the git repository.  These version files need to be added
    to the archive file to make the source within that file build-able
    without git.

    Therefore, additional steps are required to create the
    full source plus git-lfs binary files and the needed version files.

    **Note**: This process needs to be done for all pre releases as well as
    the final release.

    1. Make a temporary directory to contain the repo files. e.g.
       `mkdir earthenterprise_5.2.5; cd earthenterprise_5.2.5`

    2. Make sure you have a copy of the `build_release_archive.sh` script in
       the temporary directory. It is in the earthenterprise repo or can
       be downloaded [here](https://github.com/google/earthenterprise/raw/master/scripts/build_release_archive.sh).

    3. Set execute permissions on the script if necessary
       `chmod a+x build_release_archive.sh`

    4. Run the script, specifying the release branch from the repo to
       package. e.g. `./build_release_archive.sh release_5.2.5`

    5. After the script has run and created the archives
       `earthenterprise.tar.gz` and `earthenterprise.zip`, rename them to
       include the version string (see above for the command to retrieve
       the version string), so that it's clear what the archive contains.
       e.g `mv earthenterprise.tar.gz earthenterprise-5.2.5-714.9.tar.gz`

    6. Edit the release page to upload these 2 additional files. For an
       example, see [Open GEE 5.2.5 Release]
       (https://github.com/google/earthenterprise/releases/tag/5.2.5-714.9)

9.  **Build and test release**

    Testing should be done on all supported operating systems. The test
    systems should be clean VMs or docker images that have not been used to
    build or run Open GEE previously. Testers should test the latest
    pre-release release candidate. More information about the testing
    process will be released soon.

    If testing reveals any bugs that need to be fixed, those fixes should be
    applied to the release branch. The release branch can be merged into
    master immediately to make those changes available in master. Otherwise,
    the changes will be merged into master when the release is complete.

    Each time a new set of fixes is applied to the release branch, a new
    pre-release candidate should be generated and regression testing should
    be performed on the pre-release. The tag for the new pre-release candidate
    should be `X.Y.Z-RCN` where `X.Y.Z` is the version number and `N` is
    one more than the previous release candidate number (initially as stated
    above RC1).

    As was done with the first pre-release tag use git annotated tags for
    creating subsequent pre-release tags, so we can keep track of who made
    the tag.  Also follow the same instructions as was followed for creating
    the first release candidate to publish all subsequent release candidates.

10. **Update release notes**

    Make any last minute changes to the release notes based on changes made
    while testing. Commit any release notes changes to the release branch.

11. **Create the release in Github**

    Once a pre-release candidate is declared good to release then apply a
    release tag which will match the version string outputted from the
    version string command (see above for the version string command to
    run to get this string).

    **NOTE:** the head of the release branch should be pointing to the
    latest pre-release candidate at this point. For example, if the 3rd
    release candidate was declared to be the one that will be shipped then
    the head of the release branch in the above example should be pointing
    to the tag `5.2.5-RC3`.

    Release tag example:

    ```bash
    $ git tag -a 5.2.5-714.9 -m "Open GEE 5.2.3 Final"
    $
    ```

    Again follow the instructions at <https://help.github.com/articles/creating-releases/>.
    But this time specify the final release tag you just created and
    do not select 'this release as a pre-release'.

12. **Merge changes back into master**

    Merge the release branch back into master so that any changes made during
    the release are included in master.  This may involve resolving some
    merge conflicts if other work has already been merged into master.

    If the next step (Copy docs for next release) has already been done,
    also merge any documentation changes made during the release to the
    documentation for the next version.

13. **Copy docs for next release**

    This step can be done on master at any point after the release notes and
    version number changes have been merged back into master. The main
    concern is to ensure that these changes don't end up in the release.
    On the master branch:

    1. Copy the docs for the prior version to the new version directory,
       e.g.:

       ```bash
       cd earthenterprise/docs/geedocs
       cp -rp 5.2.5 5.2.6
       ```

    2. Edit earthenterprise/docs/_data/versions.yml to add new entry for the
       new version to the head of the list. Also indicate that this version
       is under development.
    3. Update symlinks to the newest development documents:

        ```bash
        cd docs/geedocs
        rm answer art css
        ln -s 5.2.6/answer answer
        ln -s 5.2.6/art art
        ln -s 5.2.6/css css
        ```

        ```bash
        cd earth_enterprise/docs/landing_page
        rm manual
        ln -s ../../../docs/geedocs/5.2.6 manual
        ```
    4. Merge these changes to master.

14. **Update README.md**

    Update the main README.md page for the Github repo to include a link to the
    latest release and the latest docs.

15. **Announce the release**

    Write and publish a blog post announcing the new release and highlighting
    some of the major changes. Also announce the release on the Google Earth
    Enterprise Google Group and the Slack channel with a link to the blog post.

16. **Delete pre-release RC tags**

    This is an optional step but if done it must be done **after** creating
    the final and permanent release tag for the release.  Also, clean up the
    corresponding releases on the release page.

17. **Set alpha tag for next release**

    Once the next version is known then an alpha tag should be set on the master
    branch so builds from master can start using that version

    Example:
    ```bash
    git tag -a 5.2.6-0.alpha -m "Setting next version release version tag to 5.2.6"
    ```

### Merging the release branch into master

The release branch can be merged into master at any point during the release
process to bring changes made for the release into the master branch. This may
be necessary to bring fixes that were included on the release branch into master
in a timely manner. The release branch can be merged into master multiple
times, so you can continue doing work on the release branch after merging it
into master. Keep in mind, however, that this will publish any documentation
changes from the release branch (such as the release notes) on opengee.org. Also
note that you may have to resolve merge conflicts if other work has already been
merged into master since the release branch was created. If changes do not need
to be brought into master immediately you can wait until the end of the release
process when all changes will be merged to master.

## Creating releases from previous releases

It was mentioned earlier that a new release branch is started from the latest
commit of the master branch.  This is not always the case.  It is possible to
create a release based on a previous release.  This is done for a couple of
reasons and there are two methods for doing this.  Which one you use depends
on the needs of the release.

### Re-opening a release

Sometimes despite all the testing a critical bug can slip by and get shipped
with the release.  If the bug is critical enough and is low risk the best approach
might be to re-open the release with the bug and fix the bug in that release
branch.  The new release will have the same version number (e.g. `5.2.5`) but will
be distinguished from previous releases with that same version number by its build
number (i.e. `5.2.5-714.9` vs `5.2.5-714.10`).  To re-open a release you just need
to be sure that the `RC1` tag for that release branch still exists.  If it was deleted
then you can recreated it using the build number form the last build of the release
you shipped.

Example if the previous shipped build was `5.2.5-714.9`:

```bash
git tag -a 5.2.5-RC1 "$(git rev-list --max-count=1 --skip=9 --topo-order 5.2.5-714.9)" -m "release candidate 1 for 5.2.5"
```

Once you have the first release candidate tag you can then follow the same release
described above.  Note: a release branch can produce multiple release builds.

### Creating a release branch from a release branch

Sometimes is is desired to ship an urgently needed feature before the next major
release is shipped.  In this case you might want a new version number to make the
two builds even more distinguishable from each other.  In this case you would create
a new release branch based off of the latest commit on the previous release you want
this new release to be based on.  You should pick a new version number for this
release branch and that number should extend the version number based on the previous
release.  For example if your previous release was `5.3.0` you would want to use `5.3.1`.
However, if the release you are basing this new release on had a version number of
`5.2.5` then you want to make a 4 part version number and have `5.2.5.1` as this new
release version number.  Other than what you based the new release branch on and
how you pick the version number the rest of the process is the same as described above.
