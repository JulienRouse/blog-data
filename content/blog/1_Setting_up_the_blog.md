---
title: 'Setting up Hugo, GithubPage and CircleCI to launch a blog'
url: 'setting_up_hugo_githubpage_and_circleci_to_launch_a_blog'
date: Sun, 26 May 2019 14:54:04 +0000
draft: true
tags: [hugo,circleci,blog,how-to,github]
featured: true
toc: true
summary: "How Hugo, Github Pages and CircleCI helped me bring back to life my blog."
---


# Setting up the blog

I decided to bring back to life my blog :) I chose [Hugo](https://gohugo.io/) as the template engine because it seemed well-maintened, and had good documentation, [Github Pages](https://pages.github.com/) to host it and [CircleCI](https://circleci.com/) for automating deployment.

## Setting up Hugo

### Installing

I followed the [quickstart](https://gohugo.io/getting-started/quick-start/) section on the Hugo website, because I am on a Windows system I had to download an executable and put in somewhere on the PATH. So far so good.

### Choosing a theme

Then I spent countless hours inspecting many many [themes uploaded](https://themes.gohugo.io/) and picked my favorite. Most of the themes have configuration variables specific to the theme, and they usually are documented.

Personally I choose [hugo-resume](https://themes.gohugo.io/hugo-resume/), courtesy of [Eddie Webb](https://edwardawebb.com/). I was looking for:: 

- a blog section (with tags for blog posts)
- possibility to have sections to showcase projects

And that theme fitted those criteria, and also went beyond that because it had support for many other features (A section for education, skills, open source contributions, etc). Maybe one thing I'll miss is the direct support for internationalization. I was thinking maybe translating some of my post to French, but maybe I'll manage with tags or I'll deal with it later on.

To install a theme it's a matter of downloading the theme into the`theme/` folder (or use git submodule), and having a line in your config file to tell which theme to use if there is more than one in the `theme/`folder.

### Making it work

`hugo server` launch a server that serve your site and watch changes. It is useful to make changes to it. 

There are a few useful flags:
  - `-D` include draft 
  - `-F` include pages with a publish date in the futur

Then it's time to populate the website with content, congratulation :)

Note that the expected folder names in `content/` are `blog/`, `projects/creations/`, `projetcs/contributions/` and `publications/`. 

### Tweaks 

Below is some modification I made to the site to suits my need.

#### Code blocks

I added the option to have syntax highlighting (and line numbers in my code blocks). You simply have to add:

```toml
pygmentsCodefences = true # Allow syntax highlighting for code block
pygmentsCodefencesGuessSyntax = true # Pygment will try to guess what language is in the code block to use the right highlighting
pygmentsOptions = "linenos=table"
```
to your config file. 
You can find more options to customize syntax highlighting in [the Hugo docs](https://gohugo.io/content-management/syntax-highlighting/) 

#### GDPR and privacy concerns

I tried to be as light on tracking as possible for my website so I disabled any tracking service.
The only real downside is that I can't embed youtube videos anymore but that is a tradeoff I'm willing to make.

```toml
[privacy]
  [privacy.disqus]
    disable = true
  [privacy.googleAnalytics]
    disable = true
  [privacy.instagram]
    disable = true
  [privacy.twitter]
    disable = true
  [privacy.vimeo]
    disable = true
  [privacy.youtube]
    disable = true
```

See [documentation about gdpr complicance](https://gohugo.io/about/hugo-and-gdpr/) for more detailed options for each services.

#### Table of Content for blog posts

I wanted to display a table of content for my blog posts, but also being able to not display one for short posts that don't warrant one. 

It happens that this is really easy with Hugo :) With the help of this [post from ooze.com](https://www.codeooze.com/webdesign/hugo-toc/) I managed to do it very quickly.

Steps to do it are (for `hugo-resume` but should be fairly similar for other themes)::

1. Create `/layout/blog/` (to match the structure of `/themes/hugo-resume/layout/blog/`)  then copy the file `/themes/hugo-resume/layout/blog/single.html` into `/layout/blog/single.html`. For other themes or for adding toc in other section of the website, you just have to find the right `single.html` file to edit.

2. Then edit the `single.html` to add 
   ```html
   {{ if .Params.toc }}
	<div id="toc-blog">
		{{ .TableOfContents }}
	</div> 
	{{ end }}
   ```
   above the `{{ .Content }}` tag.
   
      Before I had 
   ```html
{{ define "main" }}
{{ partial "breadcrumbs" . }}
<section class="resume-section p-3 p-lg-5 d-flex d-column">
      <div class="my-auto">
         <h2 class="mb-0"><span class="text-primary">{{ .Title }}</span></h2>
         <span class="text-primary">{{ .Date.Format "January 2, 2006" }}</span>
	     {{ .Content }}
	     {{ with .Params.link }}<p>Project link: <a href="{{ . }}">{{ . }}</a></p>{{ end }}
	     {{ partial "techtags" . }}
      </div>
</section>
{{ end }}
   ```
   
      and after:
      ```html
{{ define "main" }}
{{ partial "breadcrumbs" . }}
<section class="resume-section p-3 p-lg-5 d-flex d-column">
      <div class="my-auto">
        <h2 class="mb-0"><span class="text-primary">{{ .Title }}</span></h2>
        <span class="text-primary">{{ .Date.Format "January 2, 2006" }}</span>
	    {{ if .Params.toc }}
	    <div id="toc-blog">
		  {{ .TableOfContents }}
	    </div> 
	    {{ end }}
	    {{ .Content }}
	    {{ with .Params.link }}<p>Project link: <a href="{{ . }}">{{ . }}</a></p>{{ end }}
	    {{ partial "techtags" . }}
      </div>
</section>
{{ end }}
   ```
   Adding the `{{ if .Params.toc}}` line will allow to define in the front matter of each post if we want to generate a toc.
   I also added a div tag with an id to style it.

3. Style it! Let's find the css file. Copy `/themes/hugo-resume/static/css/tweaks.css` into `/static/css/tweaks.css` and edit it:
```css
/* Change rules for toc in blog posts */
#toc-blog {
      margin-top:25px;
      margin-bottom:25px;
}
#toc-blog a {
      color: #0062cc
}
```

#### Adjust color of the site

TODO
I found the color of the text a bit to light for my eyes so I changed it. In `/themes/hugo-resume/static/css/` I changed `resume.css`.
```css
body {
  font-family: 'Open Sans', serif;
  padding-top: 54px;
  color: #33679a; /* was #868e96; */
}
```



#### Fix responsive width for blog post

TODO
After tweaking blog posts a bit, I found out that they were some issues with responsiveness. Around the 1300px mark for width, if you shrink the size of the screen, the text overflow which render the page hard to read without scrolling a lot.

After some googling (or [duckduckgoing](https://duckduckgo.com/) in my case), it seems it is because the container for blog post user `box-sizing: border-box` but the child does not specify a width. Adding `width: 100%` on the inner div fix the problem for me.

I added the following css rule in `tweaks.css`

```css
.my-auto {
  width: 100%
}
```

## Github Pages

TODO

## Circle CI

TODO
