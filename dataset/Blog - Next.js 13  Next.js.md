As we announced at [Next.js Conf](https://nextjs.org/conf), Next.js 13 (stable) lays the foundations to be dynamic without limits:

-   [**`app` Directory (beta):**](https://nextjs.org/blog/next-13#new-app-directory-beta) Easier, faster, less client JS.
    -   [**Layouts**](https://nextjs.org/blog/next-13#layouts)
    -   [**React Server Components**](https://nextjs.org/blog/next-13#server-components)
    -   [**Streaming**](https://nextjs.org/blog/next-13#streaming)
-   **[Turbopack (alpha):](https://nextjs.org/blog/next-13#introducing-turbopack-alpha)** Up to 700x faster Rust-based Webpack replacement.
-   **[New `next/image`:](https://nextjs.org/blog/next-13#nextimage)** Faster with native browser lazy loading.
-   **[New `@next/font` (beta):](https://nextjs.org/blog/next-13#nextfont)** Automatic self-hosted fonts with zero layout shift.
-   **[Improved `next/link`:](https://nextjs.org/blog/next-13#breaking-changes)** Simplified API with automatic `<a>`.

Next.js 13 and the `pages` directory are stable and ready for production. Update today by running:

```
npm i next@latest react@latest react-dom@latest eslint-config-next@latest
```

## [New `app` Directory (Beta)](https://nextjs.org/blog/next-13#new-app-directory-beta)

Today, we're improving the routing and layouts experience in Next.js and aligning with the future of React with the introduction of the `app` directory. This is a follow-up to the [Layouts RFC](https://nextjs.org/blog/layouts-rfc) previously published for community feedback.

The `app` directory is currently in beta and we do not recommend using it in production yet. You can use Next.js 13 with the `pages` directory with stable features like the improved [`next/image`](https://nextjs.org/blog/next-13#nextimage) and [`next/link`](https://nextjs.org/blog/next-13#nextlink) components, and opt into the `app` directory at your own pace. The `pages` directory will continue to be supported for the foreseeable future.

The `app` directory includes support for:

-   **[Layouts:](https://nextjs.org/blog/next-13#layouts)** Easily share UI between routes while preserving state and avoiding expensive re-renders.
-   **[Server Components:](https://nextjs.org/blog/next-13#server-components)** Making server-first the default for the most dynamic applications.
-   **[Streaming:](https://nextjs.org/blog/next-13#streaming)** Display instant loading states and stream in units of UI as they are rendered.
-   **[Support for Data Fetching:](https://nextjs.org/blog/next-13#data-fetching)** `async` Server Components and extended `fetch` API enables component-level fetching.

![](https://nextjs.org/_next/image?url=%2Fstatic%2Fblog%2Flayouts-rfc%2Fapp-folder.png&w=3840&q=75)

The app directory can be incrementally adopted from your existing pages/ directory.

### [Layouts](https://nextjs.org/blog/next-13#layouts)

The `app/` directory makes it easy to lay out complex interfaces that maintain state across navigations, avoid expensive re-renders, and enable advanced routing patterns. Further, you can nest layouts, and **colocate application code** with your routes, like components, tests, and styles.

![](https://nextjs.org/_next/image?url=%2Fstatic%2Fblog%2Fnext-13%2Fcolocating-assets-in-the-app-directory.png&w=3840&q=75)

The app/ directory can be incrementally adopted from your existing pages/ directory.

Creating routes inside \`app/\` requires a single file, \`page.js\`:

```
// app/page.js
// This file maps to the index route (/)
export default function Page() {
  return <h1>Hello, Next.js!</h1>;
}
```

You can then **define layouts** through the file system. Layouts share UI between multiple pages. On navigation, layouts preserve state, remain interactive, and do not re-render.

```
// app/blog/layout.js
export default function BlogLayout({ children }) {
  return <section>{children}</section>;
}
```

[Learn more about layouts and pages](https://beta.nextjs.org/docs/routing/fundamentals) or [deploy an example to try it out](https://vercel.com/templates/next.js/app-directory).

### [Server Components](https://nextjs.org/blog/next-13#server-components)

The `app/` directory introduces support for React's new Server Components architecture. [Server and Client Components](https://beta.nextjs.org/docs/rendering/server-and-client-components) use the server and the client each for what they're best at - allowing you to build fast, highly-interactive apps with a single programming model that provides a great developer experience.

With Server Components, we're laying the foundations to build complex interfaces while **reducing the amount of JavaScript sent to the client**, enabling faster initial page loads.

When a route is loaded, the Next.js and React runtime will be loaded, which is **cacheable** and **predictable** in size. This runtime does _not_ increase in size as your application grows. Further, the runtime is asynchronously loaded, enabling your HTML from the server to be progressively enhanced on the client.

[Learn more about Server Components](https://beta.nextjs.org/docs/rendering/server-and-client-components) or [deploy an example to try it out](https://vercel.com/templates/next.js/app-directory).

### [Streaming](https://nextjs.org/blog/next-13#streaming)

The `app/` directory introduces the ability to progressively render and incrementally stream rendered units of the UI to the client.

With Server Components and [nested layouts](https://beta.nextjs.org/docs/routing/pages-and-layouts) in Next.js, you're able instantly render parts of the page that do not specifically require data, and show a [loading state](https://beta.nextjs.org/docs/routing/loading-ui) for parts of the page that are fetching data. With this approach, the user does not have to wait for the entire page to load before they can start interacting with it.

![](https://nextjs.org/_next/image?url=%2Fstatic%2Fblog%2Fnext-13%2Fstreaming.png&w=3840&q=75)

You can colocate your application code, such as components, tests, and styles, with your routes.

When deployed to Vercel, Next.js 13 applications that use the `app/` directory will stream responses by default in both the Node.js and Edge runtimes for improved performance.

[Learn more about streaming](https://beta.nextjs.org/docs/data-fetching/fundamentals) or [deploy an example to try it out](https://vercel.com/templates/next.js/app-directory).

### [Data Fetching](https://nextjs.org/blog/next-13#data-fetching)

React's recent [Support for Promises RFC](https://github.com/acdlite/rfcs/blob/first-class-promises/text/0000-first-class-support-for-promises.md) introduces a **powerful new way to fetch data and handle promises** inside components:

```
// app/page.js
async function getData() {
  const res = await fetch('https://api.example.com/...');
  // The return value is *not* serialized
  // You can return Date, Map, Set, etc.
  return res.json();
}

// This is an async Server Component
export default async function Page() {
  const data = await getData();

  return <main>{/* ... */}</main>;
}
```

The native `fetch` Web API has also been extended in React and Next.js. It [automatically dedupes fetch requests](https://beta.nextjs.org/docs/data-fetching/fundamentals#automatic-fetch-request-deduping) and provides **one** flexible way to fetch, cache, and revalidate data at the component level. This means all the benefits of Static Site Generation (SSG), Server-Side Rendering (SSR), and Incremental Static Regeneration (ISR) are now available through one API:

```
// This request should be cached until manually invalidated.
// Similar to `getStaticProps`.
// `force-cache` is the default and can be omitted.
fetch(URL, { cache: 'force-cache' });

// This request should be refetched on every request.
// Similar to `getServerSideProps`.
fetch(URL, { cache: 'no-store' });

// This request should be cached with a lifetime of 10 seconds.
// Similar to `getStaticProps` with the `revalidate` option.
fetch(URL, { next: { revalidate: 10 } });
```

In the `app` directory, you can fetch data inside [layouts, pages](https://beta.nextjs.org/docs/routing/pages-and-layouts), and components – including support for [streaming responses](https://nextjs.org/blog/next-13#streaming) from the server.

We're enabling ergonomic ways to handle loading and error states and stream in UI as it's rendered. In a future release, we'll be improving and simplifying data mutations, as well.

![](https://nextjs.org/_next/image?url=%2Fstatic%2Fblog%2Fnext-13%2Fstreaming.png&w=3840&q=75)

With the app/ directory, you can use a new special file loading.js to automatically create Instant Loading UI with Suspense boundaries.

We're excited to work with the open-source community, package maintainers, and other companies contributing to the React ecosystem to build for this new era of React and Next.js. The ability to colocate data fetching inside components and ship less JavaScript to the client were two important pieces of community feedback we are excited to include with the `app/` directory.

[Learn more about data fetching](https://beta.nextjs.org/docs/data-fetching/fundamentals) or [deploy an example to try it out](https://vercel.com/templates/next.js/app-directory).

## [Introducing Turbopack (Alpha)](https://nextjs.org/blog/next-13#introducing-turbopack-alpha)

Next.js 13 includes [Turbopack](https://vercel.com/blog/turbopack), the new Rust-based successor to Webpack.

Webpack has been downloaded over **3 billion times**. While it's been an integral part of building the Web, we've hit the limits of the maximum performance possible with JavaScript-based tooling.

In Next.js 12, we began our transition to native Rust-powered tooling. We started by migrating away from Babel, which resulted in 17x faster transpilation. Then, we replaced Terser, which resulted in 6x faster minification. It's time to go all-in on native for bundling.

Using the Turbopack alpha with Next.js 13 results in:

-   **700x faster** updates than Webpack
-   **10x faster** updates than Vite
-   **4x faster** cold starts than Webpack

![](https://nextjs.org/_next/image?url=%2Fstatic%2Fblog%2Fnext-13%2Fturbopack.png&w=3840&q=75)

Turbopack is our Rust-based successor to Webpack, with 700x faster HMR for large applications.

[Turbopack](https://turbo.build/pack) only bundles the minimum assets required in development, so startup time is extremely fast. On an application with 3,000 modules, **Turbopack takes 1.8 seconds** to boot up. **Vite takes 11.4 seconds** and **Webpack takes 16.5 seconds**.

Turbopack has out-of-the-box support for Server Components, TypeScript, JSX, CSS, and more. During the alpha, [many features](https://turbo.build/pack/docs/features) are not yet supported. We'd love to hear your feedback on using Turbopack to speed up your local iterations.

> **Note:** Turbopack in Next.js currently only supports `next dev`. View the [supported features](https://turbo.build/pack/docs/features). We are also working to add support for `next build` through Turbopack.

Try out the Turbopack alpha today in Next.js 13 with `next dev --turbo`.

## [`next/image`](https://nextjs.org/blog/next-13#nextimage)

Next.js 13 introduces a **powerful new Image component**, allowing you to easily display images without layout shift and optimize files on-demand for increased performance.

During the Next.js Community Survey, **70% of respondents** told us they used the Next.js Image component in production, and in turn, saw **improved Core Web Vitals**. With Next.js 13, we're improving `next/image` even further.

The new Image component:

-   Ships less client-side JavaScript
-   Easier to style and configure
-   More accessible requiring `alt` tags by default
-   Aligns with the Web platform
-   Faster because native lazy loading doesn't require hydration

```
import Image from 'next/image';
import avatar from './lee.png';

function Home() {
  // "alt" is now required for improved accessibility
  // optional: image files can be colocated inside the app/ directory
  return <Image alt="leeerob" src={avatar} placeholder="blur" />;
}
```

[Learn more about the Image component](https://nextjs.org/docs/basic-features/image-optimization) or [deploy an example to try it out](https://vercel.com/templates/next.js/nextjs-boilerplate).

### [Upgrading `next/image` to Next.js 13](https://nextjs.org/blog/next-13#upgrading-nextimage-to-nextjs-13)

The old Image component was renamed to `next/legacy/image`. We've provided a codemod that will automatically update your existing usage of `next/image` to `next/legacy/image`. For example, this command would run the codemod on your `./pages` directory when run from the root:

```
npx @next/codemod next-image-to-legacy-image ./pages
```

[Learn more about the codemod](https://nextjs.org/docs/advanced-features/codemods) or [check out the documentation](https://nextjs.org/docs/basic-features/image-optimization).

## [`@next/font`](https://nextjs.org/blog/next-13#nextfont)

Next.js 13 introduces a **brand new font system** that:

-   Automatically optimizes your fonts, including custom fonts
-   Removes external network requests for improved privacy and performance
-   Built-in automatic self-hosting for any font file
-   Zero layout shift automatically using the CSS [`size-adjust`](https://web.dev/css-size-adjust/) property

This new font system allows you to conveniently use all Google Fonts with performance and privacy in mind. CSS and font files are downloaded at build time and self-hosted with the rest of your static assets. **No requests are sent to Google by the browser.**

```
import { Inter } from '@next/font/google';

const inter = Inter();

<html className={inter.className}>
```

Custom fonts are also supported, including support for automatic self-hosting, caching, and preloading of font files.

```
import localFont from '@next/font/local';

const myFont = localFont({ src: './my-font.woff2' });

<html className={myFont.className}>
```

You can customize every part of the font loading experience while still ensuring great performance and no layout shift, including the `font-display`, preloading, fallbacks, and more.

[Learn more about the new Font component](https://nextjs.org/docs/basic-features/font-optimization) or deploy an example to try it out.

## [`next/link`](https://nextjs.org/blog/next-13#nextlink)

`next/link` no longer requires manually adding `<a>` as a child.

This was added as an experimental option in [12.2](https://nextjs.org/blog/next-12-2) and is now the default. In Next.js 13, `<Link>` always renders an `<a>` and allows you to forward props to the underlying tag. For example:

```
import Link from 'next/link'

// Next.js 12: `<a>` has to be nested otherwise it's excluded
<Link href="/about">
  <a>About</a>
</Link>

// Next.js 13: `<Link>` always renders `<a>`
<Link href="/about">
  About
</Link>
```

[Learn more about the improved Link component](https://nextjs.org/docs/api-reference/next/link) or [deploy an example to try it out](https://vercel.com/templates/next.js/nextjs-boilerplate).

### [Upgrading `next/link` to Next.js 13](https://nextjs.org/blog/next-13#upgrading-nextlink-to-nextjs-13)

To upgrade your links to Next.js 13, we've provided a codemod that will automatically update your codebase. For example, this command would run the codemod on your `./pages` directory when run from the root:

```
npx @next/codemod new-link ./pages
```

[Learn more about the codemod](https://nextjs.org/docs/advanced-features/codemods) or check out the documentation.

## [OG Image Generation](https://nextjs.org/blog/next-13#og-image-generation)

Social cards, also known as open graph images, can massively increase the engagement rate of clicks on your content, with some experiments showing up to 40% better conversions.

Static social cards are time-consuming, error-prone, and hard to maintain. Because of this, social cards are often lacking or even skipped. Until today, dynamic social cards that need to be personalized and computed on the fly were difficult and expensive.

We've created a new library **`@vercel/og`** that works seamlessly with Next.js to generate dynamic social cards.

```
// pages/api/og.jsx

import { ImageResponse } from '@vercel/og';

export const config = {
  runtime: 'experimental-edge',
};

export default function () {
  return new ImageResponse(
    (
      <div
        style={{
          display: 'flex',
          fontSize: 128,
          background: 'white',
          width: '100%',
          height: '100%',
        }}
      >
        Hello, World!
      </div>
    ),
  );
}
```

This approach is **5x faster** than existing solutions by using Vercel Edge Functions, WebAssembly, and a brand new core library for converting HTML and CSS into images and leveraging the React component abstraction.

[Learn more about OG Image Generation](https://vercel.com/docs/concepts/functions/edge-functions/og-image-generation) or [deploy an example to try it out](https://vercel.com/new/clone?repository-url=https://github.com/vercel/examples/tree/main/edge-functions/vercel-og-nextjs&project-name=vercel-og-nextjs&repository-name=vercel-og-nextjs).

## [Middleware API Updates](https://nextjs.org/blog/next-13#middleware-api-updates)

In Next.js 12, we introduced Middleware to enable full flexibility with the Next.js router. We've heard your feedback on the initial API design and have added some additions to improve the developer experience and add powerful new functionality.

You can now more easily set headers on the request:

```
// middleware.ts
import { NextResponse } from 'next/server';
import type { NextRequest } from 'next/server';

export function middleware(request: NextRequest) {
  // Clone the request headers and set a new header `x-version`
  const requestHeaders = new Headers(request.headers);
  requestHeaders.set('x-version', '13');

  // You can also set request headers in NextResponse.rewrite
  const response = NextResponse.next({
    request: {
      // New request headers
      headers: requestHeaders,
    },
  });

  // Set a new response header `x-version`
  response.headers.set('x-version', '13');
  return response;
}
```

You can also now provide a response directly from Middleware, without having to `rewrite` or `redirect`.

```
// middleware.ts
import { NextRequest, NextResponse } from 'next/server';
import { isAuthenticated } from '@lib/auth';

// Limit the middleware to paths starting with `/api/`
export const config = {
  matcher: '/api/:function*',
};

export function middleware(request: NextRequest) {
  // Call our authentication function to check the request
  if (!isAuthenticated(request)) {
    // Respond with JSON indicating an error message
    return NextResponse.json(
      {
        success: false,
        message: 'Auth failed',
      },
      {
        status: 401,
      },
    );
  }
}
```

Sending responses from Middleware currently requires the `experimental.allowMiddlewareResponseBody` configuration option inside `next.config.js`.

## [Breaking Changes](https://nextjs.org/blog/next-13#breaking-changes)

-   The minimum React version has been bumped from 17.0.2 to 18.2.0.
-   The minimum Node.js version has been bumped from 12.22.0 to 14.6.0, since 12.x has reached end-of-life ([PR](https://github.com/vercel/next.js/pull/41482)).
-   The `swcMinify` configuration property was changed from `false` to `true`. See [Next.js Compiler](https://nextjs.org/docs/advanced-features/compiler) for more info.
-   The `next/image` import was renamed to `next/legacy/image`. The `next/future/image` import was renamed to `next/image`. A [codemod is available](https://nextjs.org/docs/advanced-features/codemods#next-image-to-legacy-image) to safely and automatically rename your imports.
-   The `next/link` child can no longer be `<a>`. Add the `legacyBehavior` prop to use the legacy behavior or remove the `<a>` to upgrade. A [codemod is available](https://nextjs.org/docs/advanced-features/codemods#new-link) to automatically upgrade your code.
-   Routes are no longer prefetched when the `User-Agent` is a bot.
-   The deprecated `target` option of `next.config.js` has been removed.
-   The supported browsers have been changed to drop Internet Explorer and target modern browsers. You can still use Browserslist to change targeted browsers.
    -   Chrome 64+
    -   Edge 79+
    -   Firefox 67+
    -   Opera 51+
    -   Safari 12+

To learn more, check out the [upgrade guide](https://nextjs.org/docs/upgrading).

## [Community](https://nextjs.org/blog/next-13#community)

Six years ago, we released Next.js to the public. We set out to build a zero-configuration React framework that simplifies your developer experience. Looking back, it's incredible to see how the community has grown, and what we've been able to ship together. Let's keep going.

Next.js is the result of the combined work of **over 2,400 individual developers**, industry partners like Google and Meta, and our core team. With over 3 million npm downloads per week and 94,000 GitHub stars, Next.js is one of the most popular ways of building the Web.

Special thanks to the Aurora team at Google Chrome who helped with the foundational research and experiments that led to this release.

This release was brought to you by the contributions of: @ijjk, @huozhi, @HaNdTriX, @iKethavel, @timneutkens, @shuding, @rishabhpoddar, @hanneslund, @balazsorban44, @devknoll, @anthonyshew, @TomerAberbach, @philippbosch, @styfle, @mauriciomutte, @hayitsdavid, @abdennor, @Kikobeats, @cjdunteman, @Mr-Afonso, @kdy1, @jaril, @abdallah-nour, @North15, @feedthejim, @brunocrosier, @Schniz, @sedlukha, @hashlash, @Ethan-Arrowood, @fireairforce, @migueloller, @leerob, @janicklas-ralph, @Trystanr, @atilafassina, @nramkissoon, @kasperadk, @valcosmos, @henriqueholtz, @nip10, @jesstelford, @lorensr, @AviAvinav, @SukkaW, @jaycedotbin, @saurabhburade, @notrab, @kwonoj, @sanruiz, @angeloashmore, @falsepopsky, @fmontes, @Gebov, @UltiRequiem, @p13lgst, @Simek, @mrkldshv, @thomasballinger, @kyliau, @AdarshKonchady, @endymion1818, @pedro757, @perkinsjr, @gnoff, @jridgewell, @silvioprog, @mabels, @nialexsan, @feugy, @jackromo888, @crazyurus, @EarlGeorge, @MariaSolOs, @lforst, @maximbaz, @maxam2017, @teobler, @Nutlope, @sunwoo0706, @WestonThayer, @Brooooooklyn, @Nsttt, @charlypoly, @aprendendofelipe, @sviridoff, @jackton1, @nuta, @Rpaudel379, @marcialca, @MarDi66, @ismaelrumzan, @javivelasco, @eltociear, and @hiro0218.