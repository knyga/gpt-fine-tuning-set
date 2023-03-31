Next.js is well known for its file system-based routing. However, Next.js v13, which is new at the time of writing, has changed the manner in which many tasks were previously performed through its [new `app` directory](https://beta.nextjs.org/docs/app-directory-roadmap).

While still supporting the same file system-based routing, which uses the `pages` directory, the new `app` directory introduces the concepts of layouts, error components, and loading components while also leveraging React’s server components for building a UI. In this article, we’ll explore these new features by building a simple app. Let’s get started!

## Table of contents

- [New features in Next.js 13](https://blog.logrocket.com/next-js-13-new-app-directory/#concepts)
  - [Page directory vs. app directory](https://blog.logrocket.com/next-js-13-new-app-directory/#page-directory-vs-app-directory)
  - [Routing with the app directory](https://blog.logrocket.com/next-js-13-new-app-directory/#routing-app-directory)
  - [`loading.tsx` file](https://blog.logrocket.com/next-js-13-new-app-directory/#loading-tsx-file)
  - [`error.tsx` file](https://blog.logrocket.com/next-js-13-new-app-directory/#error-tsx)
  - [`template.tsx` file](https://blog.logrocket.com/next-js-13-new-app-directory/#template-tsx)
- [Caveats of using the `app` directory](https://blog.logrocket.com/next-js-13-new-app-directory/#caveats)
  - [Mandatory root layout](https://blog.logrocket.com/next-js-13-new-app-directory/#mandatory-root-layout)
  - [Head tag](https://blog.logrocket.com/next-js-13-new-app-directory/#head-tag)
  - [Route groups](https://blog.logrocket.com/next-js-13-new-app-directory/#route-group)
  - [Server components](https://blog.logrocket.com/next-js-13-new-app-directory/#server-components)
- [Hands-on with Next.js 13](https://blog.logrocket.com/next-js-13-new-app-directory/#hands-on)
  - [Project creation](https://blog.logrocket.com/next-js-13-new-app-directory/#project-creation)
  - [The page and layout file](https://blog.logrocket.com/next-js-13-new-app-directory/#page-layout-file)
  - [Testing the layout](https://blog.logrocket.com/next-js-13-new-app-directory/#testing-layout)
  - [Testing the error file](https://blog.logrocket.com/next-js-13-new-app-directory/#testing-error-file)
  - [Testing the loading file](https://blog.logrocket.com/next-js-13-new-app-directory/#testing-loading-file)
- [Data fetching](https://blog.logrocket.com/next-js-13-new-app-directory/#data-fetching)
  - [Client and server-side rendering](https://blog.logrocket.com/next-js-13-new-app-directory/#client-server-side-rendering)

## New features in Next.js 13

Before we get started working on a project with Next.js 13, we’ll review the new features and concepts that Next.js 13 has introduced.

### Page directory vs. app directory

If you’ve worked with previous versions of [Next.js, you might already be familiar with the `pages` directory](https://blog.logrocket.com/implementing-ssr-next-js-dynamic-routing-prefetching/#creating-next-js-routes). Any file created inside of the `pages` directory would act as a route in the UI. For example, `pages/home.jsx` would take care of the `/home` route:

![Pages Home JSX Route](https://blog.logrocket.com/wp-content/uploads/2022/11/pages-home-jsx-route.png)

The new `app` directory works alongside the `pages` directory to [support incremental adoption](https://beta.nextjs.org/docs/app-directory-roadmap#supported-features) and provides other new features like server-side rendering and static-site generation.

### Routing with the `app` directory

Just like files inside of the `pages` directory, routing with the `app` directory is controlled via the folders inside of it. The UI for a particular route is defined with a `page.jsx` file inside of the folder.

Therefore, a folder structure that looks like `app/profile/settings/page.jsx` will take care of rendering the `/profile/settings` route:

![Nextjs App Directory Routing](https://blog.logrocket.com/wp-content/uploads/2022/11/next-js-app-directory-routing.png)

### `loading.tsx` file

`loading.tsx` is an optional file that you can create within any directory inside the `app` folder. It automatically wraps the page inside of a [React suspense boundary](https://beta.reactjs.org/apis/react/Suspense#suspense). The component will be shown immediately on the first load as well as when you’re navigating between the sibling routes.

### `error.tsx` file

`error.tsx` is an optional file that isolates the error to the smallest possible subsection of the app. Creating the `error.tsx` file automatically wraps the page inside of a [React error boundary](https://reactjs.org/docs/error-boundaries.html). Whenever any error occurs inside the folder where this file is placed, the component will be replaced with the contents of this component.

### `layout.tsx` file

You can use the `layout.tsx` file to define a UI that is shared across multiple places. A layout can render another layout or a page inside of it. Whenever a route changes to any component that is within the layout, its state is preserved because the layout component is not unmounted.

### `template.tsx` file

`template.tsx` is similar to the `layout.tsx` file, but upon navigation, a new instance of the component is mounted and the state is not preserved.

Using layouts and templates allows us to take advantage of a concept known as partial rendering. While moving between routes inside of the same folder, only the layouts and pages inside of that folder are fetched and rendered:

![Template Tsx Partial Rendering](https://blog.logrocket.com/wp-content/uploads/2022/11/template-tsx-partial-rendering.png)

## Caveats of using the `app` directory

With so many changes having been introduced in Next.js 13, there are some things that we need to keep in mind when moving to the `app` directory from the `pages` directory.

### Mandatory root layout

There must be a file that defines the root layout at the top level of the app directory. This layout is applicable to all the routes in the app. In addition, the root layout must define the `<html>` and the `<body>` tags because Next.js does not automatically add them.

### Head tag

Inside any folder in the `app` directory, we’ll create a `head.js` file that will define the contents of the `<head>` tag for that folder. The component returned from this `head.js` file can only return certain limited tags like `<title>`, `<meta>`, `<link>`, and `<script>`.

### Route groups

Every folder inside the `app` directory contributes to the URL path. But, it is possible to opt-out of it by wrapping the folder name inside of parentheses. All the files and folders inside of this special folder are said to be a part of that route group:

![Nextjs Partial Rendering Route Groups](https://blog.logrocket.com/wp-content/uploads/2022/11/next-js-partial-rendering-route-groups.png)

### Server components

By default, all of the components created inside of the `app` directory are React server components, leading to better performance due to a smaller bundle size. But, if we want to switch to the client component, we need to specify that with the [`use client` directive](https://beta.nextjs.org/docs/rendering/server-and-client-components#convention) at the top of the file.

## Hands-on with Next.js 13

Let’s experiment with all the new features in Next.js 13 by running through an example.

### Project creation

First, we create a new Next.js project using [Create Next App](https://blog.logrocket.com/introducing-the-new-create-next-app/):

```
npx create-next-app next-13
cd next-13
```

Let’s run the bootstrapped code as is:

```
npm run dev
```

We are greeted with the familiar homepage:

![Nextjs Homepage](https://blog.logrocket.com/wp-content/uploads/2022/11/next-js-homepage.png)

### The page and layout file

Let’s create a folder parallel to the `pages` directory and name it `app`. Create a `layout.js` file inside of `app` with the code below:

```
export default function Layout({ children }) {
  return (
    <html lang="en">
      <head>
        <title>Next.js</title>
      </head>
      <body>
        {children}
      </body>
    </html>)
}
```

Create a `page.js` file with the following code:

```
import '../styles/globals.css'
export default function Page() {
  return <h1>Hello, Next.js!</h1>;
}
```

We’ve also imported the `global.css` file to make use of the global styles that are already defined. The `app` directory is still an experimental feature, so we need to set a flag in the `next.config.js` file in order to use it:

```
module.exports = {
  reactStrictMode: true,
  experimental:{appDir: true}
}
```

Finally, we need to delete the `pages/index.js` file, which will conflict with the file in the `app` directory. With that in place, we can now run the dev server:

```
npm run dev
```

We see that the root route `/` now shows the UI corresponding to the `app/page.js` file:

![Page-js Route UI](https://blog.logrocket.com/wp-content/uploads/2022/11/page-js-route-ui.png)

### Testing the layout

With that in place, let’s test how the layout file impacts the overall UI. First, we’ll write some CSS styles in a `layout.module.css` file in the same directory:

```
.header {
  width: 100%;
  height: 50vh;
  background-color: cyan;
  text-align: center;
  font-size: 2rem;
}
```

Next, we import those styles in the `layout.js` file and add them to a `div` inside the body just above the children:

```
import styles from './layout.module.css'

export default function Layout({ children }) {
  return (
    <html lang="en">
      <head>
        <title>Next.js</title>
      </head>
      <body>
        <div
          className={styles.header}
        >From layout</div>
        <div>
          {children}
        </div>
      </body>
    </html>)
}
```

The UI now looks like the following:

![Import Styles Layout JS UI](https://blog.logrocket.com/wp-content/uploads/2022/11/import-styles-layout-js-ui.png)

Let’s add a new folder in the `app` directory called `second`. Create a file inside it named `page.js` with the following code:

```
import '../../styles/globals.css'

export default function Page() {
  return <h1>Second route!</h1>;
}
```

Navigating to the second route `[http://localhost:3000/second](http://localhost:3000/second)` loads the following UI:

![NextJs Secound Route UI](https://blog.logrocket.com/wp-content/uploads/2022/11/next-js-secound-route-ui.png)

The layout file placed inside the `app` directory is being shared by the `page.js` in the same directory as well as the `page.js` inside of the `second` folder. You can accomplish any common changes that deal with the layout via the layout file.

### Testing the error file

Next, let’s check out the `error.js` file. We’ll create a folder inside the `app` folder; we’ll name the folder `breaking` and create separate `page.js` and `breaking.module.css` files:

```
'use client';

import '../../styles/globals.css'
import styles from './breaking.module.css';

export default function Page() {
  return (
    <div className={styles.component}>
      <div>BREAKING</div>
      <div>
        <button onClick={(e) => console.log(e.b.c)}>
          break this
        </button>
      </div>
    </div>
  );
}
```

At the top of the page, `use client` tells Next.js to render this component as a client component, not a server component, which is the default. We’re handling user input via the button component below:

```
.component {
  width: 200px;
  height: 200px;
  display: flex;
  align-items: center;
  justify-content: center;
  border: 2px solid black;
  flex-direction: column;
}

.error {
  background-color: tomato;
  color: white;
}
```

With this CSS in place, the component looks something like the image below:

![Nextjs Render Client Component](https://blog.logrocket.com/wp-content/uploads/2022/11/next-js-render-client-component.png)

Now, let’s create an `error.js` file in the `breaking` folder. `error.js` will act as an error boundary in case any error occurs either inside this component or any components in its subtree:

```
'use client';

import '../../styles/globals.css'
import { useEffect } from 'react';
import styles from './breaking.module.css';

export default function Error({
  error,
  reset,
}) {
  useEffect(() => {

    console.error(error);
  }, [error]);

  return (
    <div className={styles.error}>
      <div>ERROR</div>
      <p>Something went wrong!</p>
      <button onClick={() => reset()}>Reset error boundary</button>
    </div>
  );
}
```

Notice that this is also a client component. Two props are passed to this component: the `error` prop provides more details about the error, and the `reset` function resets the error boundary. This should be enough to contain the error only to the component and preserve the UI as well as the state of the rest of the application.

### Testing the loading file

Next, we’ll test the functionality of the `loading.js` file. Let’s create one inside of the same folder with the following code:

```
export default function Loading() {
  return <h1>Loading...</h1>
}
```

With that in place, we need to set up some navigation. Inside the `second/page.js` we place a link to navigate to the `/breaking` route:

```
export default function Page() {
  return (<Link href="/breaking">navigate to breaking</Link>);
}
```

Upon clicking this link, we’ll see that before the breaking component gets mounted, the UI from the `loading.js` file will appear for a split second:

![Loadingjs File UI Display](https://blog.logrocket.com/wp-content/uploads/2022/11/loading-js-file-ui-display.gif)

## Data fetching

Lastly, we’ll explore how data fetching in Next.js 13 differs from earlier versions. All of the components inside the `app` folder are server components by default.

Let’s make the changes to the `second.js` component to fetch random dog facts from the [Dog Facts API](https://dukengn.github.io/Dog-facts-API/):

```
async function getData() {
  const index = Math.floor(Math.random()*10)
  const res = await fetch(https:
  return res.json();
}
```

We’ll call this function directly inside of our React component by making it async:

```
export default async function Page() {
  const data = await getData();
  return (
    <p>
      {data[0].fact}
    </p>
  );
}
```

The code above fetches the dog fact on the server side and displays it in our component:

![Dog Fact Fetched Server Side](https://blog.logrocket.com/wp-content/uploads/2022/11/dog-fact-fetched-server-side.png)

### Client and server-side rendering

Using the Fetch API natively inside the component provides us with the ability to cache and revalidate the requests as per our requirement. Therefore, the previous utils like `getStaticProps` and `getServerSideProps` can be implemented via just one API as seen below:

```

fetch(URL, { cache: 'force-cache' });


fetch(URL, { cache: 'no-store' });


fetch(URL, { next: { revalidate: 20 } });
```

## Conclusion

That wraps up almost all the changes that were introduced with the `app` directory in Next.js 13.

Although at the time of writing, these new features are in beta and are bound to change slightly before being officially released, we can agree that they provide much more flexibility to configure our UI through the loading, error, and layout components. The simplicity of the native Fetch API on server components is also a great addition.

Here’s the link to the [code](https://github.com/kokanek/nextjs-13) that we worked with. Feel free to explore!
