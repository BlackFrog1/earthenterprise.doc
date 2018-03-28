## Open GEE Release Process

This document describes the process for creating an Open GEE release.

### A Note on Version Numbering

Open GEE version numbers are designed to work well with package managers like
yum and apt. In yum and apt, `5.2.0-beta1` comes *after* `5.2.0`. Thus, all
version numbers for Open GEE have a dash at the end that indicates whether the
release is beta or final and the order of the releases.

### Release Process

Once work on a release has been completed:

1. **Select the new release number**

    Use the principles of [semantic versioning](http://semver.org/) to select
    the new release number.

1. **Create release branch**

    Create a release branch named `release_X.Y.Z` where `X.Y.Z` is the release
    number. This branch should point to the last commit on master that will be
    included in the release.

1. **Update version number**

    The following files need to be updated with the latest version number.
    Commit these updates to the release branch.

    - earth_enterprise/src/support/geecheck/geecheck.pl (2 places)
    - earth_enterprise/src/version.txt
    - earth_enterprise/src/fusion/portableglobe/cutter/cgi-bin/geecheck_tests/common.py
    - earth_enterprise/src/installer/common.sh (2 places)
    - earth_enterprise/BUILD.md 
    - earth_enterprise/Upgrade.md (if applicable)

1. **Update documentation**

    Make the following documentation updates and then commit them to the release
    branch.

    1. Create release notes for the new release. Pattern it after the [existing release notes](http://www.opengee.org/geedocs/answer/7160000.html).
    1. Update all the existing release notes pages with a link to the new
    release notes in the right sidebar.
    1. Update _earthenterprise/docs/geedocs/&lt;version_number&gt;/index.html_:
        1. Update the release notes link to point to the new release notes.
        1. Update the text with the new version number.
    1. Update earthenterprise/docs/geedocs/index.html to indicate that the new
        version is now the current version.

1. **Update Supported Platforms**

    If necessary, update the supported platforms and versions in _src/fusion/portableglobe/cutter/cgi-bin/geecheck_tests/user_tests/common.py_.

1. **Create the pre-release in Github**

    For instructions, see <https://help.github.com/articles/creating-releases/>.
    Be sure to specify the release branch and indicate that it is a pre-release.
    The tag should be `X.Y.Z-1.beta` where `X.Y.Z` is the version number.

1. **Build and test release**

    Testing should be done on all supported operating systems. The test systems
    should be clean VMs or docker images that have not been used to build or run
    Open GEE previously. Testers should test the latest pre-release. More
    information about the testing process will be released soon.

    If testing reveals any bugs that need to be fixed, those fixes should be
    applied to the release branch. The release branch can be merged into master
    immediately to make those changes available in master. Otherwise, the
    changes will be merged into master when the release is complete.

    Each time a new set of fixes is applied to the release branch, a new
    pre-release should be generated and regression testing should be performed
    on the pre-release. The tag for the new pre-release should be
    `X.Y.Z-W.beta` where `X.Y.Z` is the version number and `W` is one more than
    the previous beta number.

1. **Update release notes**

    Make any last minute changes to the release notes based on changes made
    while testing. Commit any release notes changes to the release branch.

1. **Create the release in Github**

    For instructions, see <https://help.github.com/articles/creating-releases/>.
    Be sure to specify the release branch. The tag should be `X.Y.Z-W.final`
    where `X.Y.Z` is the version number and `W` is one more than the last beta
    number.

    The default GitHub release process is not really compatible with git-lfs, 
    which is used by the earthenterprise repo. It will create source archive
    files which do not include the actual git-lfs files, instead just the links
    to them. Therefore, additional steps are required to create the full source
    plus git-lfs binary files.

    1. Make a temporary directory to contain the repo files. e.g. `mkdir earthenterprise_5.2.1; cd earthenterprise_5.2.1`
   
    1. Make sure you have a copy of the `build_release_archive.sh` script in the temporary directory. It is
    in the earthenterprise repo or can be downloaded 
    [here](https://github.com/google/earthenterprise/raw/master/scripts/build_release_archive.sh).

    1. Set execute permissions on the script if necessary `chmod a+x build_release_archive.sh`

    1. Run script, specifying the release branch from the repo to package. e.g. 
    `./build_release_archive.sh release_5.2.1`

    1. After the script has run and created the archives `earthenterprise.tar.gz` and `earthenterprise.zip`, 
    rename them to include the version tag, so that it's clear what the archive contains. e.g. 
    `mv earthenterprise.tar.gz earthenterprise-5.2.1-6.final.tar.gz`

    1. Edit the release page to upload these 2 additional files. For an example, see [Open GEE 5.2.1 Release](https://github.com/google/earthenterprise/releases/tag/5.2.1-6.final)


1. **Merge changes back into master**

    Merge the release branch back into master so that any changes made during
    the release are included in master.  This may involve resolving some merge
    conflicts if other work has already been merged into master.

1. **Copy docs for next release**

    This step can be done on master at any point after the release notes and
    version number changes have been merged back into master. The main concern
    is to ensure that these changes don't end up in the release.
  On the master branch:
    1. Copy the docs for the prior version to the new version directory, e.g.:

        ```bash
        cd earthenterprise/docs/geedocs
        cp -rp 5.2.0 5.2.1
        ```

    1. Edit earthenterprise/docs/geedocs/index.html to add new entry for the new
     version to the head of the list. Also indicate that this version is under development.
    1. Link manual to the newest development documents:

        ```bash
        cd earth_enterprise/docs/landing_page
        rm manual
        ln -s ../../../docs/geedocs/5.2.1 manual
        ```

    1. Merge these changes to master.

1. **Update README.md**

    Update the main README.md page for the Github repo to include a link to the
    latest release and the latest docs.

1. **Announce the release**

    Write and publish a blog post announcing the new release and highlighting
    some of the major changes. Also announce the release on the Google Earth
    Enterprise Google Group and the Slack channel with a link to the blog post.

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