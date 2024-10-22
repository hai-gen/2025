# HAI-GEN 2025

The 6th Workshop on Human-AI Co-Creation with Generative Models, held at ACM IUI 2025.

## Instructions for creating a new site for a new conference year

1. On GitHub, create a new repository for the new conference year (e.g. `2025`).

```
https://github.com/hai-gen/2025
```

2. Checkout the repo locally.

```sh
$ git clone git@github.com:hai-gen/2025.git
```

3. Install `ruby` and `jekyll` (if needed).

> On macOS, it seems the system installation of ruby is older and may not work with newer versions of jekyll. Follow the directions given in [this guide](https://jekyllrb.com/docs/installation/macos/). `ruby-install` will install a `ruby` runtime in `~/.rubies`.

```sh
$ brew install chruby ruby-install
$ ruby-install ruby 3.3.5
```

Next, add the new `ruby` runtime to your `$PATH`:

```sh
echo "source $(brew --prefix)/opt/chruby/share/chruby/chruby.sh" >> ~/.zshrc
echo "source $(brew --prefix)/opt/chruby/share/chruby/auto.sh" >> ~/.zshrc
echo "chruby ruby-3.3.5" >> ~/.zshrc # run 'chruby' to see actual version
```

4. Install `jekyll` and `bundler`.

```sh
$ cd 2025
$ gem install jekyll bundler
```

> Note: `gem install` will install `jekyll` and `bundle` in `~/.gem` (i.e. at the user level).

Next, tell `bundler` to place dependnecies in `vendor/bundle`.

```sh
$ bundle config set --local path 'vendor/bundle'
$ bundle install
```

5. Create a new Jekyll project (note: this is only if you want to start from a clean repo state, such as in the case when we need to update dependencies like `bulma-clean-theme`).

```sh
$ jekyll new . --force
```

> 🤷‍♂️ Is `gem install` hanging? You may need to change your IPv6 settings as per [this blog post](https://rob.co.bb/posts/2018-10-22-yak-shave-gem-install-issue/).

6. Use `npm` to manage running the project locally.

```sh
$ npm init
<set up configuration>
```

Edit `package.json` to include the following line under `scripts`:

```json
"scripts": {
    ...
    "prestart": "bundle config set --local path 'vendor/bundle' && bundle install",
    "start": "bundle exec jekyll serve --livereload",
    ...
}
```

Here is a full example of `package.json`:

```json
{
  "name": "hai-gen2025",
  "version": "1.0.0",
  "description": "The 6th Workshop on Human-AI Co-Creation with Generative Models, held at ACM IUI 2025.",
  "main": "index.js",
  "scripts": {
    "prestart": "bundle config set --local path 'vendor/bundle' && bundle install",
    "start": "bundle exec jekyll serve --livereload",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "repository": {
    "type": "git",
    "url": "git+https://github.com/hai-gen/2025.git"
  },
  "author": "Justin Weisz <jweisz@us.ibm.com>",
  "license": "MIT",
  "bugs": {
    "url": "https://github.com/hai-gen/2025/issues"
  },
  "homepage": "https://github.com/hai-gen/2025#readme"
}
```

7. Set up [GitHub Pages](https://github.com/hai-gen/2025/settings/pages) using GitHub Actions. There will be a default action recommended for Jekyll. It seems fine to use as is.

8. View the site: [https://hai-gen.github.io/2025](https://hai-gen.github.io/2025)

9. Once you're happy, make the base `https://hai-gen.github.io` redirect to the current conference year by editing `index.html` in the `hai-gen.github.io` repository.


## How to run this site locally

1. Clone the repo locally if you haven't done so already.

```sh
$ git clone git@github.com:hai-gen/2025.git
```

2. Run `npm start`, which will install dependencies and run the project. The URL to access the site will be shown in the console:

```
    Server address: http://127.0.0.1:4000/
    Server running... press ctrl-c to stop.
```

### Error with `eventmachine`?

On macOS, you may get an error with `eventmachine` not being able to load `libcrypto.1.1.dylib`. To fix this:

1. Make sure `openssl` is installed: `brew install openssl`
2. Tell bundle to use openssl: `bundle config build.eventmachine --with-openssl-dir=/usr/local/opt/openssl@1.1`
3. Run `npm start` and it should work.
4. Failing that, make a symlink of `libcrypto.1.1.dylib` in the `eventmachine` vendor dir:

```
% cd vendor/bundle/ruby/2.6.0/gems/eventmachine-1.2.7/lib
% ln -s /usr/local/opt/openssl@1.1/lib/libcrypto.1.1.dylib .
```

## What to edit when starting from scratch

Starting from scratch? Here are some files you will need to add or modify.

### Gemfile

Make sure the right theme is loaded (i.e. `bulma-clean-theme`).

```ruby
#gem "minima", "~> 2.5"
gem "bulma-clean-theme"
```

### _config.yml

Make sure (at least) these keys are defined in the `_config.yml` file.

```yml
title: HAI-GEN 2025
tagline: IUI Workshop on Human-AI Co-Creation with Generative Models
email: jweisz@us.ibm.com
description: >- # this means to ignore newlines until "baseurl:"
  6th Workshop on Human-AI Co-Creation with Generative Models
baseurl: "" # the subpath of your site, e.g. /blog
url: "https://hai-gen.github.io/2025" # the base hostname & protocol for your site, e.g. http://example.com
permalink: pretty
favicon: favicon.png

# Build settings
theme: bulma-clean-theme
plugins:
  - jekyll-feed
  - kramdown

livereload: true

# ignore depreciation warnings
sass:
  quiet_deps: true
```

### assets/css/app.scss

Check the [Bulma documentation](https://bulma.io/documentation/) and the [Bulma Clean Theme documentation](https://www.csrhymes.com/bulma-clean-theme/docs/) for more information on their use of CSS. At the least, make the following definitions. And yes, we need the `---\n---` at the top of the file, otherwise the CSS somehow doesn't propagate correctly into the HTML pages (although VSCode throws an error, it's safe to ignore).

```css
---
---

// set the primary color to black; this is used for the top navbar
// keep this the same every year
$primary: #000000;

// set the text color of navbar items to be white so it's readable
$navbar-item-color: #FFFFFF;
$navbar-background-color: #222222;

// Import Main CSS file from theme
@import "main";

// override the text color on the hero image so it's readable
.hero.is-primary .title, .hero.is-primary .subtitle {
    color: rgb(255, 255, 255);
}

// maintain spacing between organizer blocks
.columns:not(:last-child) {
    margin-bottom: 2rem;
}
```

### _data/navigation.yml

This file makes the navbar appear on the top of the site.

```yml
- name: Program
  link: /program/
- name: Organizers
  link: /organizers/
- name: Call for Participation
  link: /cfp/
```

> Make sure you have corresponding `program.html`, `organizers.markdown`, and `cfp.markdown` files. For some reason, `jekyll` wasn't happy with `.md` file suffixes, so we use `.markdown` now.

### _include & _layouts

Want to override the display of certain pages? Take a look inside `vendor/bundle/ruby/3.3.0/gems/bulma-clean-theme-1.1.0/_includes` (or `_layouts`), which has the html templates used to build out different kinds of pages. To override any of these, *copy* the file into `_includes` (or `_layouts`) at the root level of the project and make the edits there.

For example, to remove the "Home" link from the top navbar, copy `header.html` into `_includes` and comment out the following line, as so:

```html
<!-- <a href="{{ site.baseurl }}/" class="navbar-item {% if page.url == "/" %}is-active{% endif %}">Home</a> -->
```

Jekyll will use anything defined locally in `_includes` and `_layouts` to override files included in the bulma package.

### _layouts/default.html

Override this file and make the sidebar include `key-dates.html`.

Change this:

```html
{% include latest-posts.html %}
```

To this:

```html
{% include key-dates.html %}
```

### Set the hero image

Include this snippet in the metadata for each page (assuming `img/hero.png` is the path to the hero image).

```
hero_image: img/hero.png
```