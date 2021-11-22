# An Example Slinkity Project from [A First Look at Slinkity](https://dev.to/ajcwebdev/a-first-look-at-slinkity-3ig)

Slinkity is a framework that uses Vite to bring dynamic, client side interactions to your static 11ty sites. It was announced by [Ben Holmes](https://twitter.com/BHolmesDev) with a Tweet on [June 14, 2021](https://twitter.com/BHolmesDev/status/1404426841440538627) and released as an alpha version on [August 8, 2021](https://www.npmjs.com/package/slinkity).

## ⛔️ WARNING - ALPHA SOFTWARE ⛔️

Slinkity is in early alpha and not recommended for production use unless you like living on the edge and filing issues. You can report issues or log bugs [here](https://github.com/Holben888/slinkity/issues).

![00-slinkity-banner](https://github.com/slinkity/slinkity-starter/raw/main/assets/social-media-banner.jpg)

Slinkity enables turning existing `.html` or `.liquid` files into `.jsx` files. It allows you to insert components into pages with shortcodes such as, `{% react './path/to/Hello.jsx' %}`. Because component-driven pages are hydrated on the client, dynamic state management works in both development and production. It aims to unify two competing camps in the current web development community:
* Lean, JavaScript-free static site generators driven by data and templating languages like Jekyll and Hugo.
* Dynamic, JavaScript-heavy web apps powered by data and React or Vue components like NextJS and NuxtJS.

### Install dependencies and start development server

`yarn start` runs `slinkity --serve` to start [a Vite server](https://vitejs.dev/guide/#index-html-and-project-root) pointed at your 11ty build. The `--incremental` flag can be used for faster builds during development.

```bash
yarn
yarn start
```

Open [localhost:8080](http://localhost:8080/) to view your site. Vite enables processing a range of file types including SASS and React.

### Available Scripts

Commands include `yarn start` for `slinkity --serve` and `yarn build` for `slinkity`. Running `slinkity` creates a production build. Your new site will appear in the `_site` folder or [wherever you tell 11ty to build your site](https://www.11ty.dev/docs/config/#output-directory). For production builds, Eleventy first builds all your routes to a temporary directory and then Vite picks up all the resource bundling, minification, and final optimizations to build your intended output from this temporary directory.

When using the `slinkity` command, all arguments are passed directly to the `eleventy` CLI except `serve` and `port`:
* `serve` starts the [11ty dev server in `--watch` mode](https://www.11ty.dev/docs/usage/#re-run-eleventy-when-you-save) to listen for file changes.
* Slinkity spins up an independent Vite server instead of 11ty's Browsersync server. `port` is for our own server which needs to be picked up and passed to Vite.

The CLI checks for Eleventy configs and will look for any custom directories returned such as input or output. If found, those are passed off to the Vite server so it can look in the right place.

![01-slinkity-architecture](https://raw.githubusercontent.com/slinkity/slinkity/main/assets/architecture-diagram.jpg)

### `.eleventy.js`

Slinkity relies on 11ty's [latest 1.0 beta build](https://www.npmjs.com/package/@11ty/eleventy/v/beta) to work properly. Our `.eleventy.js` file includes setting the input directory to `src`.

```js
// .eleventy.js

module.exports = function (eleventyConfig) {
  return {
    dir: {
      input: 'src',
    },
  }
}
```

## React Component Shortcodes

Your components will be included in a directory called `components` inside 11ty's [`_includes`](https://www.11ty.dev/docs/config/#directory-for-includes) directory. This is where all your imported components should live. Slinkity will always copy the contents of `_includes/components/` to the build for Vite to pick up. If you place your components anywhere outside of here, Vite won't be able to find them!

### `Hello.jsx`

The `Hello.jsx` component returns some text contained in `span` tags.

```jsx
// src/_includes/components/Hello.jsx

import React from "react"

const Hello = () => {
  return (
    <>
      <span>The quality or condition of a slinky</span>
    </>
  )
}

export default Hello
```

### `Counter.jsx`

In the `Counter.jsx` component a new state variable is declared called `count`.

```jsx
// src/_includes/components/Counter.jsx

import React, { useState } from 'react'

function Counter() {
  const [count, setCount] = useState(0)

  return (
    <div>
      <p>You've had {count} glasses of water 💧</p>

      <button onClick={() => setCount(count + 1)}>
        Add one
      </button>
    </div>
  )
}

export default Counter
```

With the `react` [shortcode](https://www.11ty.dev/docs/shortcodes/), you can insert components into any static template that 11ty supports. Include `react` shortcode in `index.md` and pass the path to your components. `_includes` and `.jsx` are optional in our shortcode.

```md
# ajcwebdev-slinkity

{% react 'components/Hello' %}

{% react 'components/Counter' %}
```

This will find `_includes/component/Counter.jsx` and `_includes/component/Hello.jsx`, statically render the components, insert them as HTML, and then hydrate the HTML rendered with our JavaScript components.

![02-counter-component-add-one](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/6awcc8rnb5h8gu6uqanb.png)

## Component Page

Component pages are like any other template on your 11ty site. Templates are the files that define your contents. In a blog, for instance, this could be the Markdown file that contains your blogpost.

### `about.jsx`

Say we wanted to create an `/about` page with an interactive image carousel. We can create an `about.jsx` file alongside the other pages on our site.

```jsx
// src/about.jsx

import React from 'react'

function About() {
  return (
    <h2>This page tells you stuff about things!</h2>
  )
}

export default About
```

Make sure to include that trailing slash `/` for our Vite server to find the page. This is because our JS bundle lives on `/about`, which trips up the Vite development server.

![03-about-page](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/pv8ih67mr5h4t9sxo7ce.png)

## Layouts

Slinkity is wrapping our component with some `html` and `body` tags automatically. However, if we have metadata or extra wrapper elements to include, it is useful to create a layout template. You can learn more about layout chaining [here](https://www.11ty.dev/docs/layouts/).

### `layout.html`

Populate `layout.html` with content.

```html
<!-- src/_includes/layout.html -->

<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">

    <title>{{ title }}</title>
  </head>

  <body>
    {{ content }}
  </body>
</html>
```

The `title` and `content` keys are now accessible from any layout templates applied to our page.

1. `{{ title }}` uses the "title" attribute from our page's front matter
2. `{{ content }}` renders our component page

### Applying front matter

Include `frontMatter` in `about.jsx` to wire up the layout. Front matter works the same way for component-based pages as it does for [11ty's front matter](https://www.11ty.dev/docs/data-frontmatter/). You can think of front matter as a way to pass information "upstream" for other templates to read from.

```jsx
// src/about.jsx

import React from 'react'

export const frontMatter = {
  title: 'About me',
  layout: 'layout.html',
}

function About() {
  return (
    <h2>This page tells you stuff about things!</h2>
  )
}

export default About
```

![04-about-page-with-front-matter](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/bni7arabferu2v73nftm.png)

## Deployment

Slinkity projects can be hosted with the same `build` command and `publish` directory on any of the common Jamstack hosting providers such as [Netlify](https://ajcwebdev-slinkity.netlify.app/), [Vercel](https://ajcwebdev-slinkity.vercel.app/), or [Cloudflare Pages](https://ajcwebdev-slinkity.pages.dev/). All three of these options allow you to create a custom domain name as well.

### Deploy to Netlify

Include `npx slinkity` for the build command and `_site` for the publish directory in `netlify.toml`.

```toml
[build]
  command = "npx slinkity"
  publish = "_site"
```

![05-slinkity-site-deployed-on-netlify](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/ruzxkcfanyik7y4wx88q.png)
