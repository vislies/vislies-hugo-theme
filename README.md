# VisLies Theme for Hugo

This is a theme used for the VisLies web pages (https://www.vislies.org).
This readme contains instructions on creating a new page.

## Installing Hugo

This theme is designed to be used with the [Hugo] website building
framework. Hugo can be installed with package managers like Homebrow,
MacPorts, and many others. Binaries can also be downloaded from the [Hugo
website][Hugo].

## Creating a VisLies site for a new year

For purposes of illustration, let's say we are making a web page for
VisLies in the year 2165.[^2165]

The VisLies web page is set up such that each year's site is placed in a
subdirectory with that year. In this case, the page will be available at
https://www.vislies.org/2165/. This is achieved by creating a repository
named 2165 in the [vislies project in GitHub] and then hosted with GitHub
pages.

### Creating skeleton files

The first step for our hypothetical site for 2165 is to use [Hugo] to
create a new site named `2165`.

``` sh
hugo new site 2165
cd 2165
git init
```

Note that the steps above include initializing a git repo. This is
necessary for several of the subsequent steps.

### Linking this theme

With the skeleton of the site built, you need to link in this theme. This
is done by adding this repo to the new site as a submodule.

``` sh
git submodule add https://github.com/vislies/vislies-hugo-theme.git themes/vislies-hugo-theme
```

The theme contains a subdirectory named `example` with further files to
help setup the site. This is available in
`themes/vislies-hugo-theme/example`.

## Configuring

Copy the `config.toml` example file to your repo.

``` sh
cp themes/vislies-hugo-theme/example/config.toml .
```

It is OK to overwrite the existing `config.toml` created by Hugo. There are
some edits to make to the configuration.

  * `baseURL`: Update the URL to point to the correct year (e.g.,
    `https://www.vislies.org/2165/`).
  * `this_year`: Update this to the correct year (e.g., `2165`).
  * `sidebar_color`: Set this to a web color that will be used for the
    sidebar at the left. It is good to have a distinct color for each year.
  * `gallert_ready`: A boolean that controls whether a link to the gallery
    page is included in the menu. This starts out as `false` before the
    event. After the event, a gallery is created based on what was
    presented. When this is ready, change this to `true`.

## Initial content

The `example` directory also has some templates for the page contents.
These can be copied as a starting point. Pages in Hugo are created by
markdown files in the `content` directory.

``` sh
cp themes/vislies-hugo-theme/example/content/* content
```

The example content directory contains two files. The file named
`_index.md` contains the main landing page and the file `gallery.md` is
where the gallery will go. Start by editing `_index.md`.

Another important directory is `static` which contains files that will be
provided as-is. The theme example directory contains several images in a
`static` subdirectory that can be useful for your page.

## Previewing the page

Hugo makes it easy to preview the web page.

``` sh
hugo serve
```

## Making the page live

The VisLies pages are hosted using GitHub pages.

### Pushing to GitHub

To be hosted under the vislies.org domain, the repo has to first be pushed
to GitHub. Start by creating a repository named for the year (e.g., `2165`)
in the [vislies project in GitHub] (https://github.com/vislies). Then
follow the standard instructions for pushing your local repository there.

### Instructing GitHub to build the pages

Next, add a workflow to the repository that GitHub will run when commits
are pushed to the main branch. The workflow will run hugo to create the web
pages and push it to the gh-pages branch. The is done by adding YAML
configuration files in the `.github/workflows`. The example directory has
a [_github] directory that can be copied to the the repo and renamed
`.github` (i.e., `cp -r themes/vislies-hugo-theme/example/_github .github`).

The workflow that implements building the pages is typically named
`.github/workflows/gh-pages.yml`. The following [workflow builds Hugo].

``` yml
name: github pages

on:
  push:
    branches:
      - main  # Set a branch to deploy
  pull_request:

jobs:
  deploy:
    runs-on: ubuntu-22.04
    steps:
      - uses: actions/checkout@v3
        with:
          submodules: true  # Fetch Hugo themes (true OR recursive)
          fetch-depth: 0    # Fetch all history for .GitInfo and .Lastmod

      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v2
        with:
          hugo-version: 'latest'
          extended: true

      - name: Build
        run: hugo --minify

      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        if: github.ref == 'refs/heads/main'
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./public
```

After commits are pushed to a GitHub repository with these workflows in the
repo, GitHub will automatically run them. The status of these workflows can
be found by clicking the `Actions` tab.

This procedure comes from
https://gohugo.io/hosting-and-deployment/hosting-on-github/.

### Turning on GitHub Pages

Now that GitHub is building the web pages, the next step is to turn on the
GitHub pages to serve the content. First, click the `Settings` tab and then
click the `Pages` section. Keep the `Source` as `Deploy from a branch`. (The
workflow just created takes care of deployment.) Change the `Branch` to
`gh-pages`. (The `gh-pages` is created by the already defined workflow.)
Don't forget to `Save`.

Once the `Branch` is set, you will notice a new workflow started in the
`Actions` tab. One of the actions in the workflow is `deploy`, and that
will point to the web page created.

## Building the gallery

TBD


[Hugo]: https://gohugo.io/
[vislies project in GitHub]: https://github.com/vislies
[_github]: example/_github
[workflow builds Hugo]: example/_github/workflows/gh-pages.yml

[^2165]: I think it's safe to assume that VisLies will be around for at
    least that long.
