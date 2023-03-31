_**Editor’s note:** This guide was last updated on 8 March 2022 to reflect changes introduced in NextAuth.js v4._

Authentication is an important and sensitive feature in applications where a user’s credentials such as username, email, and password are validated to confirm if users are who they say they are.

It is a way of asking users who they are, and then receiving evidence to verify their identity. The system takes credentials provided by the user and checks if they are valid.

In this article, we’ll walk you through how to set up client-side authentication that doesn’t require a password in [Next.js](https://nextjs.org/) using a powerful and secure library called [NextAuth.js](https://next-auth.js.org/).

By the end of this post, you will have created an app with authentication where your users will be able to log in using their GitHub, Google, and Facebook accounts. Upon successful signup, we will display the user’s profile picture, email, which we’ll retrieve from their social media accounts.

We will make use of React Hooks and functional components to build out our app. But before we dive deeper, let’s take a look at the two major tools we’ll be using.

## What is Next.js?

Next.js is a framework built on top of React that makes developing production-ready, fully-optimized React apps super fast and easy. It is one of the best things to have come out of the React ecosystem, as it comes with a whole lot of power with zero config.

Next.js is used in production by top companies like Netflix, Tiktok, and Nike. It is super easy to learn, especially if you’re familiar with React.

## What is NextAuth.js?

NextAuth.js is a completely secured authentication solution for implementing authentication in Next.js applications. It is a flexible authentication library designed to sync with any OAuth service, with full support for passwordless sign in.

It can be used with or without a database, and it has default support for popular databases such as MySQL, MongoDB, PostgreSQL, and MariaDB.

It can be used without a database by syncing with services like OAuth and JSON Web Token.

## How does NextAuth.js work?

With a library like NextAuth.js, you don’t need to be an expert in identity protocol like you would if you were to use OAuth to build secured Next.js applications. NextAuth.js is built to avoid the need to store sensitive data, such as a users’ password. It works perfectly with Next.js. With just 20 lines of code, you can implement an authentication feature in your app.

NextAuth.js has a client-side API you can use to interact with sessions in your app. The session data returned from the [Providers](https://next-auth.js.org/configuration/providers) contains user payload, and this can be displayed to the user upon successful login.

The session data returned to the client looks like this:

```
{
  expires: '2055-12-07T09:56:01.450Z';
  user: {
        email: 'sample@example.com';
        image: 'https://avatars2.githubusercontent.com/u/45228014?v=4';
        name: 'Ejiro Asiuwhu';
    }
}
```

Observe that the payload doesn’t contain any sensitive data. The session payload or data is meant for presentation purposes — that is, it’s meant to be displayed to the user.

NextAuth.js provides the `useSession()` React Hook, which can be used to check the user login status. Meanwhile, NextAuth.js provides a REST API that is used by the React app. To learn more about the REST API NextAuth exposes, check out the [official docs](https://next-auth.js.org/getting-started/rest-api).

## Requirements

-   Node.js 10.13 or later installed on your local machine
-   Basics of [React.js](http://reactjs.org/)

## Creating the Next.js starter application

I have created a starter Next.js project for you want to follow along. To start with, fork this [repository](https://github.com/ejirocodes/Nextjs_Authentication) to your own GitHub account and clone it.

```
git clone https://github.com/<your-Github-username>/Nextjs_Authentication.git
```

This project includes two branches: `starter` and `complete`. Check out the `starter` branch to get started. However, if you want to see the finished code, check out the `complete` branch.

Once the repository has been cloned, navigate to the working directory:

```
cd Nextjs_Authentication
```

The starter project was originally created with React v17.0.1. To avoid any issues when installing next-auth, update the `package.json` to upgrade `react` and `react-dom`:

```
"dependencies": {
    "next": "10.0.1",
    "react": "^17.0.2",
    "react-dom": "^17.0.2"
  }
```

Then, install the Next.js dependencies:

```
npm i

yarn install
```

You should already be on the `starter` branch but, if that is not the case, use this command:

```
git checkout starter
```

Launch the development server:

```
npm run dev

yarn run dev
```

By default, the project will run on port 3000. Launch your browser and navigate to `[http://localhost:3000](http://localhost:3000/)`. You should end up with this:

![Starter project.](https://blog.logrocket.com/wp-content/uploads/2020/12/starter-project.png)

Starter project.

## Set up authentication with NextAuth.js

Now that we have the Next.js starter application set up, we’re ready to learn how to authenticate a Next.js app with NextAuth.js.

This NextAuth.js client-side authentication tutorial will cover the following:

-   [Installing NextAuth.js](https://blog.logrocket.com/nextauth-js-for-next-js-client-side-authentication/#installing-nextauth-js)
-   [Creating a GitHub OAuth app](https://blog.logrocket.com/nextauth-js-for-next-js-client-side-authentication/#creating-a-github-oauth-app)
-   [Creating a Google OAuth app](https://blog.logrocket.com/nextauth-js-for-next-js-client-side-authentication/#creating-a-google-oauth-app)
-   [Creating a Facebook OAuth app](https://blog.logrocket.com/nextauth-js-for-next-js-client-side-authentication/#creating-a-facebook-oauth-app)

### Install NextAuth.js

`next-auth` is an npm package, so installing it will be a breeze:

```
npm i next-auth

yarn add next-auth
```

Upon successful installation, `next-auth` should be added to the dependencies in your `package.json` file:

```
//Dependencies in package.json
"dependencies": {
  "next": "10.0.1",
  "next-auth": "^4.2.1",
  "react": "^17.0.2",
  "react-dom": "^17.0.2"
}
```

We’re going to give users the choice to log in to our app using their GitHub, Google, or Facebook account.

## \[H3\]Create a GitHub OAuth app

Next, we’re going to add a GitHub Authentication Provider, which essentially allows users to log in to our app using their GitHub account. But first, we need to create a [GitHub OAuth app](https://github.com/settings/developers). Click on **New OAuth app** and fill out the form accordingly. Check out the [official docs](https://docs.github.com/en/free-pro-team@latest/developers/apps/creating-an-oauth-app) to learn more.

-   **Application name**: This is the name of your application. It can be called anything — it doesn’t really matter
-   **Homepage URL**: This is the full URL to the homepage of our app. Since we are still in development mode, we are going to fill in the full URL that our development server is running on. In this case, it is `[http://localhost:3000](http://localhost:3000/)`
-   **Authorization callback URL**: This is the URL that GitHub will redirect our users to after they have been successfully logged in to our app. It should be your homepage URL plus `/api/auth/callback`, resulting in `[http://localhost:3000/api/auth/callback](http://localhost:3000/api/auth/callback)`

After registration of our OAuth App, GitHub creates a Client ID and Client Secret specifically for our newly created app. Copy the client ID and secret key to your clipboard. Click on **Generate new client secret** and get a client secret.

#### Add an environmental variable

Next, create an `.env.local` file in your project’s root directory. Next.js has inbuilt support for environment variables and `.env.local` will load those variables to `process.env`. Therefore, the filename cannot be a random name. For more information, don’t hesitate to read [Next.js documentation on environment variables](https://nextjs.org/docs/basic-features/environment-variables).

Next, populate it with the following content:

```
GITHUB_ID=<client id of your github auth app should go in here>
GITHUB_SECRET=<client secret of your github app should go in here>
NEXTAUTH_URL=http://localhost:3000
/pre>
```

> `[NEXTAUTH_URL]([https://next-auth.js.org/configuration/options](https://next-auth.js.org/configuration/options))` is the url of our app. Be sure to use the port your development server is running on.

Now, back to our app. We’re going to create a file named `[...nextauth].js` in `pages/api/auth` and add the following code:

```
import NextAuth from 'next-auth'
import GitHubProvider from "next-auth/providers/github";

const options = {
    providers: [
        GitHubProvider({
            clientId: process.env.GITHUB_ID,
            clientSecret: process.env.GITHUB_SECRET
        }),
    ],
}

rest apiexport default (req, res) => NextAuth(req, res, options)
```

On line 1, we’re importing NextAuth, which is our main package. On line 2, we’re importing our [GitHub Provider](https://next-auth.js.org/providers/github) from the `next-auth` library, which are services that we can integrate into our app to allow users to sign in.

On line 6, we’re configuring our GitHub provider and passing in our GitHub secret and client ID through our environmental variables. Finally, on line 13, we’re exporting a function that returns the NextAuth and takes in the options variable as a third parameter.

Well, the magic has happened already. If we make use of the [REST API](https://next-auth.js.org/getting-started/rest-api) provided by `next-auth`, we can log in to our app using our GitHub account. Navigate to `[http://localhost:3000/api/auth/signin](http://localhost:3000/api/auth/signin)` and you should see this ![👇🏽](https://s.w.org/images/core/emoji/14.0.0/svg/1f447-1f3fd.svg)

![Sign in With Github Activated](https://blog.logrocket.com/wp-content/uploads/2020/12/sign-in-github-page.png)

Sign in with GitHub activated.

Follow the process, and shazam! You are logged in. Up next, we need to save and display user login state.

#### Check user login state with the `useSession()` Hook

We need to get the login state of our users and render user details on the frontend of our app. This can be easily achieved by using a Next.js feature called [custom app](https://nextjs.org/docs/advanced-features/custom-app). Then, we’ll wrap our component in a Provider.

Create an `_app.js` file in your `pages` directory (if it doesn’t already exist) and add the following code:

```
import { SessionProvider } from "next-auth/react"
import '../styles/globals.css'

function MyApp({ Component, pageProps }) {
  return (
    <SessionProvider session={pageProps.session}>
      <Component {...pageProps} />
    </SessionProvider>
  )
}

export default MyApp
```

By wrapping our component in a `Session``Provider`, we enable session state to be shared between pages. This, in turn, will preserve our state during page navigation, improve performance, and reduce network traffic.

Next, open the `components/Header.js` file and import `useSession`, `signIn`, and `signOut` from `next-auth/client`:

```
import { useSession, signIn, signOut } from 'next-auth/react'
```

`useSession` will be used to manage the sign in and sign out state of our users, while `signIn` and `signOut` will be used to perform the login and logout features in our app.

Let’s make use of the `useSession` Hook:

```
 const { data: session } = useSession();
```

The session will return the user’s details. Let’s use the details returned to conditionally render a sign in and sign out button.

Replace everything in the return statement in `components/Header.js` with the following code:

```
 <div className='header'>
      <Link href='/'>
        <a className='logo'>NextAuth.js</a>
      </Link>
           {session && <a href="#" onClick={handleSignout} className="btn-signin">Sign out</a>  } 
           {!session && <a href="#" onClick={handleSignin}  className="btn-signin">Sign in</a>  } 
    </div>
```

We need to create the `handleSignin` and `handleSignout` methods to enable our users to sign in and sign out:

```
const handleSignin = (e) => {
      e.preventDefault()
      signIn()
  }    
const handleSignout = (e) => {
      e.preventDefault()
      signOut()
    }
```

Your `Header.js` should now look like this:

```
import Link from 'next/link'
import { useSession, signIn, signOut } from 'next-auth/react'

export default function Header () {
  const { data: session } = useSession();

  const handleSignin = (e) => {
    e.preventDefault()
    signIn()
  }

  const handleSignout = (e) => {
    e.preventDefault()
    signOut()
  }

  return (
    <div className='header'>
      <Link href='/'>
        <a className='logo'>NextAuth.js</a>
      </Link>
      {session && <a href="#" onClick={handleSignout} className="btn-signin">Sign out</a>  }
      {!session && <a href="#" onClick={handleSignin}  className="btn-signin">Sign in</a>  }
    </div>
  )
}
```

Feel free to sign in and sign out of the app using the header button.

#### Retrieve and display user information

Now, onto our `pages/index.js`. We need to display and conditionally render user details based on their login details. First, we have to import the `useSession` hook from `next-auth`.

So, replace your `index.js` with the following content:

```
import Head from 'next/head'
import Header from '../components/Header'
import styles from '../styles/Home.module.css'
import { useSession } from 'next-auth/react'

export default function Home() {
  const { data: session, status } = useSession()
  const loading = status === "loading"

  return (
    <div className={styles.container}>
      <Head>
        <title>Nextjs | Next-Auth</title>
        <link rel="icon" href="/favicon.ico" />
      </Head>
      <Header />
      <main className={styles.main}>
        <h1 className={styles.title}>Authentication in Next.js app using Next-Auth</h1>
        <div className={styles.user}>
           {loading && <div className={styles.title}>Loading...</div>}
           {
            session &&
              <>
               <p style={{ marginBottom: '10px' }}> Welcome, {session.user.name ?? session.user.email}</p> <br />
               <img src={session.user.image} alt="" className={styles.avatar} />
              </>
            }
           {
            !session &&
              <>
               <p className={styles.title}>Please Sign in</p>
               <img src="https://cdn.dribbble.com/users/759083/screenshots/6915953/2.gif" alt="" className={styles.avatar} />
               <p className={styles.credit}>GIF by <a href="https://dribbble.com/shots/6915953-Another-man-down/attachments/6915953-Another-man-down?mode=media">Another man</a> </p>
              </>
           }
         </div>
      </main>
    </div>
  )
}
```

In this code, we are conditionally rendering the user’s image, name, and photo using the data from our session state if the user is logged into our app. If the user is not logged in, we display a dummy GIF with text instructing them to sign in.

### Create a Google OAuth app

To allow users to log in to our app using their Google account, we have to obtain OAuth 2.0 client credentials from the [Google API Console](https://console.developers.google.com/). Navigate to **C**_redentials_ and click on **Create credentials**, and then **OAuth client ID**:

![Create Google OAuth Client ID.](https://blog.logrocket.com/wp-content/uploads/2020/12/create-google-oauth-client-id.png)

Create Google OAuth client ID.

You will be asked to fill in the following:

-   **Choose an Application Type**: Select **Web Application**
-   **Name**: This is the name of your application
-   **Authorized JavaScript origins**: This is the full URL to the homepage of our app. Since we are still in development mode, we are going to fill in the full URL our development server is running on. In this case, it is `[http://localhost:3000](http://localhost:3000/)`
-   **Authorized redirect URIs**: Users will be redirected to this path after they have authenticated with Google: `[http://localhost:3000/api/auth/callback/google](http://localhost:3000/api/auth/callback/google)`

Next, a popup will display your client ID and client secret. Copy and add them o your `env.local` file:

```
GOOGLE_ID=<client id of your google auth app should go in here>
GOOGLE_SECRET=<client secret of your google auth app should go in here>
```

Next, navigate to `pages/api/auth/[...nextauth].js` and add the following to your array of providers:

```
import GoogleProvider from 'next-auth/providers/google';
...
   GoogleProvider({
      clientId: process.env.GOOGLE_ID,
      clientSecret: process.env.GOOGLE_SECRET,
    }),
...
```

Your `[...nextauth].js` should now look like this:

```
import NextAuth from 'next-auth';
import GitHubProvider from 'next-auth/providers/github';
import GoogleProvider from 'next-auth/providers/google';

const options = {
    providers: [
        GitHubProvider({
            clientId: process.env.GITHUB_ID,
            clientSecret: process.env.GITHUB_SECRET
        }),
        GoogleProvider({
          clientId: process.env.GOOGLE_ID,
          clientSecret: process.env.GOOGLE_SECRET,
        }),
    ],
}

export default (req, res) => NextAuth(req, res, options)
```

To test out user login using a Google account, quit your development server and run `npm run dev`.  
Now, with our fingers crossed and our heads held high, we should be able to sign in to our app with our Google account:

![Sign in with Google activated.](https://blog.logrocket.com/wp-content/uploads/2020/12/sign-in-google.png)

Sign in with Google activated.

### Create a Facebook OAuth app

To use the Facebook login for our app, we will need [a Facebook developer account](https://developers.facebook.com/). Create an account and then create your app. For the app type, choose **Consumer**. In the Details screen, choose a display name.

Once that is completed, your screen should look like this:

![Login options.](https://blog.logrocket.com/wp-content/uploads/2020/12/login-options.png)

Login options.

Click on **Facebook Login** and select **Web**. Now, add the following:

-   **Site URL**: Complete URL to your development server `[http://localhost:3000/](http://localhost:3000/)`

To get your app ID and app secret, navigate to the **Basic** section in **Settings**, copy them, and add them to your `env.local` file as follows:

```
FACEBOOK_ID=<app id of your facebook app should go in here>
FACEBOOK_SECRET=<app secret of your facebook app should go in here>
```

> Don’t forget to add `[http://localhost:3000/](http://localhost:3000/)` in the **App Domains** field.

Next, navigate to `pages/api/auth/[...nextauth].js` and add the following to your array of providers:

```
import FacebookProvider from 'next-auth/providers/facebook';
...
    FacebookProvider({
            clientId: process.env.FACEBOOK_ID,
            clientSecret: process.env.FACEBOOK_SECRET
      })
...
```

Your `[...nextauth].js` should now look like this:

```
import NextAuth from 'next-auth';
import GitHubProvider from 'next-auth/providers/github';
import GoogleProvider from 'next-auth/providers/google';
import FacebookProvider from 'next-auth/providers/facebook';

const options = {
    providers: [
        GitHubProvider({
            clientId: process.env.GITHUB_ID,
            clientSecret: process.env.GITHUB_SECRET
        }),
        GoogleProvider({
          clientId: process.env.GOOGLE_ID,
          clientSecret: process.env.GOOGLE_SECRET,
        }),
        FacebookProvider({
          clientId: process.env.FACEBOOK_ID,
          clientSecret: process.env.FACEBOOK_SECRET
        }),
    ],
}

export default (req, res) => NextAuth(req, res, options)
```

After you restart your development server, users should be able to log in to your Next.js app using their GitHub, Google, and Facebook accounts:

![Sign in with Facebook activated.](https://blog.logrocket.com/wp-content/uploads/2020/12/sign-in-facebook.png)

Sign in with Facebook activated.

## Conclusion

In this post, we’ve implemented user authentication in Next.js using NextAuth.js, which is a secured library to identify our users, get user profile information, and render them in our frontend.  
We’ve covered most of the use cases, but there is a lot more you can do with NextAuth.js. You can add a database using JSON Web Token, secure pages, and more.

If you are hungry for more content, check out the [tutorial page](https://next-auth.js.org/tutorials) from the NextAuth.js official docs.

Let me know in the comments section below what you thought of this tutorial. You can also reach me on [Twitter](https://twitter.com/ejirocodes) and [GitHub](https://github.com/ejirocodes). Thank you for reading and stay tuned.

## [LogRocket](https://lp.logrocket.com/blg/nextjs-signup): Full visibility into production Next.js apps

Debugging Next applications can be difficult, especially when users experience issues that are difficult to reproduce. If you’re interested in monitoring and tracking state, automatically surfacing JavaScript errors, and tracking slow network requests and component load time, [try LogRocket](https://lp.logrocket.com/blg/nextjs-signup). [![](https://files.readme.io/27c94e7-Image_2017-06-05_at_9.46.04_PM.png)](https://lp.logrocket.com/blg/nextjs-signup)[![LogRocket Dashboard Free Trial Banner](https://blog.logrocket.com/wp-content/uploads/2017/03/1d0cd-1s_rmyo6nbrasp-xtvbaxfg.png)](https://lp.logrocket.com/blg/nextjs-signup)

[LogRocket](https://lp.logrocket.com/blg/nextjs-signup) is like a DVR for web and mobile apps, recording literally everything that happens on your Next.js app. Instead of guessing why problems happen, you can aggregate and report on what state your application was in when an issue occurred. LogRocket also monitors your app's performance, reporting with metrics like client CPU load, client memory usage, and more.

The LogRocket Redux middleware package adds an extra layer of visibility into your user sessions. LogRocket logs all actions and state from your Redux stores.

Modernize how you debug your Next.js apps — [start monitoring for free](https://lp.logrocket.com/blg/nextjs-signup).