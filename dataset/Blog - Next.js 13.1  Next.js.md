Next.js 13.1 includes improvements to both the `pages/` (stable) and `app/` (beta) directories:

- [**`app` Directory (Beta) Improvements:**](https://nextjs.org/blog/next-13#improved-reliability-and-support-for-app-directory) Improved reliability and performance.
- [**Built-in Module Transpilation:**](https://nextjs.org/blog/next-13#built-in-module-transpilation-stable) Bringing `next-transpile-modules` capabilities into core.
- [**Edge Runtime (Stable):**](https://nextjs.org/blog/next-13#a-light-nodejs-runtime-for-the-edge-now-stable-for-api-routes) A light Node.js runtime for the Edge.
- [**Turbopack Updates:**](https://nextjs.org/blog/next-13#turbopack-improvements) Support for Tailwind CSS, `next/image`, `@next/font`, and more.
- [**Middleware Improvements:**](https://nextjs.org/blog/next-13#nextjs-advanced-middleware) Return responses and set request headers.
- [**SWC Import Resolution:**](https://nextjs.org/blog/next-13#import-resolution-for-smaller-bundles) For smaller JavaScript bundles when using barrel files.
- [Memory usage improvements, new templates, and more!](https://nextjs.org/blog/next-13#other-improvements)

Update today by running:

```
npm i next@latest react@latest react-dom@latest eslint-config-next@latest
```

## [Improved reliability and support for `app` directory](https://nextjs.org/blog/next-13#improved-reliability-and-support-for-app-directory)

In Next.js 13, we announced the new `app` directory (beta). This new routing and data fetching system can be incrementally adopted alongside your existing `pages` directory.

The `app` directory provides many benefits, including enhanced layouts, co-location of components, tests, and styles, component-level data fetching, and more. Thanks to your feedback and early testing, we've made several improvements to the reliability of the `app` directory:

- **No Layout Divs:** Previously, the `app` directory added additional `<div>` elements to scroll layouts into view when navigating. With 13.1, these extra elements are no longer created. The scrolling behavior is preserved.
- **TypeScript Plugin:** We've built a new TypeScript plugin that provides suggestions for page and layout configuration options, brings documentation directly into your IDE, and provides helpful usage hints around Server and Client Components (such as preventing the use of `useState` in Server Components). [Learn more](https://www.youtube.com/watch?v=pqMqn9fKEf8).
- **Reliability Improvements:** We've patched numerous bugs, including improved CSS modules support, correctly de-duplicating `cache()` and `fetch()` for layouts and pages, memory leaks, and more.
- **Less Client-Side JavaScript:** The `app` directory now includes `9.3kB` less client-side JavaScript than the `pages` directory. This baseline doesn't increase whether you add 1 or 1000 Server Components to your application. The React runtime is temporarily slightly larger, the increase is caused by the React Server Components runtime, which handles mechanics that Next.js previously handled. We are working to reduce this further.

|                     | pages/       | **app/**    | delta              |
| ------------------- | ------------ | ----------- | ------------------ |
| Total First Load JS | **Baseline** | **\-9.3kB** | **12.1%**_smaller_ |
| Next.js Runtime     | **Baseline** | **\-12kB**  | **56.8%**_smaller_ |
| React Runtime       | **Baseline** | **+2.7kB**  | **5.2%**_larger_   |

We're excited about continuing to make progress on the stability of the `app` directory. The [beta documentation](https://beta.nextjs.org/) for the `app` directory has had hundreds of updates [based on your feedback](https://vercel.com/blog/using-vercel-comments-to-improve-the-next-js-13-documentation).

## [Built-in module transpilation (stable)](https://nextjs.org/blog/next-13#built-in-module-transpilation-stable)

You can now mark dependencies from local packages (like monorepos) or from external dependencies (`node_modules`) to be transpiled and bundled. This built-in support replaces the popular `next-transpile-modules` package.

```
/** @type {import('next').NextConfig} */
const nextConfig = {
  transpilePackages: ['@acme/ui', 'lodash-es'],
};

module.exports = nextConfig;
```

We are thankful to Pierre de la Martinière ([@martpie](https://twitter.com/martpie_)) for their work on this package and their assistance in helping ensure the built-in support met the community's needs.

## [Import resolution for smaller bundles](https://nextjs.org/blog/next-13#import-resolution-for-smaller-bundles)

Many popular npm packages make use of “barrel files” to provide a single file that re-exports other modules. For example:

```
// @acme/ui/index.ts
export { default as Button } from './dist/Button';
export { default as Slider } from './dist/Slider';
export { default as Dropdown } from './dist/Dropdown';
```

This allows consumers of the package to use named exports in a single line:

```
import { Button, Slider, Dropdown } from '@acme/ui';
```

While bundlers understand these barrel files and can remove unused re-exports (called "dead code elimination"), this process involves parsing/compiling all re-exported files. In case of published libraries some npm packages ship barrel files that have thousands of modules re-exported, which slows down compile times. These libraries recommended `babel-plugin-transform-imports` to avoid this issue, but for those using SWC, there was no previous support. We've added a new SWC transform built into Next.js called `modularizeImports`.

This new setting enables the SWC transform which changes your import statements based on a defined pattern. For example, the above code for using three components would be automatically converted to use direct imports, without the developer needing to write this code manually:

```
// Before (with barrel file)
import { Button, Slider, Dropdown } from '@acme/ui';

// After (with modularized imports from plugin)
import Button from '@acme/ui/dist/Button';
import Slider from '@acme/ui/dist/Slider';
import Dropdown from '@acme/ui/dist/Dropdown';
```

This transformation is possible with the `modularizeImports` option in `next.config.js`:

```
// next.config.js
module.exports = {
  modularizeImports: {
    '@acme/ui': {
      transform: '@acme/ui/dist/{{member}}',
    },
  },
};
```

Leveraging this transform with `@mui/icons-material` or `lodash` allows skipping compilation of unused files. [Learn more](https://nextjs.org/docs/advanced-features/compiler#modularize-imports).

[View a demo](https://twitter.com/nextjs/status/1610288783311732737) to see this in action.

## [A light Node.js runtime for the edge, now stable for API routes](https://nextjs.org/blog/next-13#a-light-nodejs-runtime-for-the-edge-now-stable-for-api-routes)

The Edge Runtime inside Next.js uses a strict subset of Node.js APIs (like `Request`, `Response`, and more) which are compatible with Edge computing platforms like Vercel or when self-hosting. These APIs run everywhere, including in the browser, allowing developers to learn them once and write everywhere.

```
// pages/api/hello.ts

// "experimental-" prefix is no longer needed
export const config = {
  runtime: 'edge',
};

export default function handler(req: Request) {
  return new Response('Hello World');
}
```

Next.js Middleware already uses this light edge runtime by default for better performance. As Middleware can run before every request in your application, having a lightweight runtime is critical to ensure low latency. In Next.js 12.2, we added the ability to optionally use this runtime for [API Routes](https://nextjs.org/blog/next-12-2#edge-api-routes-experimental) as well.

With 13.1, the Edge Runtime inside Next.js is **now stable** for API routes. When self-hosted, Middleware and API Routes using the Edge Runtime will run as a single-region workload by default as part of `next start`. On Vercel, Next.js Middleware and API Routes are [deployed globally using Vercel Edge Functions](https://vercel.com/docs/concepts/next.js/overview) for the lowest possible latency. Vercel Edge Functions are also [now generally available](https://vercel.com/blog/edge-functions-generally-available).

## [Turbopack improvements](https://nextjs.org/blog/next-13#turbopack-improvements)

After releasing the [Turbopack](https://turbo.build/pack) alpha with Next.js 13, we've been focused on improving reliability, adding support for the most requested features, and defining plans for plugins and usage in other frameworks.

Since Next.js 13.0.0, Turbopack:

- Supports PostCSS, including Tailwind CSS
- Supports `next/image`
- Supports `@next/font` (Google Fonts)
- Supports loading CSS from dynamic `import()` statements
- Supports CSS source maps (thank you `@ahabhgk` for [their contribution](https://github.com/vercel/turbo/pull/2839))
- Improved error handling in `next dev` error overlay
- Improved memory usage
- Improved CSS modules support
- Improved chunking algorithm for HMR updates
- Improved reliability for HMR source maps

We are thankful to Evan You and the Vite community for their feedback and contributions to ensure the Turbopack benchmarks are as accurate as possible. We've worked together with the Vite team to validate the latest Turbopack benchmarks and make numerous improvements to our testing methodology.

As a result of this collaboration, we now use a more accurate metric which includes time spent in React's update mechanism. We were able to improve React Fast Refresh time by 30ms in Turbopack as well as Next.js 13.1 on webpack. We've also added a new benchmark for using Vite with SWC, which shows improved performance compared to using the default Vite with Babel. View the [updated benchmarks](https://turbo.build/pack/docs/benchmarks) or read about the [testing methodology](https://turbo.build/blog/turbopack-benchmarks).

Try out the Turbopack alpha version today in Next.js 13 with `next dev --turbo`. If you have any feedback let us know on the [GitHub Discussion](https://github.com/vercel/turbo/discussions/2312).

## [Next.js advanced Middleware](https://nextjs.org/blog/next-13#nextjs-advanced-middleware)

Thanks to your feedback, we're making Next.js Middleware more powerful than ever. With 13.1, you can now return responses from Middleware, as well as set headers on the request.

These API improvements give you powerful new flexibility to customize every part of the Next.js routing lifecycle. The `experimental.allowMiddlewareResponseBody` configuration option inside `next.config.js` is no longer required.

You can now more easily set headers on the request, as well as respond directly without having to `rewrite` or `redirect`:

```
// middleware.ts
import { NextResponse } from 'next/server';

export function middleware(request: Request) {
  // Check if a user has access...
  if (!isAuthorized(request)) {
    return NextResponse.json({ message: 'Unauthorized' });
  }

  // Add a new header, this will change the incoming request headers
  // that you can read in getServerSideProps and API routes
  const requestHeaders = new Headers(request.headers);
  requestHeaders.set('x-version', '13.1');

  return NextResponse.next({
    request: {
      // Apply new request headers
      headers: requestHeaders,
    },
  });
}
```

Learn more about [Next.js advanced Middleware](https://nextjs.org/docs/advanced-features/middleware).

## [Other improvements](https://nextjs.org/blog/next-13#other-improvements)

- `@next/font` now supports adding multiple font weights and styles in the same font declaration. [Learn more](https://nextjs.org/docs/basic-features/font-optimization#google-fonts).
- `next/dynamic` now uses React primitives `lazy()` and `<Suspense>`. The previous `suspense` option is no longer required. With these changes, `next/dynamic` is now compatible with the `app` directory.
- `create-next-app` has been updated with a new design, now including [`@next/font`](https://nextjs.org/docs/basic-features/font-optimization) by default for automatic self-hosting of fonts with zero layout shift. Try it out with `npx create-next-app@latest` or [deploy the template](https://vercel.com/templates/next.js/nextjs-boilerplate).
- We've made numerous improvements to the [App Directory Playground](https://app-dir.vercel.app/), which showcases some of the latest features and conventions of the `app` directory (beta) in Next.js 13. [Deploy your own](https://vercel.com/templates/next.js/app-directory).
- We've created a [high-performance image gallery template](https://vercel.com/templates/next.js/image-gallery-starter), which includes image placeholders, lazy loading, automatic optimization, keyboard support, and more. [Deploy your own](https://vercel.com/templates/next.js/image-gallery-starter).
- We've created a resource for understanding how to [migrate a large, open-source React and Express.js application](https://vercel.com/blog/migrating-a-large-open-source-react-application-to-next-js-and-vercel) to Next.js, including a detailed walkthrough and links back to specific commits.
