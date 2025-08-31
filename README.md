# Hugo theme component: hugo-component-robotstxt (manage robots.txt)

A reusable [theme component](https://gohugo.io/hugo-modules/theme-components/) to manage and generate the site's [`robots.txt`](https://developers.google.com/search/docs/crawling-indexing/robots/intro).


## Table of contents

- [Features](#features)
- [Demo](#demo)
- [Installation](#installation)
  - [Using Hugo modules](#installation-hugo-modules)
  - [Using Git submodules](#installation-git-submodules)
- [Configuration](#configuration)
  - [Settings](#settings)
    - [`excludeNonProduction`](#setting-excludeNonProduction)
    - [`exclude`](#setting-exclude)
    - [`excludeCrawlers`](#setting-excludeCrawlers)
  - [Sitemap handling](#sitemap-handling)
- [Compatibility](#compatibility)
- [Contributing](#contributing)
- [Licensing, copyright](#licensing-copyright)
- [Author information](#author-information)


## Features<a id="features"></a>

* Automatically excludes all bots and crawlers in non-production environments by default.
* Sane and useful `Disallow:` defaults.
* Supports crawler-specific blocking and per-path exclusions.
* Automatically manages sitemap references:
  * If a sitemap is enabled (default: `sitemap.xml`), its URL is added to `robots.txt`.
  * If a sitemap is disabled or renamed, the reference is updated or omitted accordingly.


## Demo<a id="demo"></a>

Clone the repository and run the included [example content](./exampleSite/content/) (requires Hugo, Go, and Git):

```bash
git clone https://github.com/foundata/hugo-component-robotstxt.git
cd ./hugo-component-robotstxt/exampleSite
HUGO_MODULE_WORKSPACE=hugo.work hugo server --ignoreVendorPaths "**"
```

Or look at the following pages using this theme component:

* https://foundata.com/robots.txt
* https://golang.foundata.com/robots.txt


## Installation<a id="installation"></a>

### Using Hugo modules<a id="installation-hugo-modules"></a>

Add the following module path(s) to your [`theme:` configuration](https://gohugo.io/hugo-modules/theme-components/):

```yaml
theme:
  - "golang.foundata.com/hugo-component-robotstxt"
```

Hugo automatically fetches and import theme module paths as Go/Hugo modules, so you do **not** need to list them under `module.imports` manually. Using modules requires [Hugo, Go, and Git](https://gohugo.io/hugo-modules/use-modules/#prerequisite) to be installed on your system.


### Using Git submodules<a id="installation-git-submodules"></a>

From the root directory of your Hugo site, initialize a new Git repository (if you haven't already), then add the theme as a [Git submodule](https://git-scm.com/book/en/v2/Git-Tools-Submodules):

```bash
git submodule add https://github.com/foundata/hugo-component-robotstxt.git themes/robotstxt
```

Now reference the theme directory name in your [`theme:` configuration](https://gohugo.io/hugo-modules/theme-components/):

```yaml
theme:
  - "robotstxt"
```

## Configuration<a id="configuration"></a>

> ℹ️ **Heads-up:** You have to set [`enableRobotsTXT: true`](https://gohugo.io/configuration/all/#enablerobotstxt) (which is `false` by default) and make sure `robotstxt` is *not* listed at [`disableKinds`](https://gohugo.io/configuration/all/#disablekinds) (which should be OK by default). Otherwise, no `robots.txt` will be created.


Example:

```yaml
# Enable generation of robots.txt file.
enableRobotsTXT: true

params:
  robotsTxt:
    # Block all user agents ("Disallow: /") in non-production environments.
    excludeNonProduction: true
    exclude:
      # Version control
      - "/.git/"
      # System and metadata dirs
      - "/.well-known/"
      # Log and temp files
      - "/*.log$"
      - "/*.tmp$"
      - "/*.bak$"
    excludeCrawlers:
      - "GPTBot" # OpenAI / ChatGPT indexing
      - "ChatGPT-User" # OpenAI / ChatGPT plugins, used for direct actions in the name of a ChatGPT user

```


### Settings<a id="settings"></a>

This section documents the theme options you can place under `params.robotsTxt` in your Hugo configuration. The example configurations and are safe to copy-paste. All keys are optional and the theme falls back to sensible behavior unless otherwise noted.


#### `excludeNonProduction`<a id="setting-excludeNonProduction"></a>

- Type: Boolean.
- Default: `true`
- Purpose: When `true`, the template adds the following directive in non-production builds:
  ```
  User-agent: *
  Disallow: /
  ```
  Production detection is based on either:
  * `hugo.IsProduction`
  * `.Site.Params.env == "production"`
- **Example (config):**
  ```yaml
  params:
    robotsTxt:
      excludeNonProduction: true
  ```


#### `exclude`<a id="setting-exclude"></a>

- Type: List of strings.
- Default: `["/.git/", "/*.log$", "/*.tmp$", "/*.bak$", "/.well-known/"]`
- Purpose:
  - List of [path patterns](https://developers.google.com/search/docs/crawling-indexing/robots/robots_txt#url-matching-based-on-path-values).
  - Each entry becomes a `Disallow:` rule for all .crawlers (`User-agent: *`).
- **Example (config):**
  ```yaml
  params:
    robotsTxt:
      exclude:
        - "/download/"
        - "*.asc$"
  ```
  becomes the following in `robots.txt`:
  ```
  User-agent: *
  Disallow: /download/
  Disallow: *.asc$
  ```


#### `excludeCrawlers`<a id="setting-excludeCrawlers"></a>

- Type: List of strings.
- Default: `[]` (empty list)
- Purpose:
  - List of crawler user-agent names to exclude. Most companies provide some kind of list, e.g.:
    - https://developers.google.com/search/docs/crawling-indexing/google-common-crawlers
    - https://platform.openai.com/docs/bots/overview-of-openai-crawlers%23.eps
  - Reminder: `robots.txt` is an *advisory* mechanism. It prevents compliant crawlers from fetching URLs, but does not protect sensitive files from  direct access.
- **Example (config):** Each entry creates a crawler-specific block:
  ```yaml
  params:
    robotsTxt:
      excludeCrawlers:
        - "ia_archiver"
        - "GPTBot"
  ```
  becomes the following in `robots.txt`:
  ```
  User-agent: ia_archiver
  Disallow: /

  User-agent: GPTBot
  Disallow: /
  ```


### Sitemap handling<a id="sitemap-handling"></a>

There is nothing to configure. But the component is aware of [Hugo's sitemap configuration](https://gohugo.io/configuration/sitemap/):

* By default Hugo generates the Sitemap as `/sitemap.xml`.
* If disabled (`disableKinds = ["sitemap"]`) or if `sitemap.filename` is set to an empty string, no `Sitemap:` line is emitted.
* If a custom filename is set (e.g. `sitemap.filename = "mysite-map.xml"`), the generated `robots.txt` will correctly reference it.


## Compatibility<a id="compatibility"></a>

This project is compatible with Hugo (extended) ≥ v0.148.0 and should always work with the latest Hugo release (we usually run the latest Hugo ourselves and fix issues promptly). It has been tested at least with:

- [Hugo extended v0.149.0](https://github.com/gohugoio/hugo/releases/tag/v0.149.0)
- [Hugo extended v0.148.0](https://github.com/gohugoio/hugo/releases/tag/v0.148.0)

If your version isn't listed, it might still work. Just give it a try.


## Contributing<a id="contributing"></a>

See [`CONTRIBUTING.md`](./CONTRIBUTING.md) if you want to get involved.

This projects's functionality is mature, so there might be little activity on the repository in the future. Don't get fooled by this, the project is under active maintenance and used daily by the maintainers.


## Licensing, copyright<a id="licensing-copyright"></a>

<!--REUSE-IgnoreStart-->
Copyright (c) 2025 foundata GmbH (https://foundata.com)

This project is licensed under the GNU General Public License v3.0 or later (SPDX-License-Identifier: `GPL-3.0-or-later`), see [`LICENSES/GPL-3.0-or-later.txt`](LICENSES/GPL-3.0-or-later.txt) for the full text.

The [`REUSE.toml`](REUSE.toml) file provides detailed licensing and copyright information in a human- and machine-readable format. This includes parts that may be subject to different licensing or usage terms, such as third-party components. The repository conforms to the [REUSE specification](https://reuse.software/spec/). You can use [`reuse spdx`](https://reuse.readthedocs.io/en/latest/readme.html#cli) to create a [SPDX software bill of materials (SBOM)](https://en.wikipedia.org/wiki/Software_Package_Data_Exchange).
<!--REUSE-IgnoreEnd-->

[![REUSE status](https://api.reuse.software/badge/github.com/foundata/hugo-component-robotstxt)](https://api.reuse.software/info/github.com/foundata/hugo-component-robotstxt)


## Author information<a id="author-information"></a>

This project was created and is maintained by [foundata](https://foundata.com/).
