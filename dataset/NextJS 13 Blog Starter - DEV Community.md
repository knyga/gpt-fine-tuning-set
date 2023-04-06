NextJS 13 is here and you know what that means, time to create another dynamic markdown blog. While the [NextJS blog starter](https://github.com/vercel/next.js/tree/canary/examples/blog-starter) is an amazing resource, it does not yet feature the new app directory file-based routing. It's time to take things into our own hands, at least until the new system is made public on NextJS 13.

## [](https://dev.to/slanted_dev/nextjs-13-blog-starter-1b6p#overview)Overview

In this project we will be taking a brief look at a handful of the changed features included in NextJS 13, however our main focus will be on NextJS's new [file routing](https://beta.nextjs.org/docs/routing/fundamentals) ecosystem.

## [](https://dev.to/slanted_dev/nextjs-13-blog-starter-1b6p#getting-started)Getting Started

Let's get things started with good old create-next-app, don't forget to add the experimental tag.  

```
npx create-next-app@latest --experimental-app
```

Enter fullscreen mode Exit fullscreen mode

> **Note**: this install gives you the option to choose between using Typescript or Javascript, and toggling ESLint. We will be using Typescript for this tutorial but feel free to choose Javascript if you like to live life dangerously.

or manual installation  

```
npm install next@latest react@latest react-dom@latest eslint-config-next@latest
```

Enter fullscreen mode Exit fullscreen mode

Here are the rest of the dependencies we will be using for the blog. You can install them now, or later on as we go.  

```
npm install remark remark-html gray-matter date-fns
```

Enter fullscreen mode Exit fullscreen mode

### [](https://dev.to/slanted_dev/nextjs-13-blog-starter-1b6p#remark-and-remarkhtml)remark and remark-html

[remark](https://github.com/remarkjs/remark/tree/main) is a powerful and versatile library that will allow us to start working with our markdown `.md` blog posts. [remark-html](https://github.com/remarkjs/remark-html) is a plugin for remark that allows us to serialize our markdown into HTML.

### [](https://dev.to/slanted_dev/nextjs-13-blog-starter-1b6p#graymatter)gray-matter

In order to get post information (such as author, title, date, etc.) from our HTML without having them be apart of our rendered post we need a way to parse YAML front matter, this is where [gray-matter](https://github.com/jonschlinkert/gray-matter) comes in hand.

Example `first-post.md`:  

```
---
postTitle: First Post!
date: 11-11-2022
---
<h1>This is my first post!</h1>
```

Enter fullscreen mode Exit fullscreen mode

using gray-matter we are able to extract:  

```
{
  data: { 
    postTitle: 'First Post!', 
    date: '11-11-2022' 
  },
 content: '<h1>This is my first post!</h1>'
}
```

Enter fullscreen mode Exit fullscreen mode

## [](https://dev.to/slanted_dev/nextjs-13-blog-starter-1b6p#say-goodbye-to-pages)Say goodbye to pages

For our first step on getting the blog up and running, it's out with the old and in with the new. NextJS has generously left the pages directory to allow users to slowly integrate their routing to the new file routing system, but for the purposes of this new project starting from scratch, let's **delete it**.

[![Pages directory image](https://res.cloudinary.com/practicaldev/image/fetch/s--tU3fyxfK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/uploads/articles/6a8pdl6mw1fajsryzefz.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tU3fyxfK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/uploads/articles/6a8pdl6mw1fajsryzefz.png)

## [](https://dev.to/slanted_dev/nextjs-13-blog-starter-1b6p#introducing-app)Introducing: app

Replacing the old pages directory in NextJS 13 is the new app directory. So what are the differences?

Inside the new app directory you will see 3 [_special files_](https://beta.nextjs.org/docs/routing/fundamentals#special-files):

-   page.tsx
-   head.tsx
-   layout.tsx

and 2 css files (you should be familiar with these):

-   global.css
-   page.module.css

### [](https://dev.to/slanted_dev/nextjs-13-blog-starter-1b6p#pagetsx)page.tsx

At first glance it's safe to assume that `page` has replaced `index` in NextJS 13. But it's also important to note that there is another major difference with this new system: `page.tsx` is what NextJS will be rendering **NOT** routing. Routing is now handled via the file path each `page.tsx` is located in.

#### [](https://dev.to/slanted_dev/nextjs-13-blog-starter-1b6p#old-page-directory-example)Old page directory example:

[![Page directory example image](https://res.cloudinary.com/practicaldev/image/fetch/s--ZgqCIYQc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/uploads/articles/qqebf90xvyeyogzry268.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZgqCIYQc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/uploads/articles/qqebf90xvyeyogzry268.png)

This would be routed by NextJS as 3 different pages `/about` , `/blog` , and `/`.

#### [](https://dev.to/slanted_dev/nextjs-13-blog-starter-1b6p#new-app-directory)New app directory:

[![App directory example image](https://res.cloudinary.com/practicaldev/image/fetch/s--fyyziqw6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/uploads/articles/975ke9okcx14pzm5i85r.PNG)](https://res.cloudinary.com/practicaldev/image/fetch/s--fyyziqw6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/uploads/articles/975ke9okcx14pzm5i85r.PNG)

The app directory **is** a route, much like the old pages directory, this means it needs its own `page.tsx` to render as an index at `example.com/`. As for the other routes, the main difference is that these pages are no longer different files all under `pages`, instead each route is housed within its own directory, with the directory name being the route and the `page.tsx` being its index render at that route.

> **Tip**: You can think of this new system like having multiple `pages` directories nested within each other, with each one making up its own route base.

### [](https://dev.to/slanted_dev/nextjs-13-blog-starter-1b6p#layouttsx)layout.tsx

I'm sure many people have created a Layout component at some point, one that stores components (such as a Header and Footer) and renders the page inside. Well the new `layout.tsx` is essentially a built in version of this. Simply open the file, create your layout, and return a ReactNode child component inside and you're done. Believe it or not it really is that simple.

> **Note**: This is also much more powerful and efficient than a typical layout component, I will be making a separate post on this in the future.

### [](https://dev.to/slanted_dev/nextjs-13-blog-starter-1b6p#other-special-files)Other Special files

You may also notice there is a `head.tsx`, for now we won't be worrying about this file. For this project we will only be dealing with `page.tsx` and `layout.tsx`. For more information check out the [NextJS docs](https://beta.nextjs.org/docs/routing/fundamentals#special-files).

## [](https://dev.to/slanted_dev/nextjs-13-blog-starter-1b6p#styles)Styles

NextJS 13 keeps the same CSS system of `global.css` along with module CSS files.

I love pure CSS just as much as the next developer, but why not get things done quick and easy with some Tailwind?

### [](https://dev.to/slanted_dev/nextjs-13-blog-starter-1b6p#tailwindcss-install)TailwindCSS Install

```
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p
```

Enter fullscreen mode Exit fullscreen mode

```
//tailwind.config.js

/** @type {import('tailwindcss').Config} */
module.exports = {
  content: [
    "./app/**/*.{js,ts,jsx,tsx}",
    "./components/**/*.{js,ts,jsx,tsx}",
  ],
  theme: {
    extend: {},
  },
  plugins: [],
}
```

Enter fullscreen mode Exit fullscreen mode

```
/*gobal.css*/

@tailwind base;
@tailwind components;
@tailwind utilities;
```

Enter fullscreen mode Exit fullscreen mode

## [](https://dev.to/slanted_dev/nextjs-13-blog-starter-1b6p#lets-make-a-blog)Let's make a blog!

Now that we have a better understanding of how routing works let's begin making a blog. We will be using the current [NextJS blog starter](https://github.com/vercel/next.js/tree/canary/examples/blog-starter) as a reference.

### [](https://dev.to/slanted_dev/nextjs-13-blog-starter-1b6p#home-page)Home page

Create a simple home (index) page to start with. Later we will add a way to fetch our recent posts to display and allow users to navigate to them.  

```
// app/page.tsx

export default function Home() {
  return (
    <div className="container mx-auto">
      <main>
        <div className="space-y-4">
          <h1 className="text-center text-5xl">NextJS 13 Blog</h1>
          <p className="text-center text-xl">
            Welcome to a dynamic markdown blog using NextJS 13.
          </p>
        </div>
      </main>
    </div>
  );
}
```

Enter fullscreen mode Exit fullscreen mode

### [](https://dev.to/slanted_dev/nextjs-13-blog-starter-1b6p#navbar)Navbar

For easy navigation create a nav header and add it to our new special file `layout.tsx`.  

```
// components/Navbar.tsx

export default function Navbar() {
    return (
        <div className="bg-neutral-800">

        </div>
    )
}
```

Enter fullscreen mode Exit fullscreen mode

We also want some pages to link to: `Home` , `Blog` , and `Github`. Create a NavLink item to display our NextJS links.

-   Home: blog information and recent posts.
-   Blog: directory of all posts.
-   Github: link to the project files.

```
type NavLink = {
  href: string;
  children: React.ReactNode;
};

const NavLink = ({ href, children }: NavLink) => {
  return (
    <Link className="hover:text-gray-300 hover:underline" href={href}>
      {children}
    </Link>
  );
};
```

Enter fullscreen mode Exit fullscreen mode

Add our new link items to the Navbar and we have a nice looking header.  

```
// components/Navbar.tsx

import Link from "next/link";

export default function Navbar() {
  return (
    <div className="bg-neutral-800">
      <nav className="container py-2 mx-auto">
        <ul className="flex space-x-6 text-lg justify-center">
          <li>
            <NavLink href="/">Home</NavLink>
          </li>
          <li>
            <NavLink href="/blog">Blog</NavLink>
          </li>
          <li>
            <NavLink href="https://github.com/garrett-huggins/next13-blog-starter">
              Github
            </NavLink>
          </li>
        </ul>
      </nav>
    </div>
  );
}

type NavLink = {
  href: string;
  children: React.ReactNode;
};

const NavLink = ({ href, children }: NavLink) => {
  return (
    <Link className="hover:text-gray-300 hover:underline" href={href}>
      {children}
    </Link>
  );
};
```

Enter fullscreen mode Exit fullscreen mode

### [](https://dev.to/slanted_dev/nextjs-13-blog-starter-1b6p#layout)Layout

Now let's import our new stylish Navbar into our layout:  

```
// app/layout.tsx

import "./globals.css";
import Navbar from "../components/Navbar";

export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="en">
      {/*
        <head /> will contain the components returned by the nearest parent
        head.tsx. Find out more at https://beta.nextjs.org/docs/api-reference/file-conventions/head
      */}
      <head />

      <body className="bg-neutral-900 text-white">
        <Navbar />
        <div
          id="page-top-spacer"
          className="h-12 bg-gradient-to-t from-transparent to-neutral-800"
        ></div>
        {children}
        <div id="page-bottom-spacer" className="h-16"></div>
      </body>
    </html>
  );
}
```

Enter fullscreen mode Exit fullscreen mode

### [](https://dev.to/slanted_dev/nextjs-13-blog-starter-1b6p#fetching)Fetching

We will need a way to fetch our posts and their front matter data. Let's grab the example api functions from the [NextJS blog starter](https://github.com/vercel/next.js/tree/canary/examples/blog-starter/lib).

> **Note**: we will be using the same functions from here but their implementation will be a little different.

#### [](https://dev.to/slanted_dev/nextjs-13-blog-starter-1b6p#lib)Lib

Create a `lib` folder to store our post getter functions:  

```
// lib/api.ts

import fs from "fs";
import { join } from "path";
import matter from "gray-matter";

const postsDirectory = join(process.cwd(), "_posts");

export function getPostSlugs() {
  return fs.readdirSync(postsDirectory);
}

export function getPostBySlug(slug: string, fields: string[] = []) {
  const realSlug = slug.replace(/\.md$/, "");
  const fullPath = join(postsDirectory, `${realSlug}.md`);
  const fileContents = fs.readFileSync(fullPath, "utf8");
  const { data, content } = matter(fileContents);

  type Items = {
    [key: string]: string;
  };

  const items: Items = {};

  // Ensure only the minimal needed data is exposed
  fields.forEach((field) => {
    if (field === "slug") {
      items[field] = realSlug;
    }
    if (field === "content") {
      items[field] = content;
    }

    if (typeof data[field] !== "undefined") {
      items[field] = data[field];
    }
  });

  return items;
}

export function getAllPosts(fields: string[] = []) {
  const slugs = getPostSlugs();
  const posts = slugs
    .map((slug) => getPostBySlug(slug, fields))
    // sort posts by date in descending order
    .sort((post1, post2) => (post1.date > post2.date ? -1 : 1));
  return posts;
}
```

Enter fullscreen mode Exit fullscreen mode

and  

```
// lib/markdownToHtml.ts

import { remark } from "remark";
import html from "remark-html";

export default async function markdownToHtml(markdown: string) {
  const result = await remark().use(html).process(markdown);
  return result.toString();
}
```

Enter fullscreen mode Exit fullscreen mode

### [](https://dev.to/slanted_dev/nextjs-13-blog-starter-1b6p#post-cards)Post cards

Before we start grabbing our posts to display, there is one last step: stylish cards to display our post front matter as preview cards.

Create a formatter for the post dates using [date-fns](https://github.com/date-fns/date-fns):  

```
// components/DateFormatter.tsx

import { parseISO, format } from 'date-fns'

type Props = {
  dateString: string
}

const DateFormatter = ({ dateString }: Props) => {
  const date = parseISO(dateString)
  return <time className="text-slate-400" dateTime={dateString}>{format(date, 'LLLL d, yyyy')}</time>
}

export default DateFormatter
```

Enter fullscreen mode Exit fullscreen mode

Then create some preview cards to display the post information:  

```
// components/PostPreview

import DateFormatter from "./DateFormatter";
import Image from "next/image";
import Link from "next/link";

type Items = {
  [key: string]: string;
};

export default function PostPreview({ post }: { post: Items }) {
  return (
    <div className="w-full mx-auto group">
      <Link href={`/posts/${post.slug}`}>
        {post?.coverImage && (
          <Image
            alt={`cover image for ${post.title}`}
            src={post.coverImage}
            width={400}
            height={400}
            style={{ width: "100%" }}
          />
        )}
        <div className="mt-4 space-y-2">
          <p className="font-semibold text-xl group-hover:underline">
            {post.title}
          </p>
          <DateFormatter dateString={post.date} />
          <p>{post.excerpt}</p>
        </div>
      </Link>
    </div>
  );
}
```

Enter fullscreen mode Exit fullscreen mode

```
// components/PostHero.tsx

import DateFormatter from "./DateFormatter";
import Image from "next/image";
import Link from "next/link";
import { getPostBySlug } from "../lib/api";

type Items = {
  [key: string]: string;
};

export default function PostHero() {
  const heroPost = getPostBySlug("hero-post", [
    "title",
    "excerpt",
    "slug",
    "date",
    "coverImage",
  ]);

  return (
    <Link href={`/posts/${heroPost.slug}`}>
      <div className="w-full mx-auto group">
        <Image
          alt={`cover image for ${heroPost.title}`}
          src={heroPost.coverImage}
          width={400}
          height={400}
          style={{ width: "100%" }}
        />

        <div className="grid mt-4 md:grid-cols-2 grid-cols-1">
          <div className="mb-2">
            <p className="font-semibold text-xl group-hover:underline">
              {heroPost.title}
            </p>
            <DateFormatter dateString={heroPost.date} />
          </div>
          <p>{heroPost.excerpt}</p>
        </div>
      </div>
    </Link>
  );
}
```

Enter fullscreen mode Exit fullscreen mode

## [](https://dev.to/slanted_dev/nextjs-13-blog-starter-1b6p#dynamic-posts)Dynamic posts

Let's use our getter functions to start displaying some posts. In NextJS 13 `page.tsx` is a React server component by default, therefore we can say goodbye to `getServerSideProps` , `getStaticProps` , and `getInitialProps`.  

```
// app/page.tsx

import { getAllPosts } from "../lib/api";
import PostPreview from "../components/PostPreview";
import PostHero from "../components/PostHero";
import Link from "next/link";

export default function Home() {
  const posts = getAllPosts(["title", "date", "excerpt", "coverImage", "slug"]);
  const recentPosts = posts.slice(0, 2);

  return (
    <div className="container mx-auto px-5">
      <main>
        <div className="space-y-4">
          <h1 className="text-center text-5xl">NextJS 13 Blog</h1>
          <p className="text-center text-xl">
            Welcome to a dynamic markdown blog using NextJS 13.
          </p>
        </div>

        <div className="h-12"></div>

        <PostHero />

        <div className="h-16"></div>

        <p className="text-3xl mb-6">Recent Posts</p>
        <div className="grid md:grid-cols-2 grid-cols-1 mx-auto md:gap-32 gap-8">
          {recentPosts.map((post) => (
            <div key={post.title}>
              <PostPreview post={post} />
            </div>
          ))}
        </div>
        <div className="h-16"></div>
        <Link
          href="/blog"
          className="text-3xl hover:text-gray-300 hover:underline"
        >
          Read More{" -> "}
        </Link>
      </main>
    </div>
  );
}
```

Enter fullscreen mode Exit fullscreen mode

As you can tell the main difference here from the original NextJS starter blog is that we no longer need to use NextJS data fetching api's, instead we can just grab our data and use it right out of the gate.

### [](https://dev.to/slanted_dev/nextjs-13-blog-starter-1b6p#post-slugs)Post Slugs

Another change in NextJS 13 is dynamic routing. Now that folders are used for routing and `page.tsx` is used for rendering, there is no more `[slug].tsx`. Instead the folders themselves can be turned into the slugs.

Inside our app directory create a `posts` folder, and inside it create a `[slug]` folder. This folder is now our search params for `/posts/[slug]`.

Now create a dynamic `page.tsx` inside our `[slug]` directory to render the individual post pages.

> **Tip**: you may also want to create a `module.css` to help style your HTML retrieved from the posts. [Here](https://github.com/garrett-huggins/next13-blog-starter/blob/main/app/posts/%5Bslug%5D/markdown-styles.module.css) are the styles I use.  

```
// app/posts/[slug]

import { getPostBySlug } from "../../../lib/api";
import markdownToHtml from "../../../lib/markdownToHtml";
import markdownStyles from "./markdown-styles.module.css";

export default async function Post({ params }: { params: { slug: string } }) {
  const post = getPostBySlug(params.slug, ["title", "author", "content"]);

  const content = await markdownToHtml(post.content || "");

  return (
    <div className="container mx-auto">
      <main>
        <div className="w-full h-16  text-white">
          <p className="text-2xl">{post.title}</p>
          <p className="text-gray-400">{post.author}</p>
          <div
            className={markdownStyles["markdown"]}
            dangerouslySetInnerHTML={{ __html: content }}
          />
        </div>
      </main>
    </div>
  );
}
```

Enter fullscreen mode Exit fullscreen mode

We now can access our individual posts as dynamically routed pages using search paramaters.

## [](https://dev.to/slanted_dev/nextjs-13-blog-starter-1b6p#blog-page)Blog page

With a separate `Home` page used to display a hero post, a few recent posts, along with any additional information, the last step is to create a blog home page that contains _all_ of our posts.

Create a `blog` folder and `page.tsx` under our `app`.  

```
// app/blog/page.tsx

import { getAllPosts } from "../../lib/api";
import PostPreview from "../../components/PostPreview";

export default function Blog() {
  const posts = getAllPosts(["title", "date", "excerpt", "coverImage", "slug"]);

  return (
    <div className="container mx-auto px-5">
      <main>
        <h1 className="text-center text-3xl">All Posts</h1>

        <div className="h-12"></div>

        <div className="grid md:grid-cols-2 grid-cols-1 lg:gap-32 gap-8">
          {posts.map((post) => (
            <div>
              <PostPreview post={post} />
            </div>
          ))}
        </div>
      </main>
    </div>
  );
}
```

Enter fullscreen mode Exit fullscreen mode

We now have our very own dynamic markdown blog, all in the new and improved NextJS 13. Help yourself to the [project repo](https://github.com/garrett-huggins/next13-blog-starter) for reference, or I encourage you to clone it to mess around with.