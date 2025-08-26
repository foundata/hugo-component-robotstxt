# Hugo theme component: `hugo-component-robots-txt`

A reusable [theme component](https://gohugo.io/hugo-modules/theme-components/) to manage and generate the site's [robots.txt](https://developers.google.com/search/docs/crawling-indexing/robots/intro).


## Features

* Automatically excludes all bots and crawlers in non-production environments by default.
* Sane and useful `Disallow:` defaults.
* Supports crawler-specific blocking and per-path exclusions.
* Automatically manages sitemap references:
  * If a sitemap is enabled (default: `sitemap.xml`), its URL is added to `robots.txt`.
  * If a sitemap is disabled or renamed, the reference is updated or omitted accordingly.


## Installation

This component uses [Hugo Modules](https://gohugo.io/hugo-modules/use-modules/). Simply import `golang.foundata.com/hugo-component-robots-txt`.


## Configuration

```yaml
params:
  robotsTxt:
    excludeNonProduction: true
    exclude:
      - "/.git/*"
      - "/.well-known/*"
      - "*.asc$"
    excludeCrawlers:
      - "ia_archiver"
```


### `excludeNonProduction`

* **Default:** `true`
* When `true`, the template adds the following directive in non-production builds:
  ```
  User-agent: *
  Disallow: /
  ```
  Production detection is based on either:
  * `hugo.IsProduction`
  * `.Site.Params.env == "production"`


### `exclude`

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

💡 For pages that should be excluded from both `robots.txt` **and** the sitemap, add `sitemapExclude: true` in the page's front matter.


### `excludeCrawlers`

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

### Sitemap handling

* By default Hugo generates `/sitemap.xml`.
* If disabled (`disableKinds = ["sitemap"]`) or if `sitemap.filename` is set to an empty string, no `Sitemap:` line is emitted.
* If a custom filename is set (e.g. `sitemap.filename = "mysite-map.xml"`), the generated `robots.txt` will correctly reference it.


## Licensing, copyright<a id="licensing-copyright"></a>

<!--REUSE-IgnoreStart-->
Copyright (c) 2025 foundata GmbH (https://foundata.com)

This project is licensed under the GNU General Public License v3.0 or later (SPDX-License-Identifier: `GPL-3.0-or-later`), see [`LICENSES/GPL-3.0-or-later.txt`](LICENSES/GPL-3.0-or-later.txt) for the full text.

The [`REUSE.toml`](REUSE.toml) file provides detailed licensing and copyright information in a human- and machine-readable format. This includes parts that may be subject to different licensing or usage terms, such as third-party components. The repository conforms to the [REUSE specification](https://reuse.software/spec/). You can use [`reuse spdx`](https://reuse.readthedocs.io/en/latest/readme.html#cli) to create a [SPDX software bill of materials (SBOM)](https://en.wikipedia.org/wiki/Software_Package_Data_Exchange).
<!--REUSE-IgnoreEnd-->

[![REUSE status](https://api.reuse.software/badge/github.com/foundata/hugo-component-robots-txt)](https://api.reuse.software/info/github.com/foundata/hugo-component-robots-txt)


## Author information<a id="author-information"></a>

This project was created and is maintained by [foundata](https://foundata.com/).
