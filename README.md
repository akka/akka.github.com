# akka.io

This repository contains the source of the [akka.io](http://akka.io) website. It's built using Jekyll.

These instructions are mostly intended for akka committers who would like to update the site (i.e., adding a news item).

## Dependencies

Install the needed ruby stuff using bundler:

    bundle install

If in doubt, head over to the github pages-page for instructions: 
https://help.github.com/articles/setting-up-your-github-pages-site-locally-with-jekyll/#step-4-build-your-local-jekyll-site

Github lists their versions of Jekyll etc. here: https://pages.github.com/versions/

## Building

After cloning, cd into the `akka/akka.github.com` directory and run:

    bundle exec jekyll serve --baseurl ''

To see the generated site, just visit `http://localhost:4000`.

## Adding a News Item

News Items must be:

1. written in [Markdown](http://daringfireball.net/projects/markdown/syntax)
2. named according to the following convention: `YYYY-MM-dd-your-title.md`, where of course `YYYY` is the 4-digit year, `MM` is the 2-digit month, and `dd` is the 2-digit day of the month.
3. placed into the `news/_posts/` directory
4. contain YAML front matter

The YAML front matter for news items can come in one of three flavors.

For a standard news item that contains a significant body of text, the following YAML front matter will suffice:

    ---
    layout: news
    title: Your Title
    author: Your Name
    ---

    Write your news article here, and make sure it's in markdown!

In this case, the first 30 or so words will be taken from the body of the news item and displayed on the front page of akka.io.

However, if you'd like to override the text that's displayed, you could use the following YAML front matter:

    ---
    layout: news
    title: Your Title
    author: Your Name
    short: A short description should be written here. This is what's rendered on the front page.
    ---

    Write your news article here, and make sure it's in markdown!

Here, whatever text is written in the `short` field of the front matter will be displayed on the front page of akka.io, underneath the title of the news item.

For a news item which points to another article outside of akka.io, the following YAML front matter will suffice:

    ---
    layout: news
    title: Your Title
    author: Your Name

    short: A short description should be written here. This is what's rendered on the front page.
    link-out: http://link-to-whatever-site-has-the-article.com
    ---

In this case, the text in the `short` field will be what is displayed on the front page of akka.io. The URL in the `link-out` field will be what the title of the article links to on the front page of akka.io. There should be no body below the YAML front matter for these news articles.



