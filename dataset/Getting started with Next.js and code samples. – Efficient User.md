## Introduction

Next.js is a popular framework for building web applications with React. It provides a lot of powerful features out of the box, such as server-rendered React components, automatic code splitting, and easy-to-use development tools. In this blog post, we’re going to take a look at how to get started with Next.js and build a simple web application. By the end of this post, you should have a good understanding of the basic concepts behind Next.js, and be able to start building your own applications with the framework.

## Creating a New Next.js Project

The first step in building a Next.js application is to create a new project. You can do this using the Next.js CLI, which can be installed using npm:

This command will create a new directory called “my-app” that contains the basic file structure for a Next.js application. You should now be able to start the development server by running the following command:

If everything is set up correctly, you should see the message “ready on **[](http://localhost:3000/)[http://localhost:3000](http://localhost:3000/)**” in your terminal, and be able to view the default “Hello World” Next.js app in your browser.

## Understanding the File Structure

Once you have created a new Next.js project, it’s a good idea to take a look at the file structure to understand how the application is organized. The most important directories and files in a Next.js project are:

-   pages/: This directory contains the pages of your web application. Each file in this directory represents a page on your site, and its filename is used as the path for that page. For example, the file pages/about.js represents the “about” page of your site, and can be accessed at the url “/about”.
-   public/: This directory contains files that should be served as-is, such as images and fonts.
-   package.json: This file contains information about your project, such as its dependencies and scripts.
-   next.config.js: This file is used to configure advanced settings for your Next.js application.

## Creating a Simple Page

Now that you have a basic understanding of the file structure of a Next.js project, let’s create our first page.

Advertisements

In the pages directory, create a new file called “about.js”. Inside this file, add the following code:

<table><tbody><tr><td><p>1</p><p>2</p><p>3</p><p>4</p><p>5</p></td><td><div><p><code>import React from "react";</code></p><p><code>export default function About() {</code></p><p><code>&nbsp;&nbsp;</code><code>return &lt;h1&gt;About Page&lt;/h1&gt;;</code></p><p><code>}</code></p></div></td></tr></tbody></table>

This is a simple React component that renders an h1 tag with the text “About Page”. Next.js uses the file name of this component to define the path of the page. So, this component will be rendered when the application is accessed at the “/about” path.

If you start the development server with “npm run dev” and access “**[](http://localhost:3000/about)[http://localhost:3000/about](http://localhost:3000/about)**” in your browser, you should see the “About Page” text on the page.

## Adding Routing

In a more complex application, you’ll likely have more than one page and you’ll need a way to navigate between them. Next.js provides an easy way to do this through the use of dynamic routing.

To add dynamic routing, you’ll need to create a new file in the pages directory, and add a special syntax to the file name.

For example, create a new file called “users/\[userId\].js”. Inside the file, you can access the **`userId`** variable through the **`useRouter`** hook from the **`next/router`** package and use it to fetch data from an API or display information about a specific user.

<table><tbody><tr><td><p>1</p><p>2</p><p>3</p><p>4</p><p>5</p><p>6</p><p>7</p><p>8</p></td><td><div><p><code>import { useRouter } from 'next/router'</code></p><p><code>export default function User() {</code></p><p><code>&nbsp;&nbsp;</code><code>const router = useRouter()</code></p><p><code>&nbsp;&nbsp;</code><code>const { userId } = router.query</code></p><p><code>&nbsp;&nbsp;</code><code>return &lt;h1&gt;User: {userId}&lt;/h1&gt;</code></p><p><code>}</code></p></div></td></tr></tbody></table>

Now, when you visit the “/users/1” or “/users/2” path, the **`userId`** variable will be set to “1” or “2” respectively, and the corresponding user information can be displayed on the page.

To create the navigation links between pages, you can use the **`Link`** component from the **`next/link`** package.

<table><tbody><tr><td><p>1</p><p>2</p><p>3</p><p>4</p><p>5</p><p>6</p><p>7</p><p>8</p><p>9</p><p>10</p><p>11</p><p>12</p><p>13</p><p>14</p><p>15</p><p>16</p><p>17</p></td><td><div><p><code>import Link from 'next/link'</code></p><p><code>export default function Navigation() {</code></p><p><code>&nbsp;&nbsp;</code><code>return (</code></p><p><code>&nbsp;&nbsp;&nbsp;&nbsp;</code><code>&lt;nav&gt;</code></p><p><code>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</code><code>&lt;Link href="/"&gt;</code></p><p><code>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</code><code>&lt;a&gt;Home&lt;/a&gt;</code></p><p><code>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</code><code>&lt;/Link&gt;</code></p><p><code>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</code><code>&lt;Link href="/about"&gt;</code></p><p><code>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</code><code>&lt;a&gt;About&lt;/a&gt;</code></p><p><code>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</code><code>&lt;/Link&gt;</code></p><p><code>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</code><code>&lt;Link href="/users/1"&gt;</code></p><p><code>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</code><code>&lt;a&gt;User 1&lt;/a&gt;</code></p><p><code>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</code><code>&lt;/Link&gt;</code></p><p><code>&nbsp;&nbsp;&nbsp;&nbsp;</code><code>&lt;/nav&gt;</code></p><p><code>&nbsp;&nbsp;</code><code>)</code></p><p><code>}</code></p></div></td></tr></tbody></table>

## Building a Server-rendered React App with Next.js

Next.js also allows you to build server-rendered React apps, which can improve the performance and SEO of your application. To do this, you can use the **`getServerSideProps`** function in a page to fetch data on the server and then pass it down to the component as props.

<table><tbody><tr><td><p>1</p><p>2</p><p>3</p><p>4</p><p>5</p><p>6</p><p>7</p><p>8</p><p>9</p><p>10</p><p>11</p><p>12</p><p>13</p><p>14</p><p>15</p><p>16</p><p>17</p></td><td><div><p><code>import axios from 'axios'</code></p><p><code>export default function User({ user }) {</code></p><p><code>&nbsp;&nbsp;</code><code>return &lt;h1&gt;User: {user.name}&lt;/h1&gt;</code></p><p><code>}</code></p><p><code>export async function getServerSideProps(context) {</code></p><p><code>&nbsp;&nbsp;</code><code>const { userId } = context.params</code></p><p><code>&nbsp;&nbsp;</code><code>const user = res.data</code></p><p><code>&nbsp;&nbsp;</code><code>return {</code></p><p><code>&nbsp;&nbsp;&nbsp;&nbsp;</code><code>props: {</code></p><p><code>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</code><code>user</code></p><p><code>&nbsp;&nbsp;&nbsp;&nbsp;</code><code>}</code></p><p><code>&nbsp;&nbsp;</code><code>}</code></p><p><code>}</code></p></div></td></tr></tbody></table>

In this example, the **`getServerSideProps`** function is making a request to an API to fetch the user data and passing it down to the component as a prop. This way, the user data will be available on the initial render of the component on the server, improving the performance and SEO of your application.

## Conclusion

In this blog post, we’ve covered the basics of getting started with Next.js. We’ve looked at how to create a new project, the file structure of a Next.js project, creating a simple page, adding routing, and building a server-rendered React app. With the knowledge from this post, you should be well on your way to building your own web applications with Next.js.

Next.js is a powerful framework that makes it easy to build high-performance web applications. With its built-in features like automatic code splitting, server-rendering, and easy-to-use development tools, it can save you a lot of time and effort compared to building a similar application from scratch. I hope this post has been helpful in getting you started with Next.js, and I encourage you to continue learning more about the framework and experimenting with building your own projects.

To take your Next.js skills to the next level, I recommend checking out the official documentation, which provides a lot of valuable information and examples. Additionally, there are many tutorials and courses available online that can help you learn more about the framework.

Another useful tool that can be used with Next.js is Vercel, it’s a cloud platform for static site generators and serverless functions that can greatly simplify the deployment process of your Next.js application. With Vercel, you can deploy your application with a single command, and it will handle everything from building your application to provisioning the necessary resources.

In addition, there are many libraries and packages that have been built for Next.js, such as next-i18next for internationalization and next-redux for state management. These can greatly enhance the functionality of your application and make development more efficient.

In summary, Next.js is a powerful framework that makes it easy to build high-performance web applications. With its built-in features, it can save you a lot of time and effort. However, if you are just getting started, it can be difficult to know where to start. I hope that this post has provided you with a solid foundation and a good starting point for your Next.js journey.