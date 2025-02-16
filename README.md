# github-style (AC's Fork)

AC Forked github.com:MeiK2333/github-style.git because it hard-coded the top menu
and AC wanted to have "Photos" and "Posts" on the top menu.

Unfortunately, AC doesn't know his way around `go` well at all, and only slightly
more HTML/CSS, so it's been a bit of a hack job. It appears that this theme
could be further enhanced by enabling 
[Hugo Menus](https://gohugo.io/content-management/menus/).
Indeed, there's a lot of opportunity for improvement in the theme, but it's
conveneint, and it really is Github style -- right down to dark mode, which is
pretty cool.

## Specific Additions:
* Adding any new `section` (directly under 'content') should cause that section to show up in the top menu
* Adding content/photos adds 'photos' in the top menu (as above) but with a `gallery` icon
* Added attribution for gallery icon. Maybe I can find an attribution-free one in the future?
* If the post section is "photos" and the post is pinned and has a "cover" image path, it will get included in the overview card.

```
---
pin: true
cover: /path/to/cover_img.png
---
```

## Note
* Reminder to self to occassionally see if [MeiK2333](https://github.com/MeiK2333/github-style) has updated upstream and follow [this process](https://stackoverflow.com/questions/7244321/how-do-i-update-a-github-forked-repository#7244456) to rebase if so. Last checked 2021-03-17.

# Original Instructions
## Init hugo site

```bash
hugo new site mysite
cd mysite
```

## Install the theme

```bash
git submodule add git@github.com:MeiK2333/github-style.git themes/github-style
```

## Update the theme

If you just installed the theme, it is already in the latest version. If not, you can update using the below commands

```bash
cd themes/github-style
git pull
```

Then, you need to rename the previous `posts` folder to `post`

```bash
cd <you-project-folder>
mv content/posts content/post
```

## Setup readme

```bash
hugo new readme.md
echo '`Hello World!`' > content/readme.md
```

## Pin post

```
---
pin: true
---
```

## Add new post

Hugo will create a post with `draft: true`, change it to false in order for it to show in the website.

```
hugo new post/title_of_the_post.md
```

## Limit display content

### Approch 1: use summary

```
---
title: "title"
date: 2019-10-22T18:46:47+08:00
draft: false
summary: "The summary content"
---
```

### Approch 2: use `<!--more-->`

Use `<!--more-->` to seperate content that will display in the posts page as abstraction and the rest of the content. This is different from summary, as summary will not appear in the post.
```
---
title: "title"
date: 2019-10-22T18:46:47+08:00
draft: false
---
abstraction show in the post page
<!--more-->
other content
```

## add last modified data

add to `config.toml`

```toml
lastmod = true

[frontmatter]
  lastmod = ["lastmod", ":fileModTime", ":default"]
```

## Support LaTex

In you post add `math: true` to [front matter](https://gohugo.io/content-management/front-matter/)

```
---
katex: math
---
```

Then the [katex script](https://katex.org/docs/autorender.html) will auto render the string enclosed be delimiters.

```
# replace ... with latex formula
display inline \\( ... \\)
display block $$ ... $$
```

![latex example](images/latex_example.png)

## config.toml example

```toml
baseURL = "https://meik2333.com/"
languageCode = "zh-cn"
title = "MeiK's blog"
theme = "github-style"
googleAnalytics = "UA-123456-789"
pygmentsCodeFences = true
pygmentsUseClasses = true

[params]
  author = "MeiK"
  description = "In solitude, where we are least alone."
  github = "MeiK2333"
  facebook = "MeiK2333"
  twitter = "MeiK2333"
  linkedin = "MeiK2333"
  instagram = "MeiK2333"
  tumblr = "MeiK2333"
  email = "meik2333@gmail.com"
  url = "https://meik2333.com"
  keywords = "blog, google analytics"
  rss = true
  lastmod = true
  favicon = "/images/github.png"
  location = "China"

  [[params.links]]
    title = "Link"
    href = "https://github.com/meik2333"
  [[params.links]]
    title = "Link2"
    href = "https://meik2333.com"
    icon = "https://meik2333.com/images/avatar.png"

[frontmatter]
  lastmod = ["lastmod", ":fileModTime", ":default"]

```

## deploy.sh example

There are various way to deploy to github, here is a link to official [document](https://gohugo.io/hosting-and-deployment/hosting-on-github/).

Here is an sample. Note line 22 have `env HUGO_ENV="production"`, makes sure googleAnalysis is loaded during production, but is not loaded when we are testing it in localhost.

```bash
#!/bin/sh

if [ "`git status -s`" ]
then
    echo "The working directory is dirty. Please commit any pending changes."
    exit 1;
fi

echo "Deleting old publication"
rm -rf public
mkdir public
git worktree prune
rm -rf .git/worktrees/public/

echo "Checking out gh-pages branch into public"
git worktree add -B gh-pages public origin/gh-pages

echo "Removing existing files"
rm -rf public/*

echo "Generating site"
env HUGO_ENV="production" hugo -t github-style

echo "Updating gh-pages branch"
cd public && git add --all && git commit -m "Publishing to gh-pages (publish.sh)"

#echo "Pushing to github"
#git push --all
```

Then you can verify the site is working and use `git push --all` to push the change to github. If you don't want to check again every time, you can uncomment the `#git push --all` in the script.
