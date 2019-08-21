---
layout: single
categories: [blog]
permalink: /:categories/:year/:month/:day/:title/
toc: true
toc_sticky: true
---

I was able to setup my personal site and shared how I did it in this [blog]({% post_url 2019-08-12-building-my-website-using-GitHub-Pages-and-Jekyll %}). What I wanted to learn next was setting up a simple build environment for this site to run a simple test. For this task, I used [Travis CI](https://travis-ci.org/) to build and test new changes pushed to this site's repository.

## Signup with Travis CI

1. Sign up for a [Travis CI](https://travis-ci.org/) account using your [GitHub](https://github.com/) account. Travis CI will sync with your GItHub account's repositories.

2. Go to your Travis CI [profile](https://travis-ci.org/account/repositories) and enable the repository for building.

    ![Repository enabled for Build](/assets/images/enable-repo-for-travis.png)

    I did not change anything from the default settings for the repository for now.

## Setup Travis CI

1. After signing up with Travic CI and enabling the repository for build, I basically followed the steps stated at the [Jekyll Docs Travis CI page](https://jekyllrb.com/docs/continuous-integration/travis-ci/). For the test script, `jekyll build` and [html-proofer](https://github.com/gjtorikian/html-proofer) are used. Test script has the following content:

    ```
    #!/usr/bin/env bash
    set -e # halt script on error

    bundle exec jekyll build
    bundle exec htmlproofer ./_site/ --disable-external --allow-hash-href
    ```

    To make sure the build runs, `gem 'html-proofer'` is also added in `Gemfile`.

    The Travis CI configuration file, `.travis.yml`, has the following content:

    ```
    language: ruby
    rvm:
      - 2.5.5

    before_script:
    - gem update --system
    - gem install bundler
    - chmod +x ./script/cibuild # or do this locally and commit

    # Assume bundler is being used, therefore
    # the `install` step will run `bundle install` by default.
    script: ./script/cibuild

    # branch whitelist, only for GitHub Pages
    branches:
      only:
      - /release-(.*)/ # test every branch which starts with "release-"
      - develop
      - master

    env:
      global:
      - NOKOGIRI_USE_SYSTEM_LIBRARIES=true # speeds up installation of html-proofer

    addons:
      apt:
        packages:
        - libcurl4-openssl-dev

    sudo: false # route your build to the container-based infrastructure for a faster build

    cache: bundler # caching bundler gem packages will speed up build

    # Optional: disable email notifications about the outcome of your builds
    notifications:
      email: false
    ```

## Run Build on Travis CI

To run the build on Travis CI, simply push new commits to the remote branches added on the Travis config file. Fix any errors on the build runs on travis.

### Build Errors Encountered

From my experience, I experienced two issues on my build configuration. The first error I got was related with the environment or platform that the build run. Travis CI build runs on a linux platform but the `Gemfile.lock` file of my repository only has the `x64-mingw32` platform in the list since I work using Git Bash commandline in Windows. The fix is either removing `Gemfile.lock` file or adding new items in the platforms list in `Gemfile.lock`. I chose the second solution and ran the following commands:

```
bundle lock --add-platform ruby
bundle lock --add-platform x86_64-linux
```

`Gemfile.lock` now has the updated platform list:

```
PLATFORMS
  ruby
  x64-mingw32
  x86_64-linux
```

The second error was with the bundle command in the build script. The build returned the following error:

```
$ bundle install --jobs=3 --retry=3 --deployment --path=${BUNDLE_PATH:-vendor/bundle}
You must use Bundler 2 or greater with this lockfile.
The command "eval bundle install --jobs=3 --retry=3 --deployment --path=${BUNDLE_PATH:-vendor/bundle} " failed. Retrying, 2 of 3.
You must use Bundler 2 or greater with this lockfile.
The command "eval bundle install --jobs=3 --retry=3 --deployment --path=${BUNDLE_PATH:-vendor/bundle} " failed. Retrying, 3 of 3.
You must use Bundler 2 or greater with this lockfile.
The command "eval bundle install --jobs=3 --retry=3 --deployment --path=${BUNDLE_PATH:-vendor/bundle} " failed 3 times.
The command "bundle install --jobs=3 --retry=3 --deployment --path=${BUNDLE_PATH:-vendor/bundle}" failed and exited with 20 during .
Your build has been stopped.
```

I learned from [Travis CI Doc](https://docs.travis-ci.com/user/languages/ruby/#bundler-20) that Bundler 2.0 can be installed since I am using Ruby 2.5.5. I added the following lines on `.travis.yml` file under `before_script:` section (already shows in the config [above](#setup-travis-ci)):

```
- gem update --system
- gem install bundler
```

## Monitor Builds on Travis CI

Builds can be monitored by visiting [Travis CI](https://travis-ci.org/). Since I am new with Travis CI, there were instances that I wondered why some expected builds did not show on the Current tab on Travis CI. That actually meant that the build did not start. You can check if a build started or not by going to the Requests page. Click on **More options** button on the right side of the page and then click on **Requests** button:

![Build Requests Button](/assets/images/travis-requests-button.png)

The build requests will be shown, any build requests not created will show the reason on the last column:

![Build Requests](/assets/images/travis-requests.png)

Make necessary fix or changes to make sure expected build requests are created.

### Add Build Status on Repository's README

Last thing I wanted to do with this simple build setup was a build status image on this site's README. I got the status image from Travis CI page for the repository.

![Build Status](/assets/images/travis-build-status.png)

Clicking on the status image shows a prompt with options for branch to get status and format. The Result code can be added on intended file. For this one, since I have a markdown `README.md` file, I chose Markdown format and added the result on `README.md` file.

![Build Status Image Prompt](/assets/images/travis-build-status-image.png)

## Conclusion

This is a simple Travis CI setup but this gave me a good initial experience with setting up automated build environment for a project. Automated build and test setup is a good software development practice which I want to implement in my future personal projects.

Be sure to implement them in your projects also! Keep learning!

Resources:

- [https://jekyllrb.com/docs/continuous-integration/travis-ci/](https://jekyllrb.com/docs/continuous-integration/travis-ci/)
- [https://docs.travis-ci.com/](https://docs.travis-ci.com/)
- [https://docs.travis-ci.com/user/languages/ruby/#bundler-20](https://docs.travis-ci.com/user/languages/ruby/#bundler-20)