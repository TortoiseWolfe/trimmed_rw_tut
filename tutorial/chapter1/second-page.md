# A Second Page and a Link

```bash
yarn redwood generate page about
```

```jsx title="web/src/pages/HomePage/HomePage.tsx"
import { Link, routes } from '@redwoodjs/router'
import { Metadata } from '@redwoodjs/web'

const HomePage = () => {
  return (
    <>
      <Metadata title="Home" description="Home page" />

      // highlight-start
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
      <main>Home</main>
      // highlight-end
    </>
  )
}

export default HomePage
```

### Back Home

Once we get to the About page we don't have any way to get back so let's add a link there as well:

```jsx title="web/src/pages/AboutPage/AboutPage.tsx"
import { Link, routes } from '@redwoodjs/router'
import { Metadata } from '@redwoodjs/web'

const AboutPage = () => {
  return (
    <>
      <Metadata title="About" description="About page" />

      // highlight-start
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
      <main>
        <p>
          This site was created to demonstrate my mastery of Redwood: Look on my
          works, ye mighty, and despair!
        </p>
        <Link to={routes.home()}>Return home</Link>
      </main>
      // highlight-end
    </>
  )
}

export default AboutPage
```

Great! Try that out in the browser and verify that you can get back and forth.
As a world-class developer you probably saw that copy-and-pasted `<header>` and gasped in disgust. We feel you. That's why Redwood has a little something called _Layouts_.
