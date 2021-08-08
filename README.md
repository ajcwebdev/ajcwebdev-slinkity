# Example Project from [A First Look at Slinkity](https://dev.to/ajcwebdev/a-first-look-at-slinkity-3ig)

Slinkity is an 11ty metaframework built with Vite that brings dynamic, client side interactions to your static 11ty sites. It enables turning existing `.html` or `.liquid` files into `.jsx` files. It aims to unify two competing camps in the current web development community:
* Lean, JavaScript-free static site generators driven by data and templating languages like Jekyll and Hugo.
* Dynamic, JavaScript-heavy web apps powered by data and React or Vue components like NextJS and NuxtJS.

Slinkity is in early alpha and not recommended for production use. You can report issues or log bugs [here](https://github.com/Holben888/slinkity/issues).

### Install dependencies

```bash
npm install
```

### Start development server

The `--incremental` flag can be used for faster builds during development.

```bash
npx slinkity --serve
```

This command starts the [11ty dev server in `--watch` mode](https://www.11ty.dev/docs/usage/#re-run-eleventy-when-you-save) to listen for file changes and [a Vite server](https://vitejs.dev/guide/#index-html-and-project-root) pointed at your 11ty build. Vite enables processing a range of file types including SASS and React.

```
[vite] 
[vite]   vite v2.4.4 dev server running at:
[vite] 
[vite]   > Local: http://localhost:8080/
[vite]   > Network: use `--host` to expose
[vite] 
[vite]   ready in 201ms.
[vite] 
[11ty] Writing _site/index.html from ./index.md (liquid)
[11ty] Wrote 1 file in 0.07 seconds (v1.0.0-canary.39)
[11ty] Watching...
```

Your components will be included in a directory called `components` inside 11ty's [`_includes`](https://www.11ty.dev/docs/config/#directory-for-includes) directory.

```jsx
// _includes/components/component.jsx
import React from "react"
const component = () => {
  return (
    <>
      <span>The quality or condition of a slinky</span>
    </>
  )
}
export default component
```

The `react` shortcode is included in `index.md`.

```markdown
# ajcwebdev-slinkity

{% react 'components/component' %}
```

![02-slinkity-site-with-react-shortcode](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/6zuzwkrddvln49uo99af.png)

### Build for production

To create a production build, run the following command:

```bash
npx slinkity
```

Your new site will appear in the `_site` folder or [wherever you tell 11ty to build your site](https://www.11ty.dev/docs/config/#output-directory).

## Deploy your site to Netlify

Include `npx slinkity` for the build command and `_site` for the publish directory in the `netlify.toml`.

```toml
[build]
  command = "npx slinkity"
  publish = "_site"
```

Connect your repo to Netlify and create a [custom domain name](https://ajcwebdev-slinkity.netlify.app/).

![03-slinkity-site-deployed-on-netlify](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/ruzxkcfanyik7y4wx88q.png)
