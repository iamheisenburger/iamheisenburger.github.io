---
title: "Blog with Markdown: How to Build a Markdown Blog in Next.js"
excerpt: "Learn how a blog with Markdown works in Next.js: content files, front matter, static generation, preview mode, and a checklist to test your pipeline."
coverImage: "/assets/blog/preview/cover.jpg"
date: "2026-10-30T15:11:51.134Z"
author:
  name: Joe Haddad
  picture: "/assets/blog/authors/joe.jpeg"
ogImage:
  url: "/assets/blog/preview/cover.jpg"
pentraDeliveryKey: "pentra:755edda4bbbf6bf7f7f437764aa00ae96d3dcbd4976e2f76d52404bd9187b319"
---

A blog with Markdown keeps each post as a plain text file and converts those files to HTML. This guide proposes one design for a statically generated Next.js blog: a content folder of `.md` files, a small module that reads and converts them, and a dynamic route that renders each post. It is the author's chosen design, not the only way to build one.

The guide explains the pieces, the decisions you need to make, and how to check that your pipeline behaves predictably. It is written for developers who want a starting point.

## What a Markdown blog actually is

A Markdown blog separates content from presentation. Each post is a text file with two parts:

- **Front matter:** structured metadata at the top of the file, such as title, date, author and excerpt.
- **Body:** the post itself, written in Markdown syntax for headings, lists, links, emphasis and code.

Your site code reads the files, parses the metadata, converts the body to HTML, and renders it inside a page template. The posts contain no layout code, so you can change the template without rewriting content. Because posts live in your repository, they can go through the same pull request review and version history as your code.

## Why the Markdown flavor matters

"Markdown" is not one precisely defined format. The original specification was intentionally ambiguous in many areas, which led different implementations to interpret the same syntax differently, so the same document could render differently on different platforms. CommonMark was created to address those ambiguities and inconsistencies, and it provides an unambiguous specification of the syntax along with a comprehensive test suite meant to ensure consistent behavior [1].

For a blog, the practical question is whether the tools you use agree with each other:

- If you write posts in one editor and render them with a converter in your build, compare the two outputs for the same post. Any difference tells you the tools interpret some syntax differently.
- If you plan to change the converter library, run your existing posts through both versions and compare the HTML before you switch.

When you choose a Markdown-to-HTML converter, check its documentation for which dialect it follows and which extensions it adds. Extensions such as tables or task lists are worth testing specifically, because they go beyond the core syntax.

## How the pieces fit together

In this design each piece has one job.

### 1. A content directory

Put posts in a dedicated folder at the root of your project, for example `_posts/`. Use predictable file names and derive the URL slug from the file name. A name like `preview-mode-for-static-generation.md` gives a readable URL and makes the file easy to find later.

### 2. Front matter conventions

Decide on a fixed set of metadata fields and use them in every post. A reasonable starting set is title, date, author, excerpt and cover image path. Your listing page, post page and any social metadata depend on those fields being present and correctly typed.

Dates deserve particular care. Use one unambiguous format everywhere, and keep it parseable, because you will sort posts by date on the index page.

### 3. A small content-loading module

Write one module that does all file access. It should:

- list the files in the content directory,
- read a file and separate front matter from body,
- return a clean object with the fields your pages need,
- sort posts, newest first.

Keeping this in one place means your pages never touch the file system directly. It also gives you a single place to add validation, such as failing the build when a required field is missing.

### 4. Markdown-to-HTML conversion

In this design, conversion happens in your build step rather than in the visitor's browser. If you prefer client-side conversion, you accept that each visitor's browser does that work; decide deliberately.

Be deliberate about sanitization. If only you and trusted collaborators write posts, rendering the converted HTML directly may be a reasonable trade-off. If posts can come from people you do not fully trust, sanitize the output or restrict raw HTML in the converter's settings.

### 5. A dynamic route for posts

In this design, one dynamic route keyed on the post slug renders every post, using the loader to find the post that matches the slug. Next.js supports more than one routing system, so check the current Next.js documentation for the data-fetching and static-generation APIs that apply to the version and router you use.

### 6. An index page

The homepage lists posts from the content-loading module, usually with title, date, author and excerpt, and links to each post page. A hero or "more stories" section is a presentation choice layered on top of that same list.

## Deciding how to author posts

A Markdown blog is one option among several. The following framework is the author's recommendation. Use it to match the approach to your situation.

| Situation | Leaning toward |
|---|---|
| Developers write and review all posts in the repository | Plain Markdown files |
| Posts need interactive components embedded in the text | A format that lets Markdown embed components, at the cost of a more complex build |
| Non-technical editors publish regularly and need a visual editor | A hosted content management system, at the cost of an external service and API dependency |
| You want to learn static generation without extra moving parts | Plain Markdown files |

Plain Markdown favors simplicity and portability. Its limits are the flip side: no built-in editing interface for non-developers, and no embedded interactivity without extra tooling. If you are unsure, start with plain Markdown and revisit the choice if your editors or content needs change.

## Drafts: decide how you will review before publishing

Decide early how you will see a draft before it goes live. Ask: where do drafts live, who reviews them, and does the review need the real site template?

With files in a repository, a branch or a pull request is a natural place for drafts. If you want to see a draft rendered in your real template, check the Next.js documentation for its preview features, and look at how they apply to your Next.js version. Whatever you choose, it affects your content-loading module: design it so it can include or exclude drafts when asked.

## A reader-run check for your own pipeline

Before you publish, run this recommended checklist and adapt it to your content.

1. **Create a stress-test post.** Write one file that uses every syntax element you plan to use: headings at several levels, ordered and unordered lists, nested lists, links, inline code, fenced code blocks, blockquotes, and any extensions you rely on.
2. **Render it in two places.** View the output in your build and in your editor's preview. Note every difference. Disagreements are most likely at the edges that CommonMark's specification was created to pin down [1], such as nested lists or unusual emphasis combinations.
3. **Break the front matter on purpose.** Remove a required field in a copy of a post. The build should fail with a clear message, not publish a page that shows "undefined".
4. **Add a post with an odd slug.** Use capital letters or a space in the file name in a throwaway branch and see what URL you get. Fix your slug rules before links are public.
5. **Check the generated HTML.** Open the built page's source and confirm the post body is present in the HTML, not injected later by client-side code.
6. **Test a draft.** Confirm that a post marked as a draft does not appear in the index or in the generated routes.

If any check fails, fix the content-loading module rather than patching individual posts. That keeps the fix in one place.

## Common mistakes to avoid

- **Mixing metadata styles.** If some posts use one date format and others use another, sorting breaks. Validate in the loader.
- **Converting Markdown on the client without deciding to.** Make the choice deliberately, and keep it consistent.
- **Skipping the content contract.** Without a documented list of front matter fields, contributors can invent their own, and templates fill with conditionals.
- **Hardcoding paths for images.** Decide where images live and how posts reference them before you have many posts.
- **Ignoring dialect differences.** If your editor and your build use different Markdown flavors, a post can look right in one and wrong in the other.
- **Treating a starter as finished.** A starter shows the mechanics. You still need to decide on metadata, SEO fields, feeds and your own design.

## Where Next.js Blog Example with Markdown fits

Next.js Blog Example with Markdown is a statically generated blog example using Next.js and Markdown. Use it as a reference for how an index and posts connect, not as a finished publishing platform.

The homepage lists sample posts with a title, date, placeholder text and author, including "Dynamic Routing and Static Generation," "Learn How to Pre-render Pages Using Static Generation with Next.js" and "Preview Mode for Static Generation." The placeholder text makes it easy to swap in your own content.

[Try Next.js Blog Example with Markdown](https://iamheisenburger.github.io) to explore a minimal statically generated Markdown blog built with Next.js.

## Next steps

Start with plain Markdown files, a fixed set of front matter fields, one content-loading module and one dynamic route. Run the stress-test post through your pipeline, and settle your slug and date rules before you publish anything. Once that foundation is stable, add features such as feeds, tags or embedded components as you need them, and keep each addition behind the same loader so your posts stay portable.

## Related reading

- [Next.js Blog Example with Markdown home page](/)

## Sources

1. [CommonMark Specification](https://markedapp.com/help/CommonMark_Spec)
