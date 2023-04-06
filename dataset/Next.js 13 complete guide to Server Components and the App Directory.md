## A tutorial on how to use Next.js 13 with server components and the app directory.

![Cover Image for Next.js 13: complete guide to Server Components and the App Directory](https://makerkit.dev/assets/images/posts/nextjs13-tutorial.webp)

Hi and welcome! In this tutorial, we'll learn how to use Next.js 13 with server components and the app directory.

## [](https://makerkit.dev/blog/tutorials/nextjs13#what-are-server-components)What are Server Components?

Server Components are a new type of React components that run on the server and return compiled JSX that is sent to the client. Next.js, with its new app directory released in Next.js 13, fully embraced Server Components by making them the default type components.

This is a big shift from traditional React components that run both on the server and on the client. In fact, as we have specified, React Server components do not execute on the client.

As such, there are some constraints to using Server Components that we need to keep in mind:

-   Server components cannot use browser-only APIs
-   Server components cannot use React hooks
-   Server components cannot use Context

___

So, what are they for?

React Server Components are useful for rendering the skeleton of a page, while leaving the interactive bits to the so-called "client components".

Despite their name, "client components" (which, IMHO, is unfortunate) are also server-rendered, and they run on both the server and the client.

React Server Components can be useful because they allows us to:

-   render pages faster
-   reduce the amount of JavaScript that needs to be sent to the client
-   improve the routing performance of server-rendered pages

In short, we use Server Components to fetch data from the server and render the skeleton of a page: then, we can pass the data to the "client components".

### [](https://makerkit.dev/blog/tutorials/nextjs13#server-components-vs-client-components)Server Components vs Client Components

As we have seen, Server Components are useful for rendering the skeleton of a page, while Client Components are the components as we know them today.

[This comparison in the Next.js docs](https://beta.nextjs.org/docs/rendering/server-and-client-components#when-to-use-server-vs-client-components) is a good way to understand the difference between the two.

#### [](https://makerkit.dev/blog/tutorials/nextjs13#defining-server-components)Defining Server Components

Server components do not need a notation to be defined as such: server components are the default components when rendered in the app directory.

We cannot use React hooks, Context, or browser-only APIs in Server Components. However, we can use Server Components only APIs, such as `headers`, `cookies`, etc.

Server components **can import client components**.

#### [](https://makerkit.dev/blog/tutorials/nextjs13#defining-client-components)Defining Client components

On the contrary, in the Next.js app directory, we need to specifically define **client components**.

We can do this by specifying the `use client` pragma at the top of the file:

```
'use client';

export default function ClientComponent() {
  return <div>Client Component</div>;
}
```

When we use client components, we can use React hooks, Context, and browser-only APIs. However, we cannot use some Server Components only APIs, such as `headers`, `cookies`, etc.

Client components **cannot import server components**, but you can pass a Server Component as a child or prop of a Client Component.

## [](https://makerkit.dev/blog/tutorials/nextjs13#app-directory)App directory

The new "app" directory released in Next.js 13 is an experimental new way to build Next.js apps. It coexists with the `pages` directory, and we can use it to incrementally migrate an existing project to the new directory structure.

This new directory structure is not just a new way to write apps, it's a whole new routing system underneath, much more powerful than the current one.

### [](https://makerkit.dev/blog/tutorials/nextjs13#file-structure)File Structure

What does the new Next.js 13 file structure look like? Let's take a look at the example app we'll be using in this tutorial.

Below is an example of a Next.js 13 app with the new `app` directory:

```
- app
  - layout.tsx
  - (site)
    - page.tsx
    - layout.tsx
  - app
    - dashboard
      - page.tsx
    - layout.tsx
```

As you can see, the name of the file reflects the type of component it is. For example, `layout.tsx` is a layout component, while `page.tsx` is a page component, and so on.

Don't worry, we will go through all the different types of components in the next sections.

#### [](https://makerkit.dev/blog/tutorials/nextjs13#colocation-of-components)Colocation of Components

One important side-effect of the new `app` directory is that it allows us to colocate components. Since filenames are conventional, we can define any file in the `app` directory without these becoming pages components.

For example, we could place our components for a specific page right in the folder where it's defined:

```
- app
  - (site)
    - components
      - Dashboard.tsx
    - page.tsx
```

Why is `(site)` in parentheses? By using parenthesis, we make the directory `site` "pathless", which means we can create new layouts, loading files, and pages in the `site` directory, without adding a new path segment to the routing.

All pages under `(site)` will be accessed from the root path `/`: for example, the page `app/(site)/page.tsx` will be accessible at `/`.

### [](https://makerkit.dev/blog/tutorials/nextjs13#layouts)Layouts

Layouts are one of the biggest new functionality made possible by the new App routing. Layouts are foundational components that wrap pages: this is not only useful for displaying a common UI across pages, but also to reuse data-fetching and logic.

Next.js needs one root layout component:

```
export default async function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang={'en'}>
      <body>{children}</body>
    </html>
  );
}
```

Layouts are defined using the convention `layout.tsx` in the `app` directory. Next.js will automatically wrap all pages within the folder where the layout is defined.

For example, if we have a layout defined in `app/(site)/layout.tsx`, Next.js will wrap all pages in the `app/(site)` directory with this layout:

```
export default async function SiteLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <div>
      <main>
        {children}
      </main>
    </div>
  );
}
```

All the pages in the `app/(site)` directory will be wrapped with the `SiteLayout` component.

One way to use Layout components is to load data needed in all pages in the layout component. For example, we could load the user's profile in the layout component, and pass it to the page components.

To fetch data, we can use the new `use` hook, an experimental hook in React that uses `Suspense` to fetch data on the server.

```
export default async function SiteLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  const data = use(getData());

  return (
    <div>
      <header>
        { data.user ? <ProfileDropown /> : null }
      </header>

      <main>
        {children}
      </main>
    </div>
  );
}

function getData() {
  return fetch('/api/data').then(res => res.json());
}
```

In the example above:

1.  we fetch the data in the layout component using the `use` hook
2.  we conditionally render the `ProfileDropdown` component based on the data.user property

#### [](https://makerkit.dev/blog/tutorials/nextjs13#reading-cookies-and-headers)Reading Cookies and Headers

If you're using a Server Component, you can read cookies and headers using from the `next/headers` package. At the time of writing, we can only use these functions to read their values, but not to set or delete them.

```
import { cookies } from 'next/headers';

export async function Layout(
  { children }: { children: React.ReactNode },
) {
  const lang = cookies.get('lang');

  return (
    <html lang={lang}>
      <body>
        {children}
      </body>
    </html>
  );
}
```

If you feel like something is missing, don't worry, it's not just you. In fact, unlike `getServerSideProps`, we do not have access to the `request` object. This is why Next.js is exposing these utilities to read data from the request.

I may be wrong, but I feel like this is still limiting.

#### [](https://makerkit.dev/blog/tutorials/nextjs13#redirecting-from-layouts)Redirecting from Layouts

In layouts, we can also redirect users to a different page.

For example, if we want to redirect users to the login page if they are not authenticated, we can do it in the layout component:

```
import { use } from 'react';
import { redirect } from 'next/navigation';

function AuthLayout(
  props: React.PropsWithChildren,
) {
  const session = use(getSession());

  if (session) {
    return redirect('/dashboard');
  }

  return (
    <div className={'auth'}>
      {props.children}
    </div>
  );
}


function getSession() {
  return fetch('/api/session').then(res => res.json());
}
```

If you have more complex setups, you can externalize the logic to a separate function. For example, we call it `loadSession`:

```
async function loadSession() {
  const session = await getSession();

  if (session) {
    return {
      redirect: true,
      destination: '/dashboard',
    };
  }

  const data = await getPageData();

  return {
    data,
  };
}
```

Loader functions using this convention will either return `{ redirect: true, destination: string }` or `{ data: unknown }`.

Now, we can use the `loadSession` function in the layout component:

```
import { use } from 'react';
import { redirect } from 'next/navigation';

function AuthLayout(
  props: React.PropsWithChildren,
) {
  const response = use(loadSession());

  if ('redirect' in response) {
    return redirect(response.destination);
  }

  const data = response.data;

  // do something with data

  return (
    <div className={'auth'}>
      {props.children}
    </div>
  );
}
```

This is a useful pattern since it allows us to reuse the same logic in multiple places, and gracefully handle redirects in a single place.

### [](https://makerkit.dev/blog/tutorials/nextjs13#pages)Pages

To define pages in the new app directory, we use the special convention `page.tsx`. That means, if we want to define a page in the `app` directory, we need to name the file `page.tsx`.

For example, if we want to define the home page of your website, we can place the page in the `app/(site)` directory and name it `page.tsx`:

```
function SitePage() {
  return <div>Site Page</div>;
}

export default SitePage;
```

#### [](https://makerkit.dev/blog/tutorials/nextjs13#page-metadata-and-seo)Page Metadata and SEO

To specify the metadata of a page, we can export the constant `metadata` property in the `page.tsx` file:

```
export const metadata = {
  title: 'Site Page',
  description: 'This is the site page',
};
```

If you need to access dynamic data, you can use the `generateMetadata` function:

```
export async function generateMetadata({ params, searchParams }) {
  return { title: '...' };
}
```

Check out the [Next.js documentation for the full list of supported metadata properties](https://beta.nextjs.org/docs/api-reference/metadata).

#### [](https://makerkit.dev/blog/tutorials/nextjs13#generating-static-pages)Generating Static Pages

To generate a list of static pages to be used with dynamic parameters, we can use the `generateStaticParams` function:

```
export async function generateStaticParams() {
  const posts = await getPosts();

  return posts.map((post) => ({
    slug: post.slug,
  }));
}
```

Check out the full documentation for [generating static paths](https://beta.nextjs.org/docs/api-reference/generate-static-params).

### [](https://makerkit.dev/blog/tutorials/nextjs13#loading-indicators)Loading Indicators

When navigation between pages, we may want to display a loading indicator. To do this, we can use the `loading.tsx` file which we can define in every directory:

```
export default function Loading() {
  return <div>Loading...</div>;
}
```

Here you can add any component you want to display while the page is loading, such as a top bar loader, or a loading spinner, or both.

### [](https://makerkit.dev/blog/tutorials/nextjs13#error-handling)Error Handling

At the moment, you can define a "not found" page using the convention `not-found.tsx`:

```
export default function NotFound() {
  return (
    <>
      <h2>Not Found</h2>
      <p>Could not find requested resource</p>
    </>
  );
}
```

This file will only be displayed if used in conjunction with the `notFound` function. This is why it's still recommended to use custom 400 and 500 pages using the old `pages` directory.

#### [](https://makerkit.dev/blog/tutorials/nextjs13#custom-404-and-500-pages)Custom 404 and 500 pages

At the time of writing, we need to stick with the regular `pages` directory to define custom 404 and 500 pages. This is because Next.js does not support custom 404 and 500 pages in the `app` directory.

### [](https://makerkit.dev/blog/tutorials/nextjs13#fonts)Fonts

We can use the package `next/font` to load fonts in our application.

To do so, we need to define a client component, and import it in the root layout `app/layout.tsx` file:

```
'use client';

import { Inter } from 'next/font/google';
import { useServerInsertedHTML } from 'next/navigation';

const heading = Inter({
  subsets: ['latin'],
  variable: '--font-family-heading',
  fallback: ['--font-family-sans'],
  weight: ['400', '500'],
  display: 'swap',
});

export default function Fonts() {
  useServerInsertedHTML(() => {
    return (
      <style
        dangerouslySetInnerHTML={{
          __html: `
          :root {
            --font-family-sans: '-apple-system', 'BlinkMacSystemFont',
              ${sans.style.fontFamily}, 'system-ui', 'Segoe UI', 'Roboto',
              'Ubuntu', 'sans-serif';

            --font-family-heading: ${heading.style.fontFamily};
          }
        `,
        }}
      />
    );
  });

  return null;
}
```

After that, we can import the `Fonts` component in the root layout:

```
import Fonts from '~/components/Fonts';

export default async function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html>
      <Fonts />

      <body>{children}</body>
    </html>
  );
}
```

### [](https://makerkit.dev/blog/tutorials/nextjs13#api-routes)API Routes

The new app directory also supports API routes. The convention to define an API route is to create a file named `route.tsx` in the `app` directory.

API routes now use the standard `Request` object rather than the `express`\-like `req` and `res` objects.

When we define an API route, we can export the handler for the methods we want to support. For example, if we want to support the `GET` and `POST` methods, we can export the `GET` and `POST` functions:

```
import { NextResponse } from 'next/server';

export async function GET() {
  return NextResponse.json({ hello: 'world' });
}

export async function POST(
  request: Request
) {
  const body = await request.json();
  const data = await getData(body);

  return NextResponse.json(data);
}
```

If we want to manipulate the response, for example by setting cookies, we can use the `NextResponse` object:

```
export async function POST(
  request: Request
) {
  const organizationId = getOrganizationId(resizeTo());
  const response = NextResponse.json({ organizationId });

  response.cookies.set('organizationId', organizationId, {
    path: '/',
    httpOnly: true,
    sameSite: 'lax',
  });

  return response;
}
```

In API routes, just like in Server Components, we can also redirect users using the `redirect` function imported from `next/navigation`:

```
import { redirect } from 'next/navigation';

export async function GET(
  request: Request
) {
  return redirect('/login');
}
```

#### [](https://makerkit.dev/blog/tutorials/nextjs13#handling-webhooks)Handling Webhooks

Handling webhooks is a common use case for API routes, and getting the raw body request is now much simpler. In fact, we can get the raw body request by using the `request.text()` method:

```

export async function POST(
  request: Request
) {
  const rawBody = await request.text();

  // handle webhook here
}
```

## [](https://makerkit.dev/blog/tutorials/nextjs13#conclusion)Conclusion

In this article, we learned how to use the new experimental app directory in Next.js 13.

The patterns and conventions we learned in this article are still experimental, and may change in the future. However, they are already very useful, and we can already start using them in our projects.

Did you know you can our **Next.js 13 SaaS Starter Kit to build your own SaaS app?** It's a fully-featured starter kit that includes everything you need to build a SaaS app with Next.js 13, including authentication, billing, and more.

Our [Supabase Next.js 13 SaaS Starter](https://makerkit.dev/next-supabase) uses [Supabase](https://supabase.com/) for the backend functionalities such as authentication, storage and a Postgres database.

While it's still experimental, you can start building your SaaS app with Next.js 13 today and future-proof your app for the future, without the need of painful migrations from the old architecture.