Next.js is like React with benefits, in that it delivers all the features of React with ease-of-use conventions and a well-defined client-server stack. Next.js 13 is the newest version, released by Vercel at the [Next.js conference](https://nextjs.org/conf) in October 2022. It brings a slew of new features, including a bundler called Turbopack and support for several React-incubated optimizations like React Server Components and streaming rendering.

All told, Next.js 13 is a significant milestone, bringing together advancements in React and Next itself in a pleasantly usable developer experience package. This release also packs in considerable behind-the-scenes optimization. Let's take a tour of what's new in Next.js 13.

Table of Contents

- [The new Turbopack bundler](https://www.infoworld.com/article/3679489/the-best-new-features-in-nextjs-13.html#toc-1)
- [Using the Turbopack in Next.js 13](https://www.infoworld.com/article/3679489/the-best-new-features-in-nextjs-13.html#toc-2)
- [The new /app directory](https://www.infoworld.com/article/3679489/the-best-new-features-in-nextjs-13.html#toc-3)
- [Layouts in Next.js 13](https://www.infoworld.com/article/3679489/the-best-new-features-in-nextjs-13.html#toc-4)
- [React Server Components](https://www.infoworld.com/article/3679489/the-best-new-features-in-nextjs-13.html#toc-5)

Show More

## The new Turbopack bundler

[Turbopack](https://turbo.build/pack) is a new general-purpose JavaScript bundler and a major feature in Next.js 13. It is intended as a Webpack replacement, and although it’s released as alpha, you can use Turbopack now as the dev-mode bundler from Next.js 13 forward. Turbopack is a new entrant into the bundler competition, where several contenders have vied to overcome Webpack’s dominance.

Turbopack is written in [Rust](https://www.infoworld.com/article/3267624/whats-new-in-the-rust-language.html), which seems to be the go-to choice for systems-oriented tooling these days. Rust’s inherent speed is one reason underlying Turborepo’s performance as compared with other build tools. (Rust is [something like C++](https://stackoverflow.blog/2020/01/20/what-is-rust-and-why-is-it-so-popular/), but with more memory safety.) Interestingly, the bundler space has been very active lately, with the Vite build tool gaining mindshare as the successor to Webpack. Vite is written in [Go](https://www.infoworld.com/article/3198928/whats-the-go-programming-language-really-good-for.html), a language of similar vintage to Rust. But Rust seems to [have the edge on efficiency](https://blog.logrocket.com/when-to-use-rust-when-to-use-golang).

![](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABQAAAAUCAYAAACNiR0NAAAABGdBTUEAALGPC/xhBQAAACBjSFJNAAB6JgAAgIQAAPoAAACA6AAAdTAAAOpgAAA6mAAAF3CculE8AAAACXBIWXMAAA3XAAAN1wFCKJt4AAABWWlUWHRYTUw6Y29tLmFkb2JlLnhtcAAAAAAAPHg6eG1wbWV0YSB4bWxuczp4PSJhZG9iZTpuczptZXRhLyIgeDp4bXB0az0iWE1QIENvcmUgNS40LjAiPgogICA8cmRmOlJERiB4bWxuczpyZGY9Imh0dHA6Ly93d3cudzMub3JnLzE5OTkvMDIvMjItcmRmLXN5bnRheC1ucyMiPgogICAgICA8cmRmOkRlc2NyaXB0aW9uIHJkZjphYm91dD0iIgogICAgICAgICAgICB4bWxuczp0aWZmPSJodHRwOi8vbnMuYWRvYmUuY29tL3RpZmYvMS4wLyI+CiAgICAgICAgIDx0aWZmOk9yaWVudGF0aW9uPjE8L3RpZmY6T3JpZW50YXRpb24+CiAgICAgIDwvcmRmOkRlc2NyaXB0aW9uPgogICA8L3JkZjpSREY+CjwveDp4bXBtZXRhPgpMwidZAAABOklEQVQ4Ea2V3U7DMAxGV8QN3OyBB+oDdK/L75DKOV0cmdC0EizSp7iJfWwvbXaY5/mIJnUoA/s+7N6cfSKe+XgoD1/ML+gpANhdaN4zBr2iC5ruCuCT+RGdWHx2bRgGk/yCuuaePsX3hPmALq4tww1kFscbWqBuYldoYxujr8O5dpeh2eEHlIAMbmHVt62g77ikvbZZKmLqdLOTfSwsk49o96eJltuWItDTt3Kl7XCvtoldYyP5GnTEMQDv2MrhWq56HRZknKsDtlV9oBjam5XFexi8285kz9WNPP+95QaWXx+h7aF0X/61w8gwA/MBjDzH6a9DcchttrB6APHj4t/1yazlQ2fh/5+emQF5BXVhqbqtbq6XAyAv1zjN3S8A3wyNu5DlhTFZ3Rl59+3CNio1VsZZ4E3/Ar4BuSbVUBIuWisAAAAASUVORK5CYII=)

Lenovo Lnit Mental Full Fc Mp4 1280x720p

0 of 30 secondsVolume 0%

This ad will end in 19

00:11

00:18

00:30

Turbopack also has architectural changes like clever use of caching, which improves the handling of source changes in an optimized way. The basic premise is that Turbopack holds a granular model of the changes that were already built and only builds those that are required to reflect ongoing changes.

Ever since Webpack first introduced the JavaScript world to the concept of a convention-over-configuration, all-in-one build-pipeline, there has been competition to see who can develop the best JavaScript-bundling tool. Developers want the fastest, most feature-rich tool they can find, capable of tackling edge cases and handling happy paths with minimal fuss.

## Using the Turbopack in Next.js 13

It’s easy to create a new Next.js app via `create-next-app` and use Turbopack. You use the `--example` switch and give it the `with-turbopack` argument, as shown in Listing 1.

#### Listing 1. Start a new turbopack-built Next App

```

npx create-next-app --example with-turbopack
```

If you look at `package.json`, you’ll see this is reflected in a small change in how the `dev` script works:

#### Listing 2. Dev mode script with turbo

```

"dev": "next dev --turbo"
```

Turbopack works as an opt-in replacement for Next.js's devmode server for the moment, but there are [big plans on the horizon](https://turbo.build/pack/docs/roadmap), including frameworks beyond React. Svelte and Vue have both been mentioned by name. Presumably, Turbopack will become the default devmode tool, and also the production build tool at some point in the future.

When you run `npm run dev`, you’ll see a screen like the one below.

[![The Next.js 13 turbopack dev mode welcome screen.](https://images.idgesg.net/images/article/2022/11/nextjs-fig1-100934437-large.jpg?auto=webp&quality=85,70)](https://images.idgesg.net/images/article/2022/11/nextjs-fig1-100934437-orig.jpg?auto=webp&quality=85,70 "<div class='credit'>IDG</div>

<p>The Next.js 13 Turbopack dev mode welcome screen. The left-hand menu shows several examples of new functionality in Next.js 13.</p>
") IDG

The Next.js 13 Turbopack dev mode welcome screen. The left-hand menu shows several examples of new functionality in Next.js 13.

Although you can see the impact of Turbopack’s performance most in large-scale apps, a fun little experiment shows the difference. Try running `dev` with `--turbo` enabled versus without, as shown in Listing 3. As you can see, even for the humble starter app, the start time drops from over 1000 milliseconds to around 8.

#### Listing 3. Turbopack devmode start time

```

// with --turbo
ready - started server on 0.0.0.0:3000, url: http://localhost:3000
event - initial compilation 7.216ms

// without --turbo
ready - started server on 0.0.0.0:3000, url: http://localhost:3000
event - compiled client and server successfully in 1118 ms (198 modules)
```

## The new /app directory

Now let’s look at our directory layout, where you will notice the new `/app` directory. This is a new feature of Next.js 13. Basically, everything in the `/app` directory participates in the next generation of React and Next.js features.

The `/app` directory lives next to the familiar `/pages` directory and supports more advanced routing and layout capabilities. Routes that match in both `/pages` and `/app` will go to `/app`, so you can gradually supersede existing routes.

The basic routing in `/app` is similar to `/pages` in that the nested folders describe the URL path, so `/app/foo/bar/page.js` becomes `localhost:3000/foo/bar` in our `dev` setup.

### Enabling the /app directory

The `/app` directory is still a beta feature, so to use it you have to enable experimental features in `next.config.js`, like so:

#### Listing 4. How to enable experimental features

```

experimental: {
    appDir: true
  }
```

Note that this was done for us when we scaffolded the new project with `create-next-app`.

## Layouts in Next.js 13

One of the superpowers `/app` has over `/pages` is support for complex nested layouts. Every branch in your URL hierarchy can define a layout that is shared with its children (_aka_, leaf nodes). Moreover, the layouts preserve their state between transitions, avoiding the expense of re-rendering content in shared panels.

Each directory considers the `page.tsx/jsx` file as the content, and `layout.tsx/jsx` defines the template for both that page and the subdirectories. So, creating nested templates becomes simple. Moreover, the framework is smart enough to not re-render sections of the page that don’t change, so navigation will not repaint layouts that aren’t affected.

For example, let’s say we wanted to have a `/foo/*` directory where all the children are surrounded by a white border. We could drop a `layout.tsx` file into a `/app/foo` directory, something like Listing 5.

#### Listing 5. app/foo/layout.tsx

```

export default function FooLayout({ children }) {
  return <section style={{borderWidth: 1, borderColor:'white'}}>{children}</section>;
}
```

In Listing 5, notice the component destructures the “children” property and uses that to place the content inside the template. Here, the layout is just a section property with an inline style giving a white border. The page file in `/app/foo` will have its contents rendered where the `{children}` token is found in the layout file. By default, layout files create nested templates, so a route that matches a subdirectory of `/app/foo/*` will also have their content placed within the `{children}` element of `/app/foo/layout.*`.

## React Server Components

By default, all components in `/app/*` are [React Server Components](https://www.infoworld.com/article/3648191/hands-on-with-react-server-components.html). Basically, server components are React’s answer to the ongoing question of how to [improve hydration](https://www.infoworld.com/article/3661810/reactive-javascript-the-evolution-of-front-end-architecture.html) in front-end apps. Much of the work in rendering components is handled on the server and a minimalist, cacheable JavaScript profile is shipped to the client to handle reactivity.

Sometimes, when using client-side hooks like `useState` or `useEffect` (where the server can’t do the work beforehand), you need to tell React it's a client-side component. You do this by adding a `‘use client’` directive to the first line of the file. We've previously used filename extensions like `.client.js` and `.server.js` to designate a client component that uses client-side hooks, but now you must use the `‘use client’` directive at head of `/app` components.

## Streaming render and suspense

[Streaming](https://github.com/reactwg/react-18/discussions/37) is another newer React optimization enabled by the new [concurrent render engine](https://www.infoworld.com/article/3610457/how-to-use-reacts-concurrent-mode.html). It’s a fundamental change in how the React engine works. The basic idea is that the UI can be divided into sections, and sections that depend on data can define loading states while they load the data concurrently. Meanwhile, sections that do not depend on data can receive their content right away for immediate display.

You will primarily use this feature with the `<Suspense>` component. In essence, `<Suspense>` says, _display this loading content while the real content is in a loading state, then show the real data-driven content when ready_. Because the UI is not blocked while this is happening and each `<Suspense>` happens concurrently, developers have a consistent and simple way to define layouts that are optimized and responsive, even with many data-dependent sections.

Next.js 13’s `/app` directory means you by default can use streaming and `<Suspense>`. Next’s back-end server implements the API that drives the loading states. The benefits are that loading states can be rendered quickly, hydrated content can be displayed as it becomes available concurrently, and the UI remains responsive while segments are loading.

These benefits are especially pronounced when the network is slow or unreliable, for example with mobile. This new feature improves both user experience and developer experience. Developers will notice that data fetching is more consistent and standard. Adopting best practice is not only simpler, but is the default.

## The new loading convention

There is a new `loading.js` convention in Next.js 13. It lives in a route directory of `/app` and acts like a `<Suspense>` for the entire route section. (Under the hood, Next.js actually applies a `<Suspense&gt`; boundary.) So, whatever is defined in the folder for `loading.js` will show while the actual content is being rendered, with the same benefits of using suspense directly.

You can see this convention in action by opening _http://localhost:3000/streaming_ in our demo app. This will display the `app/streaming/loading.tsx` file shown in Listing 6 while the actual content is loaded.

#### Listing 6. app/streaming/loading.tsx

```

import { SkeletonCard } from '@/ui/SkeletonCard';
export default function Loading() {
  return (
    <div className="space-y-4">
      <div className="grid grid-cols-3 gap-6">
        <SkeletonCard isLoading={true} />
        <SkeletonCard isLoading={true} />
        //...
    </div>
  );
}
```

Basically, the `loading.tsx` file in Listing 5 shows a grid of SkeletonCard components. A _skeleton card_ is the pulsing media card that holds the places for the real content that is ultimately loaded by the route.

## Better data fetching in Next.js 13

The Next.js data loading methods ([getServerSideProps](https://nextjs.org/docs/basic-features/data-fetching/get-server-side-props), [getStaticProps](https://nextjs.org/docs/basic-features/data-fetching/get-static-props), and [getInitialProps](https://nextjs.org/docs/api-reference/data-fetching/get-initial-props)) are now deprecated in favor of a newer approach to data fetching.

The first convention is to load data on the server, which has become simpler because all the components are server components by default. This eliminates the tendency to bounce data requests from the client off the server, when you really only need to directly hit the data store from the server and send the rendered UI to the client. See the [Next.js documentation](https://beta.nextjs.org/docs/data-fetching/fundamentals#fetching-data-with-server-components) for a longer account of the reasoning behind preferring server-side data fetching.

Data fetching in the `/app` directory has to work with streaming and suspense. Components should make their own data requests, instead of parents passing in the data—even if that data is shared between components. The framework itself will avoid redundant requests, and it will ensure only the minimal requests are made and handed to the right components. The fetching API will also cache results for reuse.

All of this makes for a simpler architecture for data fetching that is still optimized. Developers can think less about data fetching performance and just grab data as it's needed, in the component that needs it.

The new approach means you can use the asynchronous Fetch API that we are familiar with directly in server components. (React and Next extend the API to handle deduping and caching.) You can also define async server components; for example, `export default async function Page()`. See the [Next.js blog](https://nextjs.org/blog/next-13#data-fetching) for more about the new fetch API.

The overall effect of all these improvements is a simpler application architecture that still benefits from behind-the-scenes performance optimization.

## Conclusion

That’s quite a lot of action in Next.js 13—and there is more that I did not cover. Other new features include updates to the next/image component and a new font-loading system. Overall, Next.js 13 continues the tradition of delivering an all-in-one, React-with-benefits framework that makes it easier to take advantage of a variety of features.

Still, this release is special due to long-term innovations like streaming and server components. When united with Vercel’s infrastructure, Next.js 13 offers considerable ease of deployment and gives us a glimpse of the reactive development experience of the future.
