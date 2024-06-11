# Personal Blog

To run locally:

```sh
npx @11ty/eleventy --serve
```

## Notes from Jekyll Migration

### Problem-Solving Along the Way

I don't think there's a super clean config migration path from Jekyll to Eleventy but I might've also been underusing Jekyll's config. Per [this blog post](https://alex.pearwin.com/2020/06/jekyll-to-eleventy/) I moved my `_config.yml` (site-wide config) to `_data/site.json`, and then had to decide what to do about build config, which I didn't really have an equivalent for in Jekyll (I did have a config.rb, but it was minimal and I just deleted it). Eleventy currently (as of Jan 2024) [has three config filename options](https://www.11ty.dev/docs/config/) for build config. I tried using `eleventy.config.js` and specifying `"type": "module"` in my package.json settings per the recommendation of a friend, but that caused Eleventy to fatal. [It looks like Eleventy is trying to migrate away from CJS](https://www.11ty.dev/blog/canary-eleventy-v3/#new-features-and-a-short-upgrade-guide) but that work isn't yet complete, so I was able to leave the module type but did have to change to a `.cjs` extension for the config file.

Layouts are called layouts in 11ty's config and Jekyll's default pathing, but 11ty's path for them points to `_includes/` by default, which honestly seems kind of weird to me as a design choice. I've configured it in the general config file to just point to `_layouts/` as it did in Jekyll.

I also, for some reason I don't remember, had separate `_layouts` AND `_includes` directories, and my html page fragments (head, header, footer) were in `_includes`, which made this especially confounding. It's at this point that I started enthusiastically copying from Alex Pearce's [blog code](https://github.com/alexpearce/home) and I realized the syntax within the Liquid templating was different so I copied his and it just worked.

I then got stuck on my included scripts, which are probably absolute garbage and definitely include a fair amount of jquery (a combo of what's included by default by Jekyll and the theme I'd picked) but while commenting them out did finally make the blog serve locally, it also changed the location of my header text as well as the font used for the header and headings. (No posts appear yet.) So at this point I want to make this just work with my existing jquery for the theme and then update this later.

There's some pathing wonkiness happening. Eleventy is turning my pathing for posts (which was "/blog/{page slug}/") into "/blog/{page slug}/index.html", which I think was my fault for the trailing slash, as Eleventy seems to turn those into directories. Removing the trailing slash makes the files end up in `_site` in the expected location, although they now have no file extension and I'm not sure if that's what Eleventy expects (even though they're properly formatted HTML). Similarly, my about page was going to "/about/index.html". Deleting that file/directory and adding a permalink to about.md's front matter does fix the pathing, but now Eleventy can't seem to find the top-level (also without a file extension) about page.

There's also definitely something wonky happening with Sass, which worked out of the box with Jekyll, but in this way where I have no idea what Ruby libraries it was using and I think I deleted all my Ruby config, so I need to go look those up. I've installed the npm versions of compass and normalize but something can't seem to find compass anyway. This is probably worth just blowing away and redoing from scratch since I have the final CSS I currently care about anyway.

Update on the above: this definitely isn't everything but Compass was Ruby code that is totally unsupported and no longer works with modern Ruby so I've excised that entirely, but my understanding of CSS and SASS isn't good enough to figure out where it was being used and how extensively. So I've just started with deleting the import.

### Short List of Final Migration Steps

- add `package.json` and `eleventy.config.cjs` (see above)
- point layouts to `_layouts` in `eleventy.config.cjs`
- convert `_config.yml` to `_data/site.json`
- delete `config.rb`
- add `.eleventyignore` to ignore this readme
- replace my `jekyll-feed` RSS plugin with eleventy's [`rss`](https://www.11ty.dev/docs/plugins/rss/) plugin