# Maintain GitHub Pages locally

[GitHub Pages](https://docs.github.com/en/pages/quickstart) are a straighforward way to present contents of GitHub repos in a more convenient way, e.g. for a structured project presentation instead of the default repository view. To maintain them locally you need [Jekyll](https://jekyllrb.com/), the site generator behind Pages, with some specific plugins.

The following shows an easy and minimal way (no [Actions](https://github.com/marketplace?query=jekyll&type=actions), no [Gemfile](https://bundler.io/man/gemfile.5.html) per repo, no deeper [Jekyll configuration](https://jekyllrb.com/docs/configuration/)) to add GitHub Pages functionality to a local repo.


## Repo settings on GitHub

If not already running, go in your repo to "Settings" > "Pages" and set "Source" to **Deploy from a branch** ("Classic Pages experience").

```
  /* URL for "Settings" > "Pages" in your-account/your-repo */
  https://github.com/your-account/your-repo/settings/pages
```

Use the "Actions" tab in your repo to see page builds and deployments - even though you did not choose "GitHub Actions" as "Source", GitHub uses actions to "deploy from a branch", i.e. default actions.

```
  /* URL for "Actions" in your-account/your-repo */
  https://github.com/your-account/your-repo/actions
```

When the last "page build and deployment" action has finished successfully, the GitHub Pages view of your repo is available on the GitHub special domain `github.io` e.g. as

```
  /* URL for ... */
  https://your-account.github.io/your-repo
```

Our goal here is to achieve a local preview for what GitHub Pages would render after pushing changes in your local repo.



## Local repo settings


### Build settings

Required settings in your local repo boil down to a `_config.yml` file with the following content:

```yaml
  title: Your Title

  plugins:
    - jekyll-default-layout
    - jekyll-optional-front-matter
    - jekyll-readme-index
    - jekyll-relative-links
    - jekyll-remote-theme

  remote_theme: pages-themes/primer

  include:
    # - README.md
    # - LICENSE
    # - LICENCE
    # - COPYING
    # - CODE_OF_CONDUCT
    # - CONTRIBUTING
    # - ISSUE_TEMPLATE
    # - PULL_REQUEST_TEMPLATE
```

> The `title` field is technically only necessary if you would change to a theme (see below) or add a plugin requiring it; if not defined, GitHub Pages uses the `jekyll-github-metadata` plugin to retrieve it from the repo's name on GitHub that of course is not directly available locally, but quite naturally you will sooner or later prefer a nicer title than the repo's folder name - see `jekyll-github-metadata` itself that presents itself not by its kebab cased repo folder name "github-metadata", but as [GitHub Metadata](https://jekyll.github.io/github-metadata/) (see the `title` setting in [its own _config.yml](https://github.com/jekyll/github-metadata/blob/main/docs/_config.yml)).

`_config.yml` is in YAML format, the `#` comment marker "deactivates" every line it precedes. The list under `include` is the list of files that the `jekyll-optional-front-matter` plugin (see below) will ignore by default - if you e.g. want your `README.md` file to be rendered as `../your-repo/readme.html`, you have to delete the `#` before `- README.md`. Note that being "ignored" does not mean that the file would not exist in a GitHub Pages output, it would just not be transformed to an HTML file. So explicitly including `README.md` would give you `../your-repo/README.html` **and** `../your-repo/README.md`, "ignoring" it **only** `../your-repo/README.md` - this might play a role for links inside of your files and what you expect them to link to. Note also that `README.md` as such is used by another plugin here, `jekyll-readme-index`, to act as input for `../your-repo/index.html` if no dedicated `index.md` is given in your repo.

The settings given as above (except of `title`, see above) reflect defaults on GitHub Pages (see below), i.e. they would not change anything on GitHub, but they are required to make your local Pages preview look like on GitHub. **If you delete one or more `#` under `include` you MUST push `_config.yml` with your repo to let GitHub Pages reflect your choices** - and of course if you add more settings.

  * `_config.yml` is not a special GitHub Pages file, but the "normal" [configuration file for Jekyll](https://jekyllrb.com/docs/configuration/) to build HTML files. Be aware of the "_" prefix, without it Jekyll would not recognize it. **Note that the `include` section is not special to the `jekyll-optional-front-matter` plugin** - the plugin makes use of it, but it is a general [Jekyll configuration option](https://jekyllrb.com/docs/configuration/options/).


### .gitignore

You should add these files and folders that are only relevant for build processes to your [`.gitignore`](https://git-scm.com/docs/gitignore#_description) file:

```perl
  # Jekyll build temporary
  .jekyll-cache/
  .jekyll-metadata/
  _site

  # Sass CSS preprocessor - used in HTML themes
  .sass-cache/
```


## Local Jekyll setup

> **If you already have installed Jekyll, check if it is the version given below, otherwise uninstall your version or find a way for different environments with separate Jekyll installations.**

Both Jekyll ([Docs: "GitHub Pages"](https://jekyllrb.com/docs/github-pages/)) and GitHub ([Setting up a GitHub Pages site with Jekyll](https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll)) have rich setup instructions, but they don't tell you that out of the box Jekyll will be installed in version 4.x while GitHub Pages sticks to the latest 3.x version (obviously to keep tons of older GitHub Pages alive without individual migrations). This may be no problem for complex and sophisticated Jekyll projects, especially with own [GitHub Actions](https://jekyllrb.com/docs/continuous-integration/github-actions/#advantages-of-using-actions) and [Gemfiles](https://jekyllrb.com/docs/step-by-step/10-deployment/#gemfile), but if you want to keep things small and convenient and therefore prefer to rely on defaults, you may go the following steps. **Of course this installation has to be done only once, not for each repo you want to use it in**.

### Prerequisites

Jekyll is a [Ruby](https://en.wikipedia.org/wiki/Ruby_(programming_language)) application, so **you need a Ruby runtime installation** to use it (you may think of Ruby as something like what [Node.js](https://nodejs.org/) is for JavaScript).
  * Try
      ```shell
        ruby -v
      ```
    on a command line to see if Ruby is installed (chances are good on Linux and macOS to be pre-installed). If not, check the possibilties on [Installing Ruby](https://www.ruby-lang.org/en/documentation/installation) for the one that fits your system (and your level of experience)

With Ruby should come **its package manager [RubyGems](https://guides.rubygems.org/)** that allows you to install Ruby application packages (you may think of RubyGems as something like what [npm](https://npmjs.org) is for Node.js). A Ruby application package is called a `gem`, and that is also the name of the command to install a gem.
  * Try
      ```shell
        gem -v
      ```
    on a command line to confirm that RubyGems is installed. If not, refer to [its documentation](https://github.com/ruby/rubygems?tab=readme-ov-file#installation) to solve this.


### Installing Jekyll

To be able to mimic GitHub Pages defaults locally you need the exact same Jekyll version as used by GitHub - thankfully documented in the publicly available [GitHub Pages settings JSON](https://pages.github.com/versions.json), currently `3.10.0`, so the command line is

  ```shell
    gem install jekyll -v 3.10.0
  ```

**Note that newer Jekyll versions are not suitable for the plugins used by GitHub Pages** (see below).

See the "Build with Jekyll" step in [this actually ran GitHub action](https://github.com/jekyll/github-metadata/actions/runs/19973767489/job/57284696390) for the mentioned GitHub Page settings in action.



## Jekyll plugins

> **If you have already Jekyll plugins installed you may have to replace it by older versions to make them work with the required GitHub Pages plugins, e.g. `gem uninstall plugin_xy` and `gem install plugin_xy@v1.2.3`** - in case read possible error massages carefully and consult the plugin's documentation

The correct Jekyll version (see above) allows to run exactly the plugins [that are used by GitHub Pages](https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/about-github-pages-and-jekyll#plugins) of which the ones stated in the `_config.yml` example above are important locally:

#### [jekyll-default-layout](https://github.com/benbalter/jekyll-default-layout)

Makes explicit `_config.yml` settings for layout templates unnecessary by applying well known standards

Install it by

```shell
  gem install jekyll-default-layout
```

#### [jekyll-optional-front-matter](https://github.com/benbalter/jekyll-optional-front-matter)

Makes [Front Matters](https://jekyllrb.com/docs/front-matter/) for Markdown files unnecessary.

Install it by

```shell
  gem install jekyll-optional-front-matter
```

#### [jekyll-readme-index](https://github.com/benbalter/jekyll-readme-index)

Makes [an explicit index.md](https://jekyllrb.com/tutorials/convert-site-to-jekyll/#what-is-a-jekyll-website) unnecessary by using a README.md to produce an `index.html`

Install it by

```shell
  gem install jekyll-readme-index
```

#### [jekyll-relative-links](https://github.com/benbalter/jekyll-relative-links)

Makes links to Markdown files to links to the rendered HTML version of the target, i.e. preventing `see [details](more.md)` to become `see <a href="more.md">details</a>` that would open the unrendered Markdown file `more.md`, but instead `see <a href="more.html">details</a>` that opens the rendered HTML file `more.html`

Install it by

```shell
  gem install jekyll-relative-links
```

#### [jekyll-remote-theme](https://github.com/benbalter/jekyll-remote-theme)

... jekyll-theme-primer

Makes ...

Install it by

```shell
  gem install jekyll-remote-theme
```




## Syntax highlighting

GitHub Pages uses version 3.30 of [Rouge](https://rouge.jneen.net/) for syntax highlighting in code examples (see above for GitHub Pages settings JSON).

Try

  ```shell
    gem list rouge
  ```

on a command line to confirm that you have the right version installed, otherwise install it with


  ```shell
    gem install rouge -v 3.30.0
  ```

**Uninstall other version of Rouge, especially newer ones, by `gem uninstall rouge` (follow the interactive diaglogues to uninstall specific versions)**


## Using other theme

... jekyll-remote-theme

... Metadata plugin problem


## Running local GitHub Pages preview

... jekyll serve -l


## Repo view vs. Pages View

The rendering of markup files inside the repo view on GitHub is ["aggressively removing things that could harm you and your kin"](https://github.com/github/markup?tab=readme-ov-file#github-markup) - unfortunately including "style" attributes that would allow produce whitespace at the end of the page to adjust the display when jumping to links at the end of the page. But on GitHub Pages there is no stripping off, so targeting at Pages you can use styles.

As an escape hatch for stripped display in repo view you can add a hint to switch to the Pages version:

```html
  <p align="center" style="display:none;">
  <b><i>This page may be displayed less optimal in repo view - you may switch to <a href="https://YOUR-NAME.github.io/REPO/">the GitHub Pages view</a> instead</i></b>
  </p>
```

This hint will be displayed in repo view since `style="display:none;"` is stripped off, but not in Pages view where styles are kept.

