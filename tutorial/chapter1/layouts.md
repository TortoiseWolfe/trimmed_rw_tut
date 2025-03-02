# Layouts

Let's create a layout to hold that `<header>`:

```bash
yarn redwood g layout blog
```

From now on we'll use the shorter `g` alias instead of `generate`

That created `web/src/layouts/BlogLayout/BlogLayout.{jsx,tsx}` and associated test and stories files. We're calling this the "blog" layout because we may have other layouts at some point in the future (an "admin" layout, perhaps?).

Cut the `<header>` from both `HomePage` and `AboutPage` and paste it in the layout instead. Let's take out the duplicated `<main>` tag as well:

```jsx title="web/src/layouts/BlogLayout/BlogLayout.tsx"
// highlight-next-line
import { Link, routes } from '@redwoodjs/router'

type BlogLayoutProps = {
  children?: React.ReactNode
}

const BlogLayout = ({ children }: BlogLayoutProps) => {
  return (
    // highlight-start
    <>
      <header>
        <h1>Redwood Blog</h1>
        <nav>
          <ul>
            <li>
              <Link to={routes.about()}>About</Link>
            </li>
          </ul>
        </nav>
      </header>
      <main>{children}</main>
    </>
    // highlight-end
  )
}

export default BlogLayout
```

```jsx title="web/src/pages/AboutPage/AboutPage.tsx"
import { Link, routes } from '@redwoodjs/router'
import { Metadata } from '@redwoodjs/web'

const AboutPage = () => {
  return (
    <>
      <Metadata title="About" description="About page" />

      <p>
        This site was created to demonstrate my mastery of Redwood: Look on my
        works, ye mighty, and despair!
      </p>
      <Link to={routes.home()}>Return home</Link>
    </>
  )
}

export default AboutPage
```

```jsx title="web/src/pages/HomePage/HomePage.tsx"
import { Metadata } from '@redwoodjs/web'

const HomePage = () => {
  return (
    <>
      <Metadata title="Home" description="Home page" />
      Home
    </>
  )
}

export default HomePage
```

In `BlogLayout.{jsx,tsx}`, `children` is where the magic will happen. Any page content given to the layout will be rendered here. And now the pages are back to focusing on the content they care about (we can remove the import for `Link` and `routes` from `HomePage` since those are in the Layout instead).

To actually render our layout we'll need to make a change to our routes files. We'll wrap `HomePage` and `AboutPage` with the `BlogLayout`, using a `<Set>`. Unlike pages, we do actually need an `import` statement for layouts:

```tsx title="web/src/Routes.tsx"
// highlight-start
import { Router, Route, Set } from '@redwoodjs/router'
import BlogLayout from 'src/layouts/BlogLayout'
// highlight-end

const Routes = () => {
  return (
    <Router>
      // highlight-start
      <Set wrap={BlogLayout}>
        <Route path="/about" page={AboutPage} name="about" />
        <Route path="/" page={HomePage} name="home" />
      </Set>
      // highlight-end
      <Route notfound page={NotFoundPage} />
    </Router>
  )
}

export default Routes
```

### Back Home Again

A couple more `<Link>`s: let's have the title/logo link back to the homepage, and we'll add a nav link to Home as well:

```jsx title="web/src/layouts/BlogLayout/BlogLayout.tsx"
import { Link, routes } from '@redwoodjs/router'

type BlogLayoutProps = {
  children?: React.ReactNode
}

const BlogLayout = ({ children }: BlogLayoutProps) => {
  return (
    <>
      <header>
        // highlight-start
        <h1>
          <Link to={routes.home()}>Redwood Blog</Link>
        </h1>
        // highlight-end
        <nav>
          <ul>
            // highlight-start
            <li>
              <Link to={routes.home()}>Home</Link>
            </li>
            // highlight-end
            <li>
              <Link to={routes.about()}>About</Link>
            </li>
          </ul>
        </nav>
      </header>
      <main>{children}</main>
    </>
  )
}

export default BlogLayout
```

And then we can remove the extra "Return to Home" link (and Link/routes import) that we had on the About page:

```jsx title="web/src/pages/AboutPage/AboutPage.tsx"
import { Metadata } from '@redwoodjs/web'

const AboutPage = () => {
  return (
    <>
      <Metadata title="About" description="About page" />

      <p>
        This site was created to demonstrate my mastery of Redwood: Look on my
        works, ye mighty, and despair!
      </p>
    </>
  )
}

export default AboutPage
```

![image](https://user-images.githubusercontent.com/300/145901020-1c33bb74-78f9-415e-a8c8-c8873bd6630f.png)

Now we're getting somewhere! We removed all of that duplication and our header content (logo and navigation) are all in one place.

Everything we've done so far has been on the web side, which is all in the browser. Let's start getting the backend involved and see what all the hoopla is about GraphQL, Prisma and databases.
