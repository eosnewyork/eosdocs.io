# EOSDocs.io

This repository contains the code that powers the [EOSDocs.io](https://www.eosdocs.io/) website. 

The PublicWebsite folder contains the [Hugo](https://gohugo.io/) project source code which outputs static HTML (which can then be hosted anywhere).

[Hugo](https://gohugo.io/) allows content authors to write content in markdown files (See the PublicWebsite\content folder). So if you're only interested in changing the content of a page \ tutorial, that is the best place to start.

The [Hugo Learn Theme](https://themes.gohugo.io/hugo-theme-learn/) in combination with the content to produce the final output. 

The [theme documentation](https://learn.netlify.com/en/) provides details on the usage of the theme. 

## Local Development

If you'd like to see the final rendered HTML preview of any change you're making, it's very easy to clone the project to your local hard drive and then run hugo to see the fully rendered site. 

In order to execute the hugo command you will need to have installed hugo on your local machine - this is a very simple process (instructions can be found [here](https://gohugo.io/getting-started/installing/))

```
$ git clone https://github.com/BinaryFocus/eosdocs.io.git
$ cd eosdocs.io/PublicWebsite/
$ hugo server -D
```

Note that the "hugo server -D" command is now runnin a mini web server that will preview the website at http://localhost:1313/ and will regenerate the HTML after any change to a file.

## Adding new pages / content

Adding pages can be done by simply doing the following 

```
$ cd eosdocs.io/PublicWebsite/
$ hugo new dAppDevelopment\newsubpage\index.md
```

This will create a new file "PublicWebsite\content\dAppDevelopment\newsubpage\index.md"

By default this page looks as follows. 

```
---
title: "Newsubpage"
date: 2018-04-24T08:43:24-04:00
draft: true
---
```

1. Update the title 
2. Update the draft status to false when you're ready to publish the page. Note that you'll only see these pages that are in a draft state when you're running the "hugo server -D" command as described above (the -D flag enables draft page visibility). 

Here's what you should end up with

```
---
title: "A New Sub Page Title"
date: 2018-04-24T08:43:24-04:00
draft: false
---

Some Markdown here

```
