## Learn how to use Next.js 13 and the app directory by creating a full stack web app including Prisma, Postgresql and NextAuth.

![](https://miro.medium.com/v2/resize:fit:1400/1*Mllgho23BhL_MH3Rski_yA.png)

Next.js is a powerful and flexible framework that can be used for building a wide variety of web applications, from small personal projects to large-scale enterprise applications.

We are going to build a full stack application using the following technologies:

-   [Next.js](https://nextjs.org/) as the React framework
-   [Next.js API routes](https://nextjs.org/docs/api-routes/introduction) for server-side API routes as the backend
-   [Prisma](https://prisma.io/) as the ORM for migrations and database access
-   [PostgreSQL](https://www.postgresql.org/) as the database
-   [NextAuth.js](https://next-auth.js.org/) for authentication via GitHub (OAuth)
-   [TypeScript](https://www.typescriptlang.org/) as the programming language

Users will be able see all messages, but they are only able to create new messages and delete their own messages when logged in with their github account.

![](https://miro.medium.com/v2/resize:fit:1400/1*BzfZASjuEsFFKTHA_7KK1g.gif)

You can find the complete code to the project here:

## Prerequisites

In order to be able to follow along, you will need:

-   Node.js
-   A GitHub Account (to create an OAuth app)

Follow the link below and install Node.js on your machine:

## Getting Started

Let’s start our project by using “The best way to start a full-stack, typesafe Next.js app” and use the following command (the command will create a project directory for you):

```
npm create t3-app@latest
```

![](https://miro.medium.com/v2/resize:fit:1400/1*-3IipW95xmHZ9oTwIvFyCQ.gif)

1.  Give your project a name (insert a project name and enter to proceed)
2.  Select typescript (enter)
3.  Select nextauth, prisma and tailwind (space to select, arrows to navigate and enter to proceed)
4.  You can initialize a git repository if you want(y/n)
5.  Let npm install all the dependencies (y)
6.  Configure a path alias (“@/” for example)

After the installation is complete, run `npx prisma db push` . Now we can check it by running `npm run dev` and open the browser at [http://localhost:3000/](http://localhost:3000/) .

Before we continue, we want to make some changes to the installation and restructure it so that we can make use of the app directory.

First we have to add an experimental flag to `next.conf.mjs` (because the app directory is currently in beta):

```
experimental: {  appDir: true,},
```

The complete file should look like this:

```
!process.env.SKIP_ENV_VALIDATION && (await import("./src/env.mjs"));const config = {  reactStrictMode: true,    i18n: {    locales: ["en"],    defaultLocale: "en",  },  experimental: {    appDir: true,  },};export default config;
```

Next, let’s create the new `app` directory in our project directory and a new file inside it called `page.tsx`. We can copy the sample page from `src/pages/index.tsx` and paste it into the new `page.tsx` . As there will be a conflict now, because we have 2 home pages, we have to delete the `index.tsx` file. We can also go ahead and delete `_app.tsx` .

![](https://miro.medium.com/v2/resize:fit:1400/1*Dd6QBHT_fkE-9ZHcPe5pdQ.gif)

GIF create app/ directory

Before we can test our new setup, we have to include the app directory in the `tailwind.config.cjs` file:

```
module.exports = {  content: ["./app/**/*.{js,ts,jsx,tsx}", "./src/**/*.{js,ts,jsx,tsx}"],  theme: {    extend: {},  },  plugins: [],};
```

Let’s run `npm run dev` and see what is happening. There will be a lot of output in your console, but the most important is:

“Your page app/page.tsx did not have a root layout. We created app\\layout.tsx and app\\head.tsx for you.”

We can now find those 2 new files in our app directory. There are special file conventions that are used to create a UI for each route segment. The most common special files are `page.tsx` and `layout.tsx` .

-   **page.tsx** is used to define UI unique to a route.
-   **layout.tsx** is used to define UI that is shared across multiple routes.

There are also reserved files for metadata, error and loading that we need/want in our folder:

\- **head.tsx** is used to define the metadata of an html file.

\- **error.tsx** is used to create error UI for a segment and its children.

\- **loading.tsx** is used to create loading UI for a segment and its children.

However, when we look at the browser at [http://localhost:3000/](http://localhost:3000/), we can see that there is no styling. We can fix this by importing the `globals.css` file into our `layout.tsx` file:

```
import "@/styles/globals.css";export default function RootLayout({  children,}: {  children: React.ReactNode;}) {  return (    <html>      <head />      <body>{children}</body>    </html>  );}
```

## Database Setup

For the purpose of this guide, we will use a free PosgtreSQL database hosted on Supabase.

Alternatively, you can also use a [local](https://www.prisma.io/dataguide/postgresql/setting-up-a-local-postgresql-database) PostgreSQL database. However, once you reach the deployment step of this guide, you’ll need a hosted database so that it can be accessed from the application when it’s deployed on Vercel.

Head over to [https://supabase.com/](https://supabase.com/), create an account or sign in with github and create a new project.

![](https://miro.medium.com/v2/resize:fit:1400/1*XrsCvkEx-LmYRNbLRAeoHQ.gif)

GIF create supabase project

Now we can get the connection string from the project settings.

![](https://miro.medium.com/v2/resize:fit:1400/1*kAEVMYuxPEEdI0fjm4xVSA.png)

Screenshot supabase project settings

Let’s connect to our new database by using the connection string in our `.env` file (use your own connection string here):

```
DATABASE_URL="postgresql://postgres:[YOUR-PASSWORD]@db.xjmezeypuvhhzifwjyhi.supabase.co:5432/postgres"
```

We also have to change the datasource provider in `schema.prisma` to:

```
provider = "postgresql"
```

## Schema with Prisma

With [Prisma](https://www.prisma.io/), you can define your database schema using a data definition language (DDL) and Prisma will automatically generate a set of TypeScript types and APIs that allow you to interact with your database in a type-safe and convenient way. Prisma also provides a migration system that makes it easy to evolve your database schema over time, and it integrates with a wide range of popular databases, including PostgreSQL, MySQL, SQLite, and more.

And this is exactly what we are going to need when working with a database, so let’s get started and create our models.

```
generator client {    provider = "prisma-client-js"}datasource db {    provider = "postgresql"                    url      = env("DATABASE_URL")}model Posts {  id        String     @default(cuid()) @id  title     String  published Boolean @default(false)  author    User?   @relation(fields: [authorId], references: [id])  authorId  String?  createdAt DateTime  @default(now())}model Account {    id                String  @id @default(cuid())    userId            String    type              String    provider          String    providerAccountId String    refresh_token     String? @db.Text    access_token      String? @db.Text    expires_at        Int?    token_type        String?    scope             String?    id_token          String? @db.Text    session_state     String?    user              User    @relation(fields: [userId], references: [id], onDelete: Cascade)    @@unique([provider, providerAccountId])}model Session {    id           String   @id @default(cuid())    sessionToken String   @unique    userId       String    expires      DateTime    user         User     @relation(fields: [userId], references: [id], onDelete: Cascade)}model User {    id            String    @id @default(cuid())    name          String?    email         String?   @unique    emailVerified DateTime?    image         String?    accounts      Account[]    sessions      Session[]    posts         Posts[]}model VerificationToken {    identifier String    token      String   @unique    expires    DateTime    @@unique([identifier, token])}
```

This Prisma schema defines `Posts` with a relation (one-to-many) between the two models `Posts` and `User` via the `author` field on `Posts` and the `posts` field on `User`.

Everything necessary for NextAuth will be discussed in the next section.

To actually create the tables in your database, you now can use the following command of the Prisma CLI:

```
npx prisma db push
```

We can now see the tables that have been created for us in the supabase table editor, or you can use the following command to start prisma studio:

```
npx prisma studio
```

As the docs of create-t3-app suggests, located at `src/server/db.ts`, the Prisma Client is instantiated as a global variable (as recommended as [best practice↗](https://www.prisma.io/docs/guides/database/troubleshooting-orm/help-articles/nextjs-prisma-client-dev-practices#problem) by the team at Prisma) and exported to be used in your API routes. We include the Prisma Client in Context by default and recommend using this instead of importing it separately in each file.

With this file, you can now import this `PrismaClient` instance anywhere in your Next.js app as follows:

```
import { prisma } from '@/server/db'
```

In order to make sure that the Prisma client is using the latest schema, we have to run the following command to update the client every time we make changes to the schema:

```
npx prisma generate
```

## Setting up NextAuth

NextAuth.js is a popular open-source authentication library for Next.js applications. It provides a simple way to implement authentication in your application using various authentication providers, such as Google, Facebook, GitHub, and others, as well as custom providers.

NextAuth.js supports several authentication flows, such as email and password-based authentication, social authentication, and token-based authentication. It provides a flexible and customizable API to handle the authentication process, and it integrates seamlessly with Next.js, making it easy to add authentication to your application.

With create-t3-app we have already set up NextAuth in our project. However, we need to make some configurations.

In general, a file called `[...nextauth].js` in `pages/api/auth` contains the dynamic route handler for NextAuth.js which will also contain all of your global NextAuth.js configurations. In our case, the configurations are all made in `src/server/auth.ts` .

As you can see, the default provider that has been set up for us is Discord. Nevertheless, we are going to use the Github provider, so you can see how you can change the file so that you can use any Provider that you like.

Replace the Discord provider import with the Github provider:

```
import GithubProvider from "next-auth/providers/github"
```

And replace the Discord provider with the Github provider down below in the providers configuration:

```
GithubProvider({  clientId: env.GITHUB_ID,  clientSecret: env.GITHUB_SECRET,}),
```

We also have to change the `.env.mjs` file. This file is used for validation. Replace the Discord variables with the Github Variables:

```
import { z } from "zod";const server = z.object({  DATABASE_URL: z.string().url(),  NODE_ENV: z.enum(["development", "test", "production"]),  NEXTAUTH_SECRET:    process.env.NODE_ENV === "production"      ? z.string().min(1)      : z.string().min(1).optional(),  NEXTAUTH_URL: z.preprocess(            (str) => process.env.VERCEL_URL ?? str,        process.env.VERCEL ? z.string().min(1) : z.string().url()  ),    GITHUB_ID: z.string(),  GITHUB_SECRET: z.string(),});const client = z.object({  });const processEnv = {  DATABASE_URL: process.env.DATABASE_URL,  NODE_ENV: process.env.NODE_ENV,  NEXTAUTH_SECRET: process.env.NEXTAUTH_SECRET,  NEXTAUTH_URL: process.env.NEXTAUTH_URL,  GITHUB_ID: process.env.GITHUB_ID,  GITHUB_SECRET: process.env.GITHUB_SECRET,  };const merged = server.merge(client);let env =  (process.env);if (!!process.env.SKIP_ENV_VALIDATION == false) {  const isServer = typeof window === "undefined";  const parsed =  (    isServer      ? merged.safeParse(processEnv)       : client.safeParse(processEnv)   );  if (parsed.success === false) {    console.error(      "❌ Invalid environment variables:",      parsed.error.flatten().fieldErrors    );    throw new Error("Invalid environment variables");  }  env = new Proxy(parsed.data, {    get(target, prop) {      if (typeof prop !== "string") return undefined;                  if (!isServer && !prop.startsWith("NEXT_PUBLIC_"))        throw new Error(          process.env.NODE_ENV === "production"            ? "❌ Attempted to access a server-side environment variable on the client"            : `❌ Attempted to access server-side environment variable '${prop}' on the client`        );      return target[ (prop)];    },  });}export { env };
```

Next we need to provide the following env variables in our `.env` file:

```
GITHUB_ID=""GITHUB_SECRET=""
```

To get the credentials for the Github provider, go to [github.com](https://github.com/) and make sure you are logged in. Then hit settings and click Developer settings on the bottom of the left sidebar. Now select OAuth Apps and register a new application.

You can give your application any name. For the homepage URL we will use [http://localhost:3000/](http://localhost:3000/) in development. Simply change it later when going in production. And for the authorization callback URL we will use [http://localhost:3000/api/auth/callback/github](http://localhost:3000/api/auth/callback/github) .

Use the Client ID and generate a new Client Secret that we can use in our `.env` file.

> Notice that we have NEXTAUTH\_URL=”http://localhost:3000" in our `.env` file. Make sure to change this when going in production.

We also want to provide a variable for `NEXTAUTH_SECRET` . The easiest method is to follow the link ([https://generate-secret.vercel.app/32](https://generate-secret.vercel.app/32)) and use the response that you get.

```
NEXTAUTH_SECRET=""
```

## Sign in and Sign out

Now that we have set up our project, we can start developing our app. We want to build a simple app that let’s us log in, see all posts of users and let’s us create and delete our own posts.

First we are going to implement auth in our app. Let’s start by creating a new file in our `app` directory called `Actions.tsx` . This file will contain our sign in and sign out buttons.

Also go ahead and create a new folder called `components` in our `src` directory and add a new file called `Icons.tsx` including the following:

```
export function GitHubIcon() {  return (    <svg      width="20"      height="20"      viewBox="0 0 24 24"      fill="none"      xmlns="http://www.w3.org/2000/svg"    >      <g clipPath="url(#clip0_9914_10)">        <path          d="M12 0C5.374 0 0 5.373 0 12C0 17.302 3.438 21.8 8.207 23.387C8.806 23.498 9 23.126 9 22.81V20.576C5.662 21.302 4.967 19.16 4.967 19.16C4.421 17.773 3.634 17.404 3.634 17.404C2.545 16.659 3.717 16.675 3.717 16.675C4.922 16.759 5.556 17.912 5.556 17.912C6.626 19.746 8.363 19.216 9.048 18.909C9.155 18.134 9.466 17.604 9.81 17.305C7.145 17 4.343 15.971 4.343 11.374C4.343 10.063 4.812 8.993 5.579 8.153C5.455 7.85 5.044 6.629 5.696 4.977C5.696 4.977 6.704 4.655 8.997 6.207C9.954 5.941 10.98 5.808 12 5.803C13.02 5.808 14.047 5.941 15.006 6.207C17.297 4.655 18.303 4.977 18.303 4.977C18.956 6.63 18.545 7.851 18.421 8.153C19.191 8.993 19.656 10.064 19.656 11.374C19.656 15.983 16.849 16.998 14.177 17.295C14.607 17.667 15 18.397 15 19.517V22.81C15 23.129 15.192 23.504 15.801 23.386C20.566 21.797 24 17.3 24 12C24 5.373 18.627 0 12 0Z"          fill="currentColor"        />      </g>      <defs>        <clipPath id="clip0_9914_10">          <rect width="24" height="24" fill="white" />        </clipPath>      </defs>    </svg>  );}
```

Next we create our sign in and sign out buttons. As all of the components in the app directory are server components, we need to add `"user client";` so that this part is rendered at the client and we can have an `onClick` for our button.

Open `Actions.tsx` and paste in the following code:

```
"use client";import { GitHubIcon } from "@/components/Icons";import { signIn, signOut } from "next-auth/react";export function SignOut() {  return (    <button      className="mt-2 mb-6 text-xs text-white hover:text-[hsl(280,100%,70%)]"      // eslint-disable-next-line @typescript-eslint/no-misused-promises      onClick={() => signOut()}    >      → Sign out    </button>  );}export function SignIn() {  return (    <button      className="mb-4 flex rounded-md border border-gray-800 bg-black px-4 py-3 text-sm font-semibold text-neutral-200 transition-all hover:text-white"      // eslint-disable-next-line @typescript-eslint/no-misused-promises      onClick={() => signIn("github")}    >      <GitHubIcon />      <div className="ml-3">Sign in with GitHub</div>    </button>  );}
```

Here we are creating a sign out button that invokes the `signout()` function of NextAuth when clicked and a sign in button with that invokes the `signin()` function respectively with the Github icon and a specific style that we defined using tailwind.

We can now go ahead and edit our `page.tsx` file and make use of the `Actions.tsx` that we just defined:

```
import { SignIn, SignOut } from "./Actions";import { getServerSession } from "next-auth/next";import { authOptions } from "@/server/auth";export default async function Home() {  let session;  try {    const [sessionRes] = await Promise.allSettled([      getServerSession(authOptions),    ]);    if (sessionRes.status === "fulfilled") {      session = sessionRes.value;    } else {      console.error(sessionRes);    }  } catch (error) {    console.error(error);  }  return (    <>      <main className="flex min-h-screen flex-col items-center justify-center bg-gradient-to-b from-[#2e026d] to-[#15162c] text-white">        <div className="container flex flex-col items-center justify-center gap-12 px-4 py-16 ">          <h1 className="text-5xl font-extrabold tracking-tight sm:text-[5rem]">            Create <span className="text-[hsl(280,100%,70%)]">T3</span> App          </h1>          {session?.user ? (            <>              <SignOut />            </>          ) : (            <SignIn />          )}        </div>      </main>    </>  );}
```

In order to check if a user is logged in or not, we are using `getServerSession` and store the value of the settled promise in `session` that we can later use to check if there is any session already there. If a user is logged in, our app will render the sign out button and if there is no user logged in, our app will render the sign in button.

We can now test our app by running `npm run dev` and log in. A user will be created in our database with all the information that is defined in our schema. After the first login, you will see the following every time you log in:

![](https://miro.medium.com/v2/resize:fit:1400/1*447C3_Cmu6P2pR8YqTY-ow.gif)

GIF login test

## Create and Delete

Now that we managed to log in a user we can start setting up our posts. We want to be able to see all posts, but only be able create and delete our own posts when logged in.

## API Routes

First let’s create our api endpoints by creating a new file called `posts.ts` in `src/pages/api` :

```
import type { NextApiRequest, NextApiResponse } from "next";import { getSession } from "next-auth/react";import { prisma } from "@/server/db";export default async function handler(  req: NextApiRequest,  res: NextApiResponse) {  const session = await getSession({ req });  if (!session || !session.user) {    return res.status(403).send("Unauthorized");  }  const id = session.user.id;  if (req.method === "POST") {    await prisma.posts.create({      data: {                title: req.body.title,        authorId: id,      },    });    return res.status(200).json({ error: null });  }  if (req.method === "DELETE") {    await prisma.posts.delete({      where: {                id: req.body.id,      },    });    return res.status(204).end();  }  return res.send("Method not allowed.");}
```

This is a Next.js API route handler that handles `POST` and `DELETE` requests to `/api/posts`. It first gets the user's session using `getSession` from the `next-auth/react` package. If there is no session or no user in the session, it sends a `403` response with the message "Unauthorized".

If the request method is `POST`, it creates a new post using the `create` method of the `posts` model in the Prisma ORM. The post data is taken from the `title` property of the `req.body` object and the `id` of the user is taken from the session.

If the request method is `DELETE`, it deletes a post using the `delete` method of the `posts` model in the Prisma ORM. The post ID is taken from the `id` property of the `req.body` object.

If the request method is neither `POST` nor `DELETE`, it sends a "Method not allowed" message in the response.

The response format depends on the request method. If the request method is `POST`, it returns a `200` status code with a JSON object containing `error: null`. If the request method is `DELETE`, it returns a `204` status code with an empty body.

## Form Component

Next we want to create our form component. Go ahead and create a new file called `Form.tsx` in our `app` directory:

```
"use client";import { useRouter } from "next/navigation";import { useState, useTransition } from "react";export default function Form() {  const router = useRouter();  const [isPending, startTransition] = useTransition();  const [isFetching, setIsFetching] = useState(false);  const isMutating = isFetching || isPending;  async function onSubmit(e: React.FormEvent<HTMLFormElement>) {    e.preventDefault();    setIsFetching(true);    const form = e.currentTarget;    const input = form.elements.namedItem("entry") as HTMLInputElement;    const res = await fetch("/api/posts", {      body: JSON.stringify({        title: input.value,      }),      headers: {        "Content-Type": "application/json",      },      method: "POST",    });    input.value = "";        const { error } = await res.json();    console.log(error);    setIsFetching(false);    startTransition(() => {                  router.refresh();    });  }  return (    <form      style={{ opacity: !isMutating ? 1 : 0.7 }}      className="relative max-w-[500px] text-sm"      // eslint-disable-next-line @typescript-eslint/no-misused-promises      onSubmit={onSubmit}    >      <input        aria-label="Your message"        placeholder="Your message..."        disabled={isPending}        name="entry"        type="text"        required        className="mt-1 block w-full rounded-md border-neutral-300 bg-gray-100 py-3 pl-4 pr-32 text-neutral-900 focus:border-blue-500 focus:ring-blue-500 dark:bg-neutral-800 dark:text-neutral-100"      />      <button        className="absolute right-1 top-2 flex h-9 w-16 items-center justify-center rounded bg-neutral-200 px-2 py-1 font-medium text-neutral-900 dark:bg-neutral-700 dark:text-neutral-100"        disabled={isMutating}        type="submit"      >        Send      </button>    </form>  );}
```

This is a React component that represents a form for creating a new post in the blog application. It uses the `useRouter` hook from Next.js to get the router instance, and the `useState` and `useTransition` hooks from React to manage the state of the form submission process.

The `onSubmit` function is called when the form is submitted. It first prevents the default form submission behavior, and then sets the `isFetching` state to `true`. It then gets the input value from the form and sends a `POST` request to the `/api/posts` endpoint with the title of the new post as the body of the request. Once the request completes, the `isFetching` state is set back to `false` and the `startTransition` function is called to refresh the current page without losing the client-side state. The form has an input field for entering the post title and a "Send" button for submitting the form. The "Send" button is disabled when the form is in the process of being submitted.

## Delete Component

We also want to create a delete component. Go ahead and create a new file called `Delete.tsx` in our `app` directory:

```
"use client";import { useRouter } from "next/navigation";import { useState, useTransition } from "react";export default function Delete({ id }: { id: string }) {  const router = useRouter();  const [isPending, startTransition] = useTransition();  const [isFetching, setIsFetching] = useState(false);  const isMutating = isFetching || isPending;  async function onClick() {    setIsFetching(true);    await fetch("/api/posts", {      body: JSON.stringify({        id: id,      }),      headers: {        "Content-Type": "application/json",      },      method: "DELETE",    });    setIsFetching(false);    startTransition(() => {                  router.refresh();    });  }  return (    <button      className="text-sm text-red-500 opacity-0 transition hover:opacity-100"      disabled={isMutating}      type="button"      // eslint-disable-next-line @typescript-eslint/no-misused-promises      onClick={onClick}    >      {isMutating ? "Deleting..." : "Delete"}    </button>  );}
```

This is a React component called `Delete` that creates a button which allows users to delete a post. When the button is clicked, the `onClick` function is triggered which sends a `DELETE` request to the `/api/posts` endpoint with the `id` of the post to be deleted.

The component uses the `useRouter` hook from `next/navigation` to access the current route and the `useTransition` and `useState` hooks from `react` to manage the state of the deletion process. The `isMutating` variable is true while the deletion process is ongoing.

The `onClick` function sets `isFetching` to true before sending the `DELETE` request. If the request is successful, `isFetching` is set to false and the `router.refresh()` function is called to refresh the current route and fetch new data from the server.

## Homepage

Last but not least, we can implement all our components in `page.tsx` and create an async function that fetches all posts, even if the user is not logged in:

```
import { SignIn, SignOut } from "./Actions";import { getServerSession } from "next-auth/next";import { authOptions } from "@/server/auth";import Image from "next/image";import Form from "./Form";import { prisma } from "@/server/db";import Delete from "./Delete";import { type Session } from "next-auth";async function getPosts() {  const data = await prisma.posts.findMany({    include: {      author: true,    },    orderBy: {      createdAt: "desc",    },  });  return data;}export default async function Home() {  let session: Session | null | undefined;  let posts;  try {    const [postsRes, sessionRes] = await Promise.allSettled([      getPosts(),      getServerSession(authOptions),    ]);    if (postsRes.status === "fulfilled" && postsRes.value[0]) {      posts = postsRes.value;    } else {      console.error(postsRes);    }    if (sessionRes.status === "fulfilled") {      session = sessionRes.value;    } else {      console.error(sessionRes);    }  } catch (error) {    console.error(error);  }  return (    <>      <main className="flex min-h-screen flex-col items-center justify-center bg-gradient-to-b from-[#2e026d] to-[#15162c] text-white">        <div className="container flex flex-col items-center justify-center gap-10 px-4 py-16 ">          <h1 className="text-5xl font-extrabold tracking-tight sm:text-[5rem]">            Our <span className="text-[hsl(280,100%,70%)]">BLOG</span> App          </h1>          <div className="flex flex-col items-center justify-center gap-1">            {session?.user ? (              <>                <Image                  className="w-14 rounded-full"                  width={64}                  height={64}                  src={session.user.image as string}                  alt={session.user.name as string}                />                <SignOut />                <Form />              </>            ) : (              <SignIn />            )}          </div>          <div className="flex max-w-md flex-col items-center justify-center gap-5">            {posts?.map((post) => (              <div                key={post.id}                className="flex flex-row items-center justify-center gap-2"              >                <h2 className="text-sm">{post.author?.name}:</h2>                <p className="break-all text-sm font-bold">{post.title}</p>                {session?.user.email === post.author?.email && (                  <Delete id={post.id} />                )}              </div>            ))}          </div>        </div>      </main>    </>  );}
```

Now we can test our app. Go ahead and use `npm run dev` and check the browser at [http://localhost:3000/](http://localhost:3000/).

![](https://miro.medium.com/v2/resize:fit:1400/1*BzfZASjuEsFFKTHA_7KK1g.gif)

GIF create and delete

## Summary

In this article, we learned how to create a full-stack blogging application using Next.js, Prisma, PostgreSQL, and NextAuth. We also explored the app directory feature of Next.js 13 and how to use it to structure our application. We set up database models using Prisma, added authentication with NextAuth, created API routes, and implemented components for creating and deleting posts. Finally, we brought everything together in our homepage.

This tutorial serves as a great starting point for building full-stack web applications using Next.js and I hope you find it helpful when starting your first full-stack app.