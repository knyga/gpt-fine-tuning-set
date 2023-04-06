There’s no doubt that Next.js is a leading solution for creating a full-stack web application. Some of the biggest and finest companies around the world are using this React framework such as Twitch, Nike, and Notion.

On October 2022, Vercel released one of its biggest updates in a brand new version: Next.js 13. This new release ships React 18 with it and some big new features.

_Read the full [Next.js 13](https://nextjs.org/blog/next-13) / [React 18](https://reactjs.org/blog/2022/03/29/react-v18.html) release notes to learn more._

Let’s see how we can manage videos in a Next.js 13 and React 18 web application with [api.video](http://api.video/) 🧘🏽♂️

## Create a Next.js 13 application 🚀

If you already have a Next.js project with a version under 13, you can upgrade it to v13 by [following this guide](https://nextjs.org/docs/upgrading). To keep this article simple, we will create a new application.

1.  Run one of the following commands in a new terminal and follow the command prompts
    

```
npx create-next-app@latest
# or
yarn create next-app
# or
pnpm create next-app
```

We will name our brand new application videos\_app. We will use TypeScript, ESLint (not mandatory), the src/ directory, and the app/ directory (since there is a new V13 feature available). Don’t configure any alias yet.

![Next.js prompt](https://img.api.video/1676967722-screenshot-2023-02-07-at-16-30-34.png?auto=format&fm=jpg)

2.  Open your app and run it 🏃🏽
    

```
npm run dev
# or
yarn dev
# or
pnpm dev
```

Everything is working fine? Good job! 🎉

💡 _Too long for you? Create a video-ready Next.js app with all api.video tools already installed in seconds with our official Next.js boilerplate! Read our short tutorial [here](https://api.video/blog/tutorials/api-video-next-js-boilerplate-start-your-video-project-the-easy-way)_ 🤓

## Our video application structure 🏗️

Our goal here is to build a basic but fully operational video application. To do so, we will divide it into 3 pages:

-   Our landing page to upload a video ⬆️
    
-   A player view where you can playback a video 📺
    
-   A videos list view where all of your videos will be displayed 🏄🏽♂️
    

Next.js provides a perfect navigation and page system that will help us build our project easily.

## Get your API key 🔑

To be able to upload and playback videos from [api.video](http://api.video/), create and customize video players, and do many more things, you need an API key. Get it from your [dashboard](https://dashboard.api.video/login) or [create a free account](https://dashboard.api.video/register) (yes, it’s free, really).

## A minimal layout first 💅🏽

The goal of this tutorial is to make a working video app without any great design. But we need a minimal layout: top navigation, and a little CSS to keep things readable. We will also create a loading component that is automatically displayed while the content of a route segment loads 😲

1.  Replace your `src/app/layout.tsx` code with the following one.
    

```
'use client'

import Link from 'next/link'
import { useRouter } from 'next/navigation'
import './globals.css'

export default function RootLayout({
  children,
}: {
  children: React.ReactNode
}) {
  const router = useRouter()
  return (
    <html lang="en">
      {/*
        <head /> will contain the components returned by the nearest parent
        head.tsx. Find out more at https://beta.nextjs.org/docs/api-reference/file-conventions/head
      */}
      <head />
      <body style={{display: 'flex', flexDirection: 'column', alignItems: 'center', padding: 60}}>
        <header style={{display: 'flex', gap: 15, alignSelf: 'flex-start', marginBottom: 30}}>
          <Link href="/">Upload a video</Link>
{/* We use the router here to revalidate our videos data on each page load */}
          <div style={{ cursor: 'pointer' }} onClick={() => router.push('/videos')}>Videos list</div>
        </header>
        {children}
      </body>
    </html>
  )
}
```

Next.js 13 `app` directory allows you to declare layout globally (root layout) and for specific pages (nested layouts). The `src/app/layout.tsx` file also replaces the previous `pages/_app.js` and `pages/_document.js`. _[Read more about it here.](https://beta.nextjs.org/docs/routing/pages-and-layouts#layouts)_

2.  Create a new file `src/app/loading.tsx` and add a simple component.
    

```
export default function Loading() {
  return <p>Loading...</p>
}
```

In Next.js 13, a `loading.[tsx,js]` file in the `app` directory is globally and automatically used by your whole application for loading state. How great it is! _[Read more about it here.](https://beta.nextjs.org/docs/data-fetching/streaming-and-suspense)_

## Upload a video with Next.js 13 ⬆️

In this section, we will create a page that allows a user to select a video file and upload it to [api.video](http://api.video/).

We will keep this view as our home page, the `src/app/page.tsx` file.

1.  We need to use the [api.video TypeScript video uploader](https://github.com/apivideo/api.video-typescript-uploader#readme) package to upload a video to your project. Install it by running one of the following commands
    

```
npm i @api.video/video-uploader
# or
yarn add @api.video/video-uploader
# or
pnpm i @api.video/video-uploader
```

2.  Edit src/app/page.tsx as follows
    

```
'use client'

import { ChangeEvent, useState } from 'react'
import { VideoUploader } from '@api.video/video-uploader'
import Link from 'next/link'

export default function Home() {
  const [uploading, setUploading] = useState<boolean>(false)
  const [progress, setProgress] = useState<number>(0)
  const [videoId, setVideoId] = useState<string | undefined>(undefined)

  async function handleFile(event: ChangeEvent<HTMLInputElement>) {
    if (!event.currentTarget.files) return
    try {
      setUploading(true)
      setVideoId(undefined)
      const videoUploader = new VideoUploader({
        uploadToken: 'YOUR_UPLOAD_TOKEN',
        file: event.currentTarget.files[0]
      })
      videoUploader.onProgress(e => setProgress(Math.round(e.uploadedBytes * 100 / e.totalBytes)))
      videoUploader.onPlayable(e => setVideoId(e.videoId))
      await videoUploader.upload()
    } catch (error) {
      console.error('Error trying to upload a video:', error)
    } finally {
      setUploading(false)
    }
  }

  return (
    <main>
      <h1>Upload a video</h1>
      <input type='file' onChange={handleFile} />
      <div>
        {(uploading && progress < 100) && `Video uploaded at ${progress}%`}
        {(progress >= 100 && !videoId) && 'Wait a second...'}
        {videoId && 'Your video is ready 🎉'}
      </div>
    </main>
  )
}
```

As you can see, you will need an upload token to upload a new video. You can create and find yours [here](https://dashboard.api.video/upload-tokens).

⚠️ _You can also decide to use an API key or an access token in the TypeScript video uploader, but it’s strongly discouraged because you’re exposing sensitive information_

Few things to notice here:

-   `'use client'` : By default, the app directory’s components are [server components](https://beta.nextjs.org/docs/rendering/server-and-client-components). These components are great for rendering non-interactive content. However, if you want to allow client interaction in your component (React hooks, event listeners, browser-only APIs), you need to add the `'use client'` directive at the top of your component file.
    
-   Next `<Link />` component: In Next.js 13, you don’t need to pass a `<a>` tag as children to the Link component.
    
-   `onProgress` and `onPlayable` events: our TypeScript video uploader allows you to track upload progress whenever the uploaded video is playable.
    

Congratulations! You now have implemented a video upload solution into a Next.js application 👏🏽 Pretty easy right? Let’s move on to the next part: display your video list.

## Display a videos list with Next.js 13 🏄🏽

You can now upload videos to your [api.video](http://api.video/) project. You can [watch and edit them through your dashboard](https://dashboard.api.video/videos). You can also implement this feature in your application. Let’s implement it together 🧑🏽💻

1.  We need to use the [api.video Node.js client](https://github.com/apivideo/api.video-nodejs-client) to access our videos. Install it by running one of the following commands
    

```
npm i @api.video/nodejs-client
# or
yarn add @api.video/nodejs-client
# or
pnpm i @api.video/nodejs-client
```

2.  Create a new videos directory under src/app/ and create a page.tsx file in it. In Next.js 13, pages that are created under the app directory need to be named page.tsx,js. Read more about it here.
    

![Next.js 13 app directory](https://img.api.video/1676968154-screenshot-2023-02-08-at-11-12-11.png?auto=format&fm=jpg)

3.  Edit your new src/app/videos/page.tsx file with the following code
    

```
import ApiVideoClient from '@api.video/nodejs-client'
import Link from 'next/link'

export default async function Videos() {
  const videoClient = new ApiVideoClient({ apiKey: process.env.API_KEY })
  const videos = await videoClient.videos.list()
  return (
    <>
      <div>Videos</div>
      {videos.data.length === 0 && <Link href="/">No videos! Upload one here!</Link>}
      {videos.data.map((video, i) => <Link key={i} href={`videos/${video.videoId}`}>{video.title}</Link>)}
    </>
  )
}
```

This component is a perfect example of a Server Component. Instead of using Next.js `api` directory or `getServerSideProps` to fetch our videos from [api.video](http://api.video/), we can directly do this from our component.

Congratulations! You displayed all your videos in seconds 🏎️

## Playback a video with Next.js 13 📺

In any application, a video needs a container to be played, which is often called a player. In our video app, we will use the [api.video React player](https://github.com/apivideo/api.video-react-player) to playback our videos.

We want to access and playback a video in two different ways:

-   Once a new video is uploaded and playable, we want to display a player in the same view to playback it directly.
    
-   If we go through our videos list, each item is a clickable link that redirects us to the video view, and thus the player.
    

1.  Install the React player package by running one of the following commands
    

```
npm i @api.video/react-player
# or
yarn add @api.video/react-player
# or
pnpm i @api.video/react-player
```

2.  Replace your src/app/page.tsx (the uploader page) code with the one bellow
    

```
'use client'

import { ChangeEvent, useState } from 'react'
import { VideoUploader } from '@api.video/video-uploader'
import Link from 'next/link'
import ApiVideoPlayer from '@api.video/react-player'

export default function Home() {
  const [uploading, setUploading] = useState<boolean>(false)
  const [progress, setProgress] = useState<number>(0)
  const [videoId, setVideoId] = useState<string | undefined>(undefined)

  async function handleFile(event: ChangeEvent<HTMLInputElement>) {
    if (!event.currentTarget.files) return
    try {
      setUploading(true)
      setVideoId(undefined)
      const videoUploader = new VideoUploader({
        uploadToken: 'toyfPisYyGhC4sYqnxMzEYz',
        file: event.currentTarget.files[0]
      })
      videoUploader.onProgress(e => setProgress(Math.round(e.uploadedBytes * 100 / e.totalBytes)))
      videoUploader.onPlayable(e => setVideoId(e.videoId))
      await videoUploader.upload()
    } catch (error) {
      console.error('Error trying to upload a video:', error)
    } finally {
      setUploading(false)
    }
  }

  return (
    <main>
      <h1>Upload a video</h1>
      <input type='file' onChange={handleFile} />
      <div>
        {(uploading && progress < 100) && `Video uploaded at ${progress}%`}
        {(progress >= 100 && !videoId) && 'Wait a second...'}
        {videoId && 'Your video is ready 🎉'}
      </div>
      {videoId && <ApiVideoPlayer video={{ id: videoId }} style={{ width: 500, height: 300 }} />}
    </main>
  )
}
```

A video player will now appear when your uploaded video is ready to be played.

3.  Create a new directory `src/app/videos/[videoId]` and a file `page.tsx` in it, and add the following code to it
    

```
'use client'

import ApiVideoPlayer from "@api.video/react-player"

type PlayerViewProps = {
  params: { videoId: string }
}

export default function PlayerView({ params }: PlayerViewProps) {
  return <ApiVideoPlayer video={{ id: params.videoId }} style={{ width: 500, height: 300 }} />
}
```

The new `app` directory lets you create dynamic routes as previously. Here, we are reading the `videoId` URL parameter with the reserved `params` Next.js parameter and sending it to our React player.

4.  Go back to your videos list view and try to click on one of them. You should be redirected to the player view where your video can be read 🎉
    

We are excited to see the creative solutions built with [api.video](http://api.video/) and Next.js 13! [Join our community](https://community.api.video/) to share anything about video with other passionate people 💬