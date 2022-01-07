# NextJS starter

# Development Procedures

## 1. How do you create routes or pages?

- If you create index.js in the pages folder, it will create Home route without implementing a router.
- The name of the file becomes an URL.

## 2. What is pre-rendering in NextJS?

- For client service rednering - if the user has a slow PC, it will take some time. Also, JavaScript needs to be enabled though it is not common.
- However, with the NextJS, it uses the real HTML. The user will be able to see the site even if the JavaScript is disabled. They will see even if they do not have a connection. Hydration
    - render > HTML > show to the user.
- pre-generated state with HTML shown by users. Then, other components of JavaScript renders.
- This is really good for SEO.

## 3. Routing

- We shouldn't use anchor tag when navigating because the browser will reload completely to render a new page. This will slow down the rendering speed.
- Therefore, we put <Link> around it as below. Then, the website won't reload.

NavBar.js
```js
import Link from "next/link";
import { useRouter } from "next/router";

export default function NavBar() {
    // useRouter() hook will connect to the router. This is provided by the nextjs.
    // If you console log router, it contains all the information about the paths.
  const router = useRouter();
  return (
    <nav>
        {/* when the path name is "/" or "about", the color of the link text will change to red.*/}
      <Link href="/">
        <a style={{ color: router.pathname === "/" ? "red" : "blue" }}>Home</a>
      </Link>
      <Link href="/about">
        <a style={{ color: router.pathname === "/about" ? "red" : "blue" }}>
          About
        </a>
      </Link>
    </nav>
  );
}
```

## 4. CSS Modules

- In order to implement CSS, we use modules. 
- Where is the module?

components/NavBar.module.css
```css
.link {
  text-decoration: none;
}

.active {
  color: tomato;
}
```

components/NavBar.js
```js
import Link from "next/link";
import { useRouter } from "next/router";
// import style.
import styles from "./NavBar.module.css";

export default function NavBar() {
  const router = useRouter();
  return (
    <nav>
      <Link href="/">
      {/* 
      Use a style from styles import.
      If the pathname matches, it will apply the second style.    
      */}
        <a
          className={`${styles.link} ${
            router.pathname === "/" ? styles.active : ""
          }`}
        >
          Home
        </a>
      </Link>
      <Link href="/about">
        <a
          className={[
            styles.link,
            router.pathname === "/about" ? styles.active : "",
          ].join(" ")}
        >
          About
        </a>
      </Link>
```

- When you add your className? 

## 5. Styles JSX

- There is another way of styling. This is called styles JSX. 
- This is typical only to nextjs.


```js
import Link from "next/link";
import { useRouter } from "next/router";

export default function NavBar() {
  const router = useRouter();
  return (
    <nav>
      <Link href="/">
        <a className={router.pathname === "/" ? "active" : ""}>Home</a>
      </Link>
      <Link href="/about">
        <a className={router.pathname === "/about" ? "active" : ""}>About</a>
      </Link>
      {/* style jsx gets applied on tags and classes .(class) (tag) */}
      <style jsx>{`
        nav {
          background-color: tomato;
        }
        a {
          text-decoration: none;
        }
        .active {
          color: yellow;
        }
      `}</style>
    </nav>
  );
}
```

## 6. Custom App

- How do you create a global style?
```js
<style jsx global>
```
- if you want your style to have the global on other component, there has to be another way besides copy and pasting it. 

- Instead, you want a blueprint that you want to have styles applied to all files.

_app.js (need to be underscode in front)

```js
import NavBar from "../components/NavBar";
// all the styles in the globals.css will be applied to the app that works as a blueprint of all pages.
import "../styles/globals.css";

export default function MyApp({ Component, pageProps }) {
  return (
    <>
      <NavBar />
      <Component {...pageProps} />
    </>
  );
}
```

pages/about.js
```js
export default function Potato() {
  return (
    <div>
      <h1>About</h1>
    </div>
  );
```

- When about page is called, the nextjs will call the Component and put it in MyApps as a prop. App renders it inside.

- Where is the global.css file?

styles/globals.css
- This was given as a default.

# 7. Recap of a framework.

- Library vs Framework
- you call vs you follow rules.
- pages folder > you can create page.js or _app.js
    - page.js will create a new route with the name of the .js file. 
- Create-React-App: Clinent Server Render.
- NextJS: pre-render html > add Javascript components. Even if Javascript does not render, it is going to show the html. 
    - This helps with the SEO.
- Not every page has a Navigation bar.


## 8. Layout pattern

- Create components/Layout.js
- Layout have a children.

- If you implement a head, it will automatically create a header.
    - It will change the tab title just like the react-helmet.

Layout.js
```js
import NavBar from "./NavBar";

export default function Layout({ children }) {
  return (
    <>
      <NavBar />
      <div>{children}</div>
    </>
  );
}

```
- You can create a seperate component that handles this Head.

Seo.js
```js
import Head from "next/head";

export default function Seo({ title }) {
  return (
    <Head>
      <title>{title} | Next Movies</title>
    </Head>
  );
}
```

_app.js
```js
import Layout from "../components/Layout";
import "../styles/globals.css";

export default function MyApp({ Component, pageProps }) {
  return (
    <Layout>
      <Component {...pageProps} />
    </Layout>
  );
}
```

## 9. Fetch data

*How do you hide your API?*

index.js

```js
import { useEffect, useState } from "react";
import Seo from "../components/Seo";

const API_KEY = "6a76e3254d065f99647ffb4eb0404c5c";

export default function Home() {
  const [movies, setMovies] = useState();

  // 
  useEffect(() => {
    (async () => {
      const { results } = await (
        await fetch(
          `https://api.themoviedb.org/3/movie/popular?api_key=${API_KEY}`
        )
      ).json();
      setMovies(results);
    })();
  }, []);
  return (
    <div>
      <Seo title="Home" />
      {!movies && <h4>Loading...</h4>}
      {movies?.map((movie) => (
        <div key={movie.id}>
          <h4>{movie.original_title}</h4>
        </div>
      ))}
    </div>
  );
}

```

- If you create an API request, anybody could view the API key if you look at request tab from a development mode.

- You can also redirect using the next.config.js

next.config.js
```js
const API_KEY = process.env.API_KEY;

module.exports = {
  reactStrictMode: true,
  // if any users go to localhose/old-blog, it will redirect to the new-sexy-blog.
  // :path* is any paths.
  async redirects() {
    return [
      {
        source: "/old-blog/:path*",
        destination: "/new-sexy-blog/:path*",
        permanent: false,
      },
    ];
  },
  // you can also rewrite tha path. This will mask the url from developer mode. The inspector will see /api/movies instead of the full URL to the API.
  async rewrites() {
    return [
      {
        source: "/api/movies",
        destination: `https://api.themoviedb.org/3/movie/popular?api_key=${API_KEY}`,
      },
    ];
  },
};
```