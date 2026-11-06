---
title: "React Static Site Generators Compared: Six Options and How to Choose"
excerpt: "Compare six React static site generators, learn how builds work, static vs dynamic tradeoffs, and set up a Markdown blog with create-next-app step by step."
coverImage: "/assets/blog/preview/cover.jpg"
date: "2026-11-06T15:11:48.841Z"
author:
  name: Joe Haddad
  picture: "/assets/blog/authors/joe.jpeg"
ogImage:
  url: "/assets/blog/preview/cover.jpg"
pentraDeliveryKey: "pentra:7ba85e0d394a0268e54273ffa99d0afc9fe398e0e14b78194024833bd3651ab0"
---

A React static site generator turns React components and content into pre-built HTML, CSS and JavaScript files at build time, so you can host the result on any static web server. For most new projects, Next.js (blog, marketing, app-like sites) or Docusaurus (documentation) is the practical starting point. React Static, once a popular pick, is now in maintenance mode.

## What a React static site generator is

Static site generation (SSG) means pages are rendered to files once, during a build, instead of on every request. When you run `next build`, for example, Next.js generates an HTML file per route, and the output can be hosted on any web server that serves HTML/CSS/JS static assets [10].

React fits in because the same components you would use in a browser app produce the HTML. React's own reference splits its DOM APIs three ways: client APIs render components in the browser, server APIs render them to HTML on the server, and static APIs generate static HTML for components [1]. That gives you the practical difference between the three models:

- **Client-only app:** the browser downloads JavaScript and renders everything itself.
- **Server-rendered app:** a server turns components into HTML for each request.
- **Static site:** the build step produces the HTML ahead of time, and no rendering server is needed at runtime.

Many generators blend these. React Static calls its output a "progressive static site": every exported HTML page is an entry point to a fully featured, automatically code-split React application, so it loads fast like a static page and then behaves like a single-page app [2]. Docusaurus likewise builds a single-page application with fast client-side navigation [8].

## Why choose React for static sites

- **Component reuse.** Headers, post cards, code blocks and layouts are written once as components and reused across every generated page. This is a property of how React works, and it is the main reason to prefer it over hand-written HTML templates.
- **A large ecosystem.** React Static states that it supports the whole React ecosystem, including CSS-in-JS libraries, query layers like GraphQL, and Redux [2]. Gatsby's own documentation use-case page lists React, Webpack, GraphQL and modern JavaScript as its foundations [4].
- **Familiar tooling.** If your team already writes React, the learning curve is routing and data loading rather than a new template language. React Static was designed to resemble the experience of Create React App [2].
- **A path to dynamic features.** Next.js lets you start as a static site or SPA and later upgrade to features that require a server [10].

## Top React static site generators compared

The tools below cover the main use cases. Each entry sticks to what the project's own documentation states.

### Next.js

**What it is:** A React framework whose static export (`output: 'export'`) generates one HTML file per route [10]. **Suits:** Blogs, marketing sites, and projects that may later need server features, since the same codebase can move from static to server-backed [10]. **Key features:** Create a project with `create-next-app`; the default setup enables TypeScript, Tailwind CSS, ESLint, App Router and Turbopack [3]. **Limitation:** A static export does not support features that need a Node.js server, including Incremental Static Regeneration, Draft Mode, rewrites, redirects, headers, Server Actions, and image optimization with the default loader [10].

### Gatsby

**What it is:** A React-based generator that its own documentation use-case page positions for technical documentation, built on React, Webpack and GraphQL [4]. **Suits:** Documentation sites (company docs, open source docs, design systems) and headless CMS projects, with code splitting, prefetching resources and lazy-loading built in [4]. **Key features:** A plugin system; on Netlify, for example, `gatsby-plugin-netlify` is added to the `plugins` array in `gatsby-config.js` [6]. **Limitation:** On Netlify, `StaticImage` and `gatsby-transformer-sharp` don't work properly for SSR or DSG pages, so images for those pages need to be hosted on a CDN [6].

### React Static

**What it is:** A progressive static site generator for React that is data agnostic, so you can supply data from anywhere [2]. **Suits:** Maintaining existing sites. Routing and templates are assigned in `static.config.js`, each route's `getData` result is stored as a JSON file beside its HTML on export, and `getSiteData` supplies data needed on most routes [5]. **Limitation:** It is in maintenance-only mode with no new major versions planned, and the maintainers name Remix and Next.js as good alternatives [2].

### Docusaurus

**What it is:** A static-site generator for documentation sites, where you mostly write Markdown files, with versioning, i18n, search and theme customization available; it can also produce personal sites, product sites, blogs and marketing landing pages [8]. **Suits:** Docs-first projects. The current docs reference version 3.10.2 [8]. **Key features:** Scaffold with `npx create-docusaurus@latest my-website classic`; the tutorial requires Node.js 20.0 or above [8][9]. **Limitation:** It is documentation-first, so a heavily custom marketing layout means working against its defaults rather than with them.

### Remix

**What it is:** A React framework that the React Static maintainers recommend, alongside Next.js, as an alternative to their tool [2]. **Suits:** Teams moving off React Static who want to evaluate a maintained React framework. **Limitation:** It appears in this comparison only as that recommendation, so confirm its static output mode against your hosting plan before choosing it for a purely static site.

### React DOM static APIs

**What it is:** The `react-dom/static` APIs in React itself, which generate static HTML for React components [1]. **Suits:** Developers who want to build their own minimal pipeline with no framework. **Limitation:** It is an API set rather than a site framework, so routing, content loading, and bundling are yours to assemble.

### Side-by-side summary

| Tool | Best for | Content and data | Main limitation |
|---|---|---|---|
| Next.js | Blogs, marketing sites, static-to-server growth | HTML file per route at build [10] | No server-only features in static export [10] |
| Gatsby | Documentation and headless CMS sites | GraphQL-based [4] | Image handling caveats on SSR/DSG pages on Netlify [6] |
| React Static | Existing sites | Data from anywhere via `getData` [2][5] | Maintenance-only [2] |
| Docusaurus | Docs, plus blogs and landing pages | Markdown files [8] | Documentation-first defaults |
| Remix | Migration from React Static | Not covered here | Confirm static output first |
| React DOM static APIs | Custom minimal pipelines | You supply it | No routing or framework layer |

Eleventy and Gridsome are also frequently named in this category. Neither is evaluated here, so they aren't in the table.

## How static site generators build pages

1. **Gather data.** Content comes from Markdown files, a CMS, or APIs. React Static is explicitly data agnostic, and its docs cover using a CMS and rebuilding with webhooks [2][5]. Docusaurus expects you to write Markdown files [8].
2. **Render each route.** The generator runs your components against that data. In React Static, each route's `getData` output becomes its own JSON data file stored next to the route's HTML [5]. Next.js generates an HTML file per route during `next build` [10].
3. **Write the output.** Next.js writes to the `out` folder by default. For routes `/` and `/blog/[id]`, it produces files such as `/out/index.html`, `/out/404.html`, `/out/blog/post-1.html` and `/out/blog/post-2.html` [10].
4. **Rebuild when content changes.** Because pages are files, new content means a new build. React Static's docs describe triggering rebuilds with webhooks [5]; in practice you wire your CMS or repository to run the build command.

Components are prerendered to HTML during the build, so browser-only APIs like `window`, `localStorage` and `navigator` are not available then. Next.js's guidance is to access them only in the browser, for example inside `useEffect` in a client component [10].

## Choosing the right generator

A simple way to decide is to start from project type and then check the constraints:

- **Documentation site:** Docusaurus is built for this [8], and Gatsby publishes a dedicated documentation use case [4].
- **Blog or content site:** Next.js with Markdown gives you full control over layout, and Docusaurus also supports blogs [8][10].
- **Marketing site:** Next.js is a fit if you may add server features later [10]; Docusaurus can produce landing pages [8].
- **Hosting fit:** Next.js static output works on any server that serves static assets, and its docs include an Nginx configuration and a GitHub Pages template [10]. Gatsby on Netlify may need extra plugins for some features [6].
- **Plugin ecosystem:** Gatsby and React Static are plugin-oriented [5][6]; Next.js and Docusaurus rely on their own configuration and documented features [8][10].
- **Learning curve:** Docusaurus's tutorial targets a first site in under five minutes [9]. Next.js has a one-command setup but more decisions to make on the way [3].
- **Maintenance risk:** Avoid starting new work on React Static, given its maintenance-only status [2].

## Static vs dynamic sites

**Performance.** Splitting an SPA into individual HTML files lets Next.js avoid loading unnecessary JavaScript on the client, which reduces bundle size and enables faster page loads [10].

**Hosting.** A static export can be hosted on any web server that serves HTML/CSS/JS static assets, so you don't need an application server running at request time [10].

**When you need dynamic behavior.** Anything that needs a Node.js server or logic that can't be computed during the build is unsupported in a Next.js static export: Server Actions, Route Handlers that rely on request cookies, rewrites, redirects, headers, Incremental Static Regeneration, and Draft Mode [10]. Choose a server-capable deployment if you need those. Progressive static sites can also add real-time data after load, since each page becomes a React app in the browser [2].

## Frequently asked questions

### Are React static sites good for SEO?

Static generation produces an HTML file for each route [10], so the content is present in the page before any JavaScript runs. React Static lists SEO friendliness as a feature [2], and Gatsby describes itself as optimized for SEO [4].

### Does the build need a server?

The build needs Node.js on your machine or CI; Next.js requires a minimum of Node.js 20.9 [3], and Docusaurus's tutorial asks for 20.0 or above [9]. Serving the output does not need a Node.js server for a Next.js static export [10].

### Can I still use client-side React features?

Yes. Docusaurus uses React to make the site interactive [8], and React Static pages turn into full React apps after loading [2].

### Is React Static still a safe choice?

It is in maintenance-only mode with no new major versions, and its maintainers point to Remix and Next.js [2].

## Building a Markdown blog step by step with Next.js

Next.js Blog Example with Markdown is a statically generated blog example built with Next.js and Markdown. It demonstrates static generation and preview mode, and it is a placeholder demo rather than a real product. Its homepage lists sample posts, including one on how to build a Markdown blog in Next.js. Use it as a reference for the layout below.

**1. Create the project.** Next.js requires Node.js 20.9 or later [3].

```bash
pnpm create next-app@latest my-app --yes
cd my-app
pnpm dev
```

`--yes` skips the prompts and uses saved preferences or defaults. Open `http://localhost:3000` to see the app [3]. Run `pnpm create next-app` without `--yes` and you see this prompt [3]:

```text
What is your project named? my-app
Would you like to use the recommended Next.js defaults?
  Yes, use recommended defaults - TypeScript, ESLint, No React Compiler, Tailwind CSS, No src/ directory, App Router, Cache Components, AGENTS.md
  No, reuse previous settings
  No, customize settings - Choose your own preferences
```

**2. Lay out the posts.** A simple structure is a `posts/` folder at the project root holding one `.md` file per article, plus a dynamic route such as `/blog/[id]` that renders them [10].

**3. Add front matter.** Put a metadata block (title, date, excerpt) at the top of each Markdown file, and write a small helper that reads the file, separates that block from the body, and returns both.

**4. Generate the pages.** A dynamic route needs a list of paths known at build time. In the Pages Router, `getStaticPaths` returns that list and `getStaticProps` loads each post's data. In the App Router, a dynamic route in a static export needs `generateStaticParams()`; without it, the route is unsupported [10].

**5. Enable static export.** In `next.config.js` [10]:

```js
/** @type {import('next').NextConfig} */
const nextConfig = {
  output: 'export',
}

module.exports = nextConfig
```

**6. Build and deploy.** Run `next build`. The static files appear in the `out` folder, for example `/out/blog/post-1.html` [10]. Upload that folder to any static host. Next.js's docs include an Nginx rewrite example for static hosts and a GitHub Pages template [10]. Draft Mode is unsupported in a static export [10], so if you need preview of unpublished posts, deploy to a server-capable host.

[Try Next.js Blog Example with Markdown](https://iamheisenburger.github.io) to see a statically generated Next.js blog with Markdown and preview mode laid out end to end.

## Related reading

- [Blog with Markdown: How to Build a Markdown Blog in Next.js](/posts/blog-with-markdown-nextjs)

## Sources

1. [React Reference Overview](https://react.dev/reference/react)
2. [GitHub - react-static/react-static](https://github.com/react-static/react-static)
3. [Next.js Installation](https://nextjs.org/docs/app/getting-started/installation)
4. [Gatsby for Technical Documentation](https://www.gatsbyjs.com/use-cases/technical-documentation/)
5. [react-static concepts](https://github.com/react-static/react-static/blob/master/docs/concepts.md)
6. [Gatsby on Netlify](https://docs.netlify.com/build/frameworks/framework-setup-guides/gatsby/)
7. [react static](https://github.com/danklut/react-static)
8. [Docusaurus Introduction](https://docusaurus.io/docs)
9. [Docusaurus Tutorial Intro](https://tutorial.docusaurus.io/docs/intro/)
10. [How to create a static export of your Next.js application](https://nextjs.org/docs/app/guides/static-exports)
