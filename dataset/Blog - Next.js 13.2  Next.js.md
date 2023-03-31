Next.js 13.2 includes major improvements to the App Router (`app`) in preparation for stability:

- [**Built-in SEO Support:**](https://nextjs.org/blog/next-13#built-in-seo-support-with-new-metadata-api) New Metadata API to set static and dynamic `meta` tags.
- [**Route Handlers**:](https://nextjs.org/blog/next-13#custom-route-handlers) Custom request handlers, built on Web `Request` and `Response`.
- [**MDX for Server Components:**](https://nextjs.org/blog/next-13#mdx-for-server-components) Use React components inside Markdown, server-side only.
- [**Rust MDX Parser:**](https://nextjs.org/blog/next-13#rust-mdx-parser) Faster Markdown parsing with a brand new Rust plugin.
- [**Improved Error Overlay:**](https://nextjs.org/blog/next-13#improved-error-overlay) Separate Next.js and React stack traces for improved readability.
- [**Statically Typed Links (Beta):**](https://nextjs.org/blog/next-13#statically-typed-links) Prevent broken links with `next/link` and TypeScript.
- [**Turbopack Improvements (Alpha):**](https://nextjs.org/blog/next-13#turbopack-improvements) Compatibility with Webpack loaders and improved support.
- [**Next.js Cache (Beta):**](https://nextjs.org/blog/next-13#nextjs-cache) Progressive ISR and faster re-deploys of code changes.

Update today by running:

```
npm i next@latest react@latest react-dom@latest eslint-config-next@latest
```

## [Built-in SEO support with new Metadata API](https://nextjs.org/blog/next-13#built-in-seo-support-with-new-metadata-api)

Next.js has been designed from the beginning to [enable optimization for search engines](https://nextjs.org/learn/seo/introduction-to-seo).

Serving pre-rendered HTML content not only helps improve indexing for search engines but also improves the performance of your application. While Next.js has provided a simple API for modifying metadata in your application (`next/head`) for many versions, we wanted to redesign and enhance how you optimize for search engines with the App Router (`app`).

The new Metadata API allows you to define metadata (e.g. `meta` and `link` tags inside your HTML `head` element) with an explicit metadata configuration in any layout or page that is a Server Component.

```
// app/layout.tsx

import type { Metadata } from 'next';

export const metadata: Metadata = {
  title: 'Home',
  description: 'Welcome to Next.js',
};
```

This API is simple, composable, and designed to be compatible with streaming server rendering. For example, you can set common metadata attributes in your root layout for the entire application, and compose and merge metadata objects together for other routes in your application.

This includes support for dynamic metadata as well as static:

```
// Static metadata
export const metadata = {
  title: '...',
};

// Dynamic metadata
export async function generateMetadata({ params, searchParams }) {
  const product = await getProduct(params.id);
  return { title: product.title };
}
```

All metadata options are available, including the ability to provide custom metadata, with support for TypeScript through the [TypeScript plugin](https://beta.nextjs.org/docs/configuring/typescript#using-the-typescript-plugin) or by adding the `Metadata` type.

For example, you can define open graph images through metadata:

```
export const metadata = {
  openGraph: {
    title: 'Next.js',
    description: 'The React Framework for the Web',
    url: 'https://nextjs.org',
    siteName: 'Next.js',
    images: [
      {
        url: 'https://nextjs.org/og.png',
        width: 800,
        height: 600,
      },
    ],
    locale: 'en-US',
    type: 'website',
  },
};
```

The Metadata API is available in 13.2 for the App Router (`app`), replacing the previous `head.js` special file. It is not available for the `pages` directory.

[Learn more about SEO](https://beta.nextjs.org/docs/guides/seo) or view the [API reference for Metadata](https://beta.nextjs.org/docs/api-reference/metadata). We'd like to thank [next-seo](https://github.com/garmeeh/next-seo) for their work on the community package and feedback on the initial API design.

## [Custom Route Handlers](https://nextjs.org/blog/next-13#custom-route-handlers)

One of the missing pieces for the original beta release of the App Router (`app`) was API Routes, which exist in the `pages/api` directory. We wanted to take this opportunity to create a new, more modern version of API Routes that were deeply integrated into the new routing system for `app`.

Route Handlers allow you to create custom request handlers for a given route using the Web [Request](https://developer.mozilla.org/en-US/docs/Web/API/Request) and [Response](https://developer.mozilla.org/en-US/docs/Web/API/Response) APIs.

```
export async function GET(request: Request) {}
```

Route Handlers have an isomorphic API to support both Edge and Node.js runtimes seamlessly, including support for streaming responses. Since Route Handlers use the same [route segment configuration](https://beta.nextjs.org/docs/routing/fundamentals) as pages and layouts, they support long-awaited features like general-purpose [Static Rendering](https://beta.nextjs.org/docs/routing/route-handlers#static-and-dynamic-route-handlers) and [Revalidation](https://beta.nextjs.org/docs/data-fetching/revalidating).

A `route.ts` file can export an async function named by the HTTP verbs: `GET`, `HEAD`, `OPTIONS`, `POST`, `PUT`, `DELETE`, and `PATCH`. These functions can then be wrapped and abstracted to create helpers / reusable logic for your custom route logic.

Other server functions, like `cookies` and `headers`, can be used inside Route Handlers – along with any Web APIs these abstractions are built upon. This allows for code to be shared between Server Components and Route Handlers.

```
import { cookies } from 'next/headers';

export async function GET(request: Request) {
  const cookieStore = cookies();
  const token = cookieStore.get('token');

  return new Response('Hello, Next.js!', {
    status: 200,
    headers: { 'Set-Cookie': `token=${token}` },
  });
}
```

Route Handlers are available in 13.2 for the App Router (`app`) using the `route.ts` special file. They are not available in the `pages` directory, as they are a replacement for API Routes.

[Learn more about Route Handlers](https://beta.nextjs.org/docs/routing/route-handlers) or [view the API reference](https://beta.nextjs.org/docs/api-reference/file-conventions/route). We'd like to thank SvelteKit for their [prior art and inspiration](https://kit.svelte.dev/docs/routing#server) here.

## [MDX for Server Components](https://nextjs.org/blog/next-13#mdx-for-server-components)

MDX is a superset of markdown that lets you write JSX directly in your markdown files. It is a powerful way to add dynamic interactivity and embed React components within your content.

With 13.2, you can now use MDX entirely with React Server Components – meaning less client-side JavaScript for faster page loads, all while retaining the powerful capabilities of React for templating dynamic UI. You can sprinkle interactivity into your MDX content as needed.

The `@next/mdx` plugin has been updated with support for a new special file, `mdx-components.js|ts`, defined at the root of your application to provide custom components:

```
// This file allows you to provide custom React components
// to be used in MDX files. You can import and use any
// React component you want, including components from
// other libraries.
function H1({ children }) {
  // ...
}

function H2({ children }) {
  // ...
}

export function useMDXComponents(components) {
  return { h1: H1, h2: H2, ...components };
}
```

Further, we've worked with community packages for fetching MDX content [`next-mdx-remote`](https://github.com/hashicorp/next-mdx-remote#react-server-components-rsc--nextjs-app-directory-support) and [`contentlayer`](https://www.contentlayer.dev/) to add support for React Server Components.

[Learn more about how to setup MDX with Server Components](https://beta.nextjs.org/docs/guides/mdx) or [deploy our example](https://vercel.com/new/clone?repository-url=https%3A%2F%2Fgithub.com%2Fvercel%2Fnext.js%2Ftree%2Fcanary%2Fexamples%2Fapp-dir-mdx&project-name=app-dir-mdx&repository-name=app-dir-mdx).

## [Rust MDX Parser](https://nextjs.org/blog/next-13#rust-mdx-parser)

As part of enabling MDX for Server Components, we've also rewritten the MDX parser in Rust to improve performance. This is a significant improvement over the previous JavaScript-based parser, which saw noticable slowdowns when processing a large number of MDX files.

You can opt-into using the Rust parser in `next.config.js`. For example, with `@next/mdx`:

```
/** @type {import('next').NextConfig} */
const nextConfig = {
  experimental: {
    appDir: true,
    mdxRs: true,
  },
};

const withMDX = require('@next/mdx')();
module.exports = withMDX(nextConfig);
```

We'd like to thank [Titus Wormer](https://wooorm.com/) who we sponsored to work on this project. If you'd like to use this outside of Next.js, check out the new package [mdxjs-rs](https://github.com/wooorm/mdxjs-rs).

## [Statically Typed Links](https://nextjs.org/blog/next-13#statically-typed-links)

Next.js can now statically type links in the `app` directory to prevent typos and other errors when using `next/link`, improving type safety when navigating between pages.

```
import Link from 'next/link'

// ✅
<Link href="/about" />
// ✅
<Link href="/blog/nextjs" />
// ✅
<Link href={`/blog/${slug}`} />

// ❌ TypeScript errors if href is not a valid route
<Link href="/aboot" />
```

This feature requires using the new App Router, as well as TypeScript.

```
// next.config.js

/** @type {import('next').NextConfig} */
const nextConfig = {
  experimental: {
    appDir: true,
    typedRoutes: true,
  },
};

module.exports = nextConfig;
```

This feature is now available in beta. `rewrites` and `redirects` are not yet supported.

[Learn more about statically typed routes](https://beta.nextjs.org/docs/configuring/typescript#statically-typed-links).

## [Improved Error Overlay](https://nextjs.org/blog/next-13#improved-error-overlay)

To help improve the readability and debugability of errors, we've made a number of improvements to the Next.js error overlay.

In 13.2, Next.js and React stack traces are now separated, making it easier to identify where the error is coming from. Additionally, the error overlay now displays the current version of Next.js, helping you understand whether your version is up to date.

![](https://nextjs.org/_next/image?url=%2Fstatic%2Fblog%2Fnext-13-2%2Ferror.png&w=1920&q=75)

The improved error overlay in 13.2 showing version staleness.

We've also [improved the error output](https://github.com/facebook/react/issues/24519#issuecomment-1439915463) for React hydration errors, which are now more readable and easier to debug.

## [Turbopack Improvements](https://nextjs.org/blog/next-13#turbopack-improvements)

[Turbopack](https://turbo.build/pack), announced in alpha with Next.js 13, is an incremental bundler designed to speed up both local development, as well as production builds in the future.

We've been focused on supporting existing Next.js features in Turbopack and improving overall stability as we move towards beta. Since our last release, we've added:

- Support for `next/dynamic`
- Support for `rewrites`, `redirects`, `headers` , and `pageExtensions` in `next.config.js`
- Support for 404s and errors in `pages`
- Support for CSS modules `composes: ... from ...`
- Improved Fast Refresh reliability and error recovery
- Improved CSS precedence handling
- Improved compile-time evaluation

We've also fixed many bugs and improved stability while dogfooding Turbopack with some of our largest internal Next.js applications and with early Vercel customers.

### [Custom File Transformation with Webpack Loaders](https://nextjs.org/blog/next-13#custom-file-transformation-with-webpack-loaders)

Turbopack now ships with support and compatibility for some webpack loaders. This means that you can use many loaders from the Webpack ecosystem to transform files of different types to JavaScript. Loaders like `@mdx-js/loader`, `@svgr/webpack`, and `babel-loader` are supported. [Learn more](https://turbo.build/pack/docs/features/customizing-turbopack) about customizing Turbopack.

For example, use `experimental.turbo.loaders` to configure a list of loaders for each file extension:

```
// next.config.js
module.exports = {
  experimental: {
    turbo: {
      loaders: {
        '.md': [
          {
            // Option format
            loader: '@mdx-js/loader',
            options: {
              format: 'md',
            },
          },
        ],
        '.svg': ['@svgr/webpack'],
      },
    },
  },
};
```

Check out the [Turbopack example using loaders](https://github.com/vercel/next.js/tree/canary/examples/with-turbopack-loaders) for a complete example.

### [Webpack-style Resolve Aliases](https://nextjs.org/blog/next-13#webpack-style-resolve-aliases)

Turbopack can now be configured to modify module resolution through aliases, similar to webpack's [`resolve.alias`](https://webpack.js.org/configuration/resolve/#resolvealias). Configure this through `experimental.turbo.resolveAlias`:

```
// next.config.js
module.exports = {
  experimental: {
    turbo: {
      resolveAlias: {
        underscore: 'lodash',
        mocha: { browser: 'mocha/browser-entry.js' },
      },
    },
  },
};
```

## [Next.js Cache](https://nextjs.org/blog/next-13#nextjs-cache)

Next.js 13.2 introduces the new Next.js Cache (beta), an evolution of [ISR](https://beta.nextjs.org/docs/data-fetching/revalidating) that unlocks:

- Progressive ISR at the component level
- Faster refreshes without network requests
- Faster redeploys of code changes to static pages

For pages that are entirely static, ISR works the same way as today. For pages that have more granular data fetching, mixing static and dynamic, the Next.js Cache uses a more granular, ephemeral cache.

With the foundation of [React Server Components](https://beta.nextjs.org/docs/rendering/server-and-client-components) and colocated data fetching in the Next.js App Router (`app`), you can now encapsulate static or dynamic data alongside their consuming component.

```
// app/page.jsx

export default async function Page() {
  const [staticData, dynamicData, revalidatedData] = await Promise.all([
    // Cached until manually invalidated
    fetch(`https://...`),
    // Refetched on every request
    fetch(`https://...`, { cache: 'no-store' }),
    // Cached with a lifetime of 10 seconds
    fetch(`https://...`, { next: { revalidate: 10 } }),
  ]);

  return <div>...</div>;
}
```

While developing locally with the App Router, you'll now see the same caching behavior in `next dev` as production with `next start`. This improves the speed of Fast Refresh when any Server Component or data loading code changes.

With the Next.js Cache, your app controls the cache—not third-party APIs. This differs from `cache-control` headers, where the upstream controls how long the value is cached.

### [Next.js Cache with the Vercel Cache API](https://nextjs.org/blog/next-13#nextjs-cache-with-the-vercel-cache-api)

[Next.js on Vercel](https://vercel.com/docs/concepts/next.js/overview) gives you framework-defined infrastructure. You write application code, like component-level data fetching with `fetch`, and we scaffold globally distributed infrastructure for you with no additional effort.

The new Next.js Cache makes changing code independent from changing data. This can drastically speed up the redeployment of static pages, since the generation of these pages can use the existing cache.

This new Vercel Cache API is designed to work with any framework, but has native integration with the Next.js Cache. [Learn more about how ISR evolved into the Next.js Cache](https://vercel.com/blog/vercel-cache-api-nextjs-cache), as well as how the Next.js Cache works when deploy to Vercel.

### [Next.js Cache when Self-Hosted](https://nextjs.org/blog/next-13#nextjs-cache-when-self-hosted)

When self-hosting, an LRU cache is used, which defaults to 50 MB. All entries into the cache are automatically written to disk by default. This filesystem cache can be shared between nodes if they have the same cache key, similar to [how ISR works today](https://nextjs.org/docs/basic-features/data-fetching/incremental-static-regeneration#self-hosting-isr).

For developers looking to further customize and modify the core of the Next.js Cache, they can modify underlying cache keys and change how and where cache entries are persisted, including disabling persistence entirely.

## [Other Improvements](https://nextjs.org/blog/next-13#other-improvements)

- **Fonts:** Following incredible community adoption, `@next/font` is now built-in to Next.js as `next/font`. This means you no longer need to install `@next/font` separately. [Learn more](https://nextjs.org/docs/basic-features/font-optimization).
- **Fonts:** The default `font-display` property for `next/font` has been changed to `font-display: swap` instead of `optional` based on community feedback.
- **Performance:** Optimized the build process to use less memory, ~550MB saved in our tests ([PR](https://github.com/vercel/next.js/pull/45639)).
- **Performance:** Avoid loading project configuration multiple times, leading to ~400ms faster builds (average) in our tests ([PR](https://github.com/vercel/next.js/pull/45637)).
- **Performance:** Optimized the error component to reduce 0.4kb of the HTML payload without changing the styling ([PR](https://github.com/vercel/next.js/pull/45586)).
- **Performance:** Reduced the edge bundle size by ~130KB, almost half of the size, to further decrease the cold boot size when deployed to edge environments like Vercel ([PR](https://github.com/vercel/next.js/pull/45601)).
- **Security:** Added configuration `images.contentDispositionType: "attachment"` to force download images when visiting the Image Optimization API directly ([PR](https://github.com/vercel/next.js/pull/46254)).
