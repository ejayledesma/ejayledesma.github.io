---
layout: single
categories: [blog]
permalink: /:categories/:year/:month/:day/:title/
toc: true
toc_sticky: true
---

A few months ago, I logged into my [GitHub](https://github.com/) account. I had it since 2015 but wasn't able to add anything until recently. I was trying to learn how things work around Git and GitHub when I learned about hosting static websites via [GitHub Pages](https://pages.github.com/). Since I recently had an idea of having my personal site to put things I learned and some projects that I will do, I grabbed this opportunity to setup a personal site and host it via GitHub Pages.


## Set Up Website with Jekyll and GitHub Pages
Creating and hosting a site on GitHub is pretty easy, based on my experience. I used [Jekyll](https://jekyllrb.com/) as my static site generator because it is easy to setup and use. Also it is adopted in GitHub Pages. Below are the steps I took to setup my personal site which you can try on building your own site:

1. Create a repository following the naming format `<username>.github.io`

    This format basically tells GitHub that you are creating a User Page. Another type of a GitHub Page is the Project Page which is available at `<username>.github.io/project-name/`.

2. Choose a jekyll theme

    I first selected a theme from the theme chooser. To do this, go to the **Settings** Page, scroll down to the **GitHub Pages** section, and click the **Choose a theme** button.

    ![Choose a theme screenshot](/assets/images/choose-a-theme.png)

    There's a list of [officially supported themes](https://pages.github.com/themes/) by GitHub Pages. Click on any theme to see how they look and click **Select theme** button to choose.

    ![Theme menu](/assets/images/theme-chooser.png)

    After choosing a theme, a new file will be added to the repository, `_config.yml`. It just contained the line `theme: jekyll-theme-architect`, which was the theme I chose.

    From here, a static site is already available via `https://<username>.github.io`.

3. Developer Setup on PC

    So far, all the steps I listed was done in the browser. To actually work on adding content and setting up the configurations for the site, I needed to setup my local workspace for the project. These steps are also documented on this repository's [wiki](https://github.com/ejayledesma/ejayledesma.github.io/wiki).

    - Install `ruby` and `jekyll`.
    - Clone this repository and move to the directory.

    ```
    git clone git@github.com:username/username.github.io.git && cd username.github.io
    ```

    - Create a file named `Gemfile` and write the following content:

    ```
    source 'https://rubygems.org'
    gem "github-pages", group: :jekyll_plugins
    ```

    - Build and serve the page, it will be in [http://localhost:4000](http://localhost:4000) by default and rebuilds the site for every changes saved.

    ```
    bundle exec jekyll serve
    ```

    Now, any changes can be tested locally by serving the site on the local server.

4. Choose a remote jekyll theme

    With all the basics done, I wanted to use a different jekyll theme not included on the officially [supported jekyll themes](https://pages.github.com/themes/) on GitHub Pages. I browsed some themes on [https://jekyllthemes.io/](https://jekyllthemes.io/) and I found [minimal-mistakes](https://mmistakes.github.io/minimal-mistakes/) theme. In order to use a theme not officially supported by GitHub Pages, simply update the `_config.yml` file. Instead of using `theme:`, replace it with `remote_theme:`. My `_config.yml` file now has the line:

    ```
    remote_theme: "mmistakes/minimal-mistakes@4.16.5"
    ```

    A requirement with the [minimal-mistakes](https://mmistakes.github.io/minimal-mistakes/) theme was to add `jekyll-include-cache` to the plugins list. I added it to `_config.yml`:

    ```
    plugins:
      - jekyll-include-cache
    ```

    And added it to `Gemfile`:

    ```
    source "https://rubygems.org"
    gem "github-pages", group: :jekyll_plugins
    gem 'jekyll-include-cache'
    ```

    Commit and push the change on `_config.yml` and `Gemfile`. `https://<username>.github.io` should be updated with the new theme.

## Adding Content to the Site
Once website is hosted, adding content should be easy. Make sure to read on the docs of [Jekyll](https://jekyllrb.com/docs/) and the theme chosen. For my site, I basically followed the guide from the documentation of the jekyll theme ([minimal-mistakes](https://mmistakes.github.io/minimal-mistakes/docs/quick-start-guide/)) I am using. For the content of my site, I used [Markdown](https://daringfireball.net/projects/markdown/) syntax to add styling to the pages/posts. A good resource to learn Markdown is to read this [article](https://guides.github.com/features/mastering-markdown/).
Below are the steps I took to add content to my site and some other additional tasks:

1. Pages

    The home page content is inside the `index.html` file. At the start of the file, the following Front Matter is added:

    ```
    ---
    layout: home
    ---
    ```

    Every page or posts must have Front Matter in order to add layout and other meta data needed in the page or post. Learn more about Front Matter from the [Jekyll Docs](https://jekyllrb.com/docs/front-matter/).
    
    I've added the following on `_config.yml` to list recent posts on the Home page:

    ```
    paginate: 5 # amount of posts to show
    paginate_path: /page:num/
    ```

    For other pages, I wrote the following content on `_data/navigation.yml` file to add them to the page menu/navigation:

    ```
    main:
      - title: "Home"
        url: /
      - title: "About"
        url: /about/
      - title: "Projects"
        url: /projects/
    ```

    The files for both `About` and `Projects` pages are in `_pages` folder, named `about.md` and `projects.md` respectively. [Permalinks](https://jekyllrb.com/docs/permalinks/) are also added in the Front Matter of each file matching the `url` value in the `_data/navigation.yml` file. For example, `about.md` has the following Front Matter:

    ```
    ---
    layout: single
    title: About Me
    permalink: /about/
    ---
    ```
    
    Finally, to make sure that these `_pages/` files are read/seen by Jekyll, I added the following line to `_config.yml`:

    ```
    include:
      - _pages
    ```

2. Blogs

    To add blog articles to the site, like this article, I simply add a new file with the format `yyyy-mm-dd-title-separated-by-dash.md` under the `_posts` directory. This is the usual format and directory for [posts](https://jekyllrb.com/docs/posts/) in Jekyll. Any setting or meta data are again configured in the Front Matter of each blog/article file. For example, this blog's Front Matter looks like the following:

    ```
    ---
    layout: single
    categories: [blog]
    permalink: /:categories/:year/:month/:day/:title/
    classes: wide
    toc: true
    ---
    ```

    For blog drafts, the files are put under the `_drafts` folder so jekyll won't publish them. And in order to preview these drafts in the local server, simply add `--drafts` to the jekyll command:

    ```
    bundle exec jekyll serve --drafts
    ```

    For all other settings/configuration, you can check it on this site's [_config.yml](https://github.com/ejayledesma/ejayledesma.github.io/blob/master/_config.yml)

3. RSS Feed and Favicon (just extra stuffs)

    My site is already up and has content. The first few things I noticed was a missing `feed.xml` when FEED link at the footer is clicked and also I wanted to have a custom icon for the website.

    To add the feed.xml file, I added `jekyll-feed` to the plugins list in `_config.yml` and `gem 'jekyll-feed'` in the `Gemfile`. Run `bundle install` and `bundle exec jekyll serve` locally to test if missing `feed.xml` is fixed.

    For the website icon, I created it using Microsoft's Paint :). I am not an art kinda guy so I created a simple icon via Paint. I made it a transparent png file using this [online png tool](https://onlinepngtools.com/create-transparent-png). With that transparent png file, I created the favicon via [https://realfavicongenerator.net/](https://realfavicongenerator.net/). I added the output `favicon.ico` file to the root folder of this site.

## Next Steps
I was able to set this site up just by reading documentations and guides and simply trying them out. GitHub Pages and Jekyll surely made this site easy to setup so go and try it yourself!

I personally used GitHub Pages and Jekyll because they are free. But if you want to add custom domain to your site, you can do so and follow the steps on this [documentation](https://help.github.com/en/articles/using-a-custom-domain-with-github-pages). There are other things you can do with GitHub Pages and Jekyll so make sure to read their documentations (see links below).

Also, it is important to test changes locally first before putting them online. Another useful thing to setup is to have [Continuous Integration](https://en.wikipedia.org/wiki/Continuous_integration) (CI) in your workflow to automatically test the changes that are pushed in your repository. I will be sharing how I setup CI on this site using [Travis CI](https://travis-ci.org/) on my next blog. Stay tuned!

Resources:

- [https://pages.github.com/](https://pages.github.com/)
- [https://jekyllrb.com/docs/](https://jekyllrb.com/docs/)
- [https://jekyllrb.com/docs/installation/windows/](https://jekyllrb.com/docs/installation/windows/)
- [https://pages.github.com/themes/](https://pages.github.com/themes/)
- [https://mmistakes.github.io/minimal-mistakes/docs/quick-start-guide/](https://mmistakes.github.io/minimal-mistakes/docs/quick-start-guide/)
- [https://jekyllthemes.io/](https://jekyllthemes.io/)
- [https://guides.github.com/features/mastering-markdown/](https://guides.github.com/features/mastering-markdown/)
- [https://help.github.com/en/articles/atom-rss-feeds-for-github-pages](https://help.github.com/en/articles/atom-rss-feeds-for-github-pages)
- [https://onlinepngtools.com/create-transparent-png](https://onlinepngtools.com/create-transparent-png)
- [https://realfavicongenerator.net/](https://realfavicongenerator.net/)
- [https://help.github.com/en/articles/using-a-custom-domain-with-github-pages](https://help.github.com/en/articles/using-a-custom-domain-with-github-pages)