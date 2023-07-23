# Focus Otter Blog

Hey! This is my blog 😊

It's a Hugo blog based of off the [up-buisness](https://gitlab.com/writeonlyhugo/up-business-demo) theme.

I was going to go with a NextJS style app, but I wanted markdown, embed shortcodes, code-highlighting, etc. Though I can do that with MDX, prism, etc, I'd have to wire all of that up myself...Hugo gives me that out of the box with a bunch of [ready-to-go themes](https://themes.gohugo.io/tags/blog/).

## Purpose of this readme

Though Hugo is built on the Go programming language, you don't need to know it. It's all just HTML, CSS, and JavaScript. With that said, setting this blog up did come with some challenges in terms of knowing what my local environment needed to download and how to host it. In addition, Hugo does a lot for you out of the box.

This readme will describe some of those challenges, how I overcame them, but also have a few notes for myself on things I should remember when it comes to creating blog posts and such.

## How I plan to use this blog

I'm trying out a new flow:

GitHub Repo -> [Video Tap](https://videotap.com/) -> Blog Post -> FeedHive.

I've always felt that I should start with a blog first, but by doing a video first, I can automate the blog process (still cleaning it up where need be), and use my TBC cross-posting tool to publish to Medium, Hashnode, and Dev.to. From there, I can use FeedHive + The tweets generated from Video Tap to schedule out Social Media posts.

This is experimental, but I feel is a solid attempt to 10x my workflow. Still on the lookout for a video editor 👀

---

## Hosting on Amplify

So Amplify _supports_ Hugo projects, but the version it has is dated. Even when setting the build options to `latest` it still doesn't use the `extended` version. So as part of my build process, I download the extended version.

Checkout the `amplify.yml` file in the root of this project's directory.

## The Hugo Project Structure

I'm still wrapping my head around this TBH.

So non-sensitive, project values and configuration are in the `config.yaml` file.

Static assets like fonts and images are in the `/static` directory.

The `/resources/_gen` directory I think I'm save to leave alone since the name suggests it's stuff that gets autogenerated. I actually tried deleting it, but it came back on the next build.

The `layouts` directory contains my homepage (`index.html`), a custom 404 page (`404.html`). This also contains sectional data like `partials` and layout fields. This is where I'll probably go when I need to tweak things.

The `data/home` directory seems to have all of the config data for the `layouts/index.html` file.

In short, a `layout/*.html` page references a partial and config.

## Understanding the Home Page

## Working with the Hugo Blog Sections

As mentioned, this section will focus on things I need to remember when it comes to creating a Hugo Blog. So while you (reader), may find them useful, it's really intended for me...so I can't guarantee everything will make sense 😅

### Running the site locally

Use `hugo server`

Different from React apps, this runs on `http://localhost:1313`

### Theme.toml file

I don't know what the `theme.toml` file is for, but I deleted it and nothing seemed to break. Look in your git history if something happens and you end up needing it.

I also deleted the `.hidden` file, and the `hugo_lock` file 🤷🏽‍♂️

### Images

I host my images in Cloudinary instead of including them in the build. This way they can be cached by a CDN and the right size image can be served based on the users network and device.

The Cloudinary credentials are in 1Password under the email address `mtliendo+blog@focusotter.com`.

#### Cover Image

When I first downloaded the template, the `image` property was an array of images. I'm not sure why, but in any case, I switched it to a string:

```md
image = "https://res.cloudinary.com/dgtvzkmvu/image/upload/f_auto,q_auto/v1689496355/home-automation/cover_ocgv2i.png"
```

To get the cover image to show up on the blog page and as the thumbnail on the card, I modified the `/partials/utilities/get-featured-image.html` and `/partials/utilities/card-img-top.html` so that they look for the `image` key in the frontmatter.

Then to display the cover image on an individual blog, I added the following to the `layout/_default/single.html` file:

```md
{{ with .Params.image }}

<div class="fig-img">
<img src="{{ . }}" alt="cover" width="100%" height="100%" />
</div>
{{ end }}
```

#### Markdown Images

To add an image, use the following Hugo shortcode:

```md
{{< figure class="fig-img"
src="https://uri.png"
title="some alt text" >}}
```

Note that this **does** need to have the class `fig-img`. This is to make sure it fits within the blog layout. The code to do this is in `blog-single.scss` as:

```scss
.fig-img img {
	max-width: 100%;
	height: auto;
}
```

### Adding Dates to Posts

The theme didn't come with dates added to posts so I modified the `list.html` and `single.html` files with this code snippet:

```md
{{ with .Params.date.Format "January 02, 2006"}}

<p class="text-black-61 text-center pb-3">{{ . }}</p>
{{ end }}
```

> 🗒️ Be sure the frontmatter date is "2006-01-02" format.

### Creating a new post

If I want to create a new post, insteat of adding the file manually, I should use the following command:

```sh
hugo new posts/my-new-file.md
```

This is because the `archetypes/default.md` file will be read and apply default frontmatter. What's cool is the date will be populated and the title will be based on the file name but the hyphens will be removed and title casing will be applied.

> 🗒️ Be sure to update the date so that it's "2006-01-02" format.

### Series and Tags

Pretty straight forward.

A frontmatter `series` is an array of strings. Each string value (ex "Themes Place") will be hyphenated and any posts with the same value will be put in a series.

So `series = ["Themes Guide"]` becomes `/series/themes-guide`.

The same applies for tags.

### Blog Shortcodes

I touched on this above, but it's fairly easy to add in a shortcode.

There are codes available for tweets, youtube videos, gists, and more. Checkout the [link to the docs here](https://gohugo.io/content-management/shortcodes/#use-hugos-built-in-shortcodes)
