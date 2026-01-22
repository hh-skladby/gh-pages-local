# Maintain GitHub Pages locally

[GitHub Pages](https://docs.github.com/en/pages/quickstart) are a straighforward way to present contents of GitHub repos in a more convenient way, e.g. for a structured project presentation instead of the default repository view. To maintain them locally you need [Jekyll](https://jekyllrb.com/), the site generator behind Pages, with some specific plugins.

The following shows an easy and minimal way (no [Actions](https://github.com/marketplace?query=jekyll&type=actions), no [Gemfile](https://bundler.io/man/gemfile.5.html) per repo, no deeper [Jekyll configuration](https://jekyllrb.com/docs/configuration/) ) to add GitHub Pages functionality to a local repo.


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

> The `title` field is technically only necessary if you would change to a theme (see below) or add a plugin requiring it; if not defined, GitHub Pages uses the `jekyll-github-metadata` plugin to retrieve it from the repo's name on GitHub that of course is not immediately available locally, but quite naturally you will sooner or later prefer a nicer title than the repo's folder name - see `jekyll-github-metadata` itself that presents itself not by its kebab cased repo folder name "github-metadata", but as [GitHub Metadata](https://jekyll.github.io/github-metadata/) (see the `title` setting in [its own _config.yml](https://github.com/jekyll/github-metadata/blob/main/docs/_config.yml)).

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

... NOT per repo

... Both Jekyll ([Docs: "GitHub Pages"](https://jekyllrb.com/docs/github-pages/)) and GitHub ([Setting up a GitHub Pages site with Jekyll](https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll)) have rich setup instructions, but these are targeting 



, including "automation" by the ..., but a huge problem is that Jekyll as a standalone project and Jekyll as an integrated part of GitHub have diverted, making local setups not working as expected.


...

... Jekyll version and dependencies see also e.g. [https://github.com/hh-lohmann/26amj38/actions/runs/21244691252/job/61130907235]()


## Jekyll plugins

...

... https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/about-github-pages-and-jekyll#plugins

... [dependencies of GitHub Pages Ruby Gem](https://github.com/github/pages-gem/blob/master/lib/github-pages/dependencies.rb) hints to required plugins + configs [https://github.com/github/pages-gem/blob/master/lib/github-pages/configuration.rb]()

* https://github.com/benbalter/jekyll-optional-front-matter
* https://github.com/benbalter/jekyll-readme-index
* https://github.com/benbalter/jekyll-relative-links


## Syntax highlighting

... may differ


## Using other theme

... jekyll-remote-theme

... Metadata plugin problem


