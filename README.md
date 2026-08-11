# Mitchell's Blog

A Jekyll blog designed for GitHub Pages.

## Publish with GitHub Pages

1. Push this repository to `mitchilling.github.io` on GitHub.
2. On GitHub, open **Settings → Pages**.
3. Under **Build and deployment**, select **Deploy from a branch** and choose
   `main` and `/(root)`.

GitHub Pages will build and publish the site at
`https://mitchilling.github.io`.

## Write a post

Create a Markdown file in `_posts/`, using this name format:

```text
YYYY-MM-DD-your-post-title.md
```

Start it with Jekyll front matter:

```md
---
layout: post
title: "Your post title"
date: 2026-08-11 09:00:00 +0800
---

Your post goes here.
```

## Publish in English and Chinese

Every translated pair uses the same `translation_key` and a language code. The
site shows a link to the other language automatically.

```md
---
layout: post
title: "Your English title"
date: 2026-08-11 09:00:00 +0800
lang: en
translation_key: a-short-unique-key
permalink: /posts/a-short-unique-key/
---
```

For its Chinese version, create a second post with `lang: zh`, the same
`translation_key`, and a Chinese URL:

```md
---
layout: post
title: "你的中文标题"
date: 2026-08-11 09:00:00 +0800
lang: zh
translation_key: a-short-unique-key
permalink: /zh/posts/a-short-unique-key/
---
```

English posts appear on `/`; Chinese posts appear on `/zh/`.

## Preview locally

The site requires Ruby 3.1 through 3.3. macOS includes an older system Ruby,
so install the supported Ruby 3.3 series with Homebrew first:

```sh
brew install ruby@3.3
echo 'export PATH="$(brew --prefix ruby@3.3)/bin:$PATH"' >> ~/.zshrc
exec zsh
```

Confirm that `ruby --version` reports Ruby 3.3, then install the project
dependencies and run the local server:

```sh
bundle install
bundle exec jekyll serve
```

Then visit `http://localhost:4000`.

## License

The site's source code is available under the [MIT License](LICENSE). Unless a
post says otherwise, original writing and visual content are available under
[CC BY 4.0](LICENSE-CONTENT.md).
