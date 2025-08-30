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
    - [Sitemap handling](#setting-sitemapHandling)
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

Clone the repository and run the included example content (requires Hugo, Go, and Git):

```bash
git clone https://github.com/foundata/hugo-component-robotstxt.git
cd hugo-component-robotstxt/exampleSite
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

Example:

```yaml
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

#### `excludeNonProduction`<a id="setting-excludeNonProduction"></a>

* **Default:** `true`
* When `true`, the template adds the following directive in non-production builds:
  ```
  User-agent: *
  Disallow: /
  ```
  Production detection is based on either:
  * `hugo.IsProduction`
  * `.Site.Params.env == "production"`


#### `exclude`<a id="setting-exclude"></a>

* **Type:** list of [path patterns](https://developers.google.com/search/docs/crawling-indexing/robots/robots_txt#url-matching-based-on-path-values).
* Each entry becomes a `Disallow:` rule for all .crawlers (`User-agent: *`).
* Example:
  ```yaml
  exclude:
    - "/download/"
    - "*.asc$"
  ```
  becomes:
  ```
  User-agent: *
  Disallow: /download/
  Disallow: *.asc$
  ```


#### `excludeCrawlers`<a id="setting-excludeCrawlers"></a>

* **Type:** list of crawler user-agent names.
* Each entry creates a crawler-specific block:
  ```yaml
  excludeCrawlers:
    - "ia_archiver"
    - "GPTBot"
  ```
  becomes:
  ```
  User-agent: ia_archiver
  Disallow: /

  User-agent: GPTBot
  Disallow: /
  ```

#### Sitemap handling<a id="setting-sitemapHandling"></a>

* By default Hugo generates `/sitemap.xml`.
* If disabled (`disableKinds = ["sitemap"]`) or if `sitemap.filename` is set to an empty string, no `Sitemap:` line is emitted.
* If a custom filename is set (e.g. `sitemap.filename = "mysite-map.xml"`), the generated `robots.txt` will correctly reference it.


## Compatibility<a id="compatibility"></a>

The component should be compatible with nearly every Hugo version and should always work with the latest Hugo release (we usually run the latest Hugo ourselves and fix issues promptly). It has been tested with:

- Hugo Extended v0.148.0
- Hugo Extended v0.148.2

If your version isn't listed (especially if it's older) feel free to try it. You may need to adjust [`config/_default/module.yaml`](./config/_default/module.yaml).



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
