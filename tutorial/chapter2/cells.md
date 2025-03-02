# Cells

### Our First Cell

```bash
yarn rw g cell Articles
```

This command will result in a new file at `/web/src/components/ArticlesCell/ArticlesCell.{jsx,tsx}` (and `test.{jsx,tsx}` `mock.{js,ts}` and `stories.{jsx,tsx}` files—more on those in [chapter 5 of the tutorial](../chapter5/storybook.md)!). This file will contain some boilerplate to get you started:

```tsx title="web/src/components/ArticlesCell/ArticlesCell.tsx"
import type { ArticlesQuery, ArticlesQueryVariables } from 'types/graphql'

import type {
  CellFailureProps,
  CellSuccessProps,
  TypedDocumentNode,
} from '@redwoodjs/web'

export const QUERY: TypedDocumentNode<ArticlesQuery, ArticlesQueryVariables> =
  gql`
    query ArticlesQuery {
      articles {
        id
      }
    }
  `

export const Loading = () => <div>Loading...</div>

export const Empty = () => <div>Empty</div>

export const Failure = ({
  error,
}: CellFailureProps<ArticlesQueryVariables>) => (
  <div style={{ color: 'red' }}>Error: {error.message}</div>
)

export const Success = ({
  articles,
}: CellSuccessProps<ArticlesQuery, ArticlesQueryVariables>) => {
  return (
    <ul>
      {articles.map((item) => {
        return <li key={item.id}>{JSON.stringify(item)}</li>
      })}
    </ul>
  )
}
```

However, this is not a valid query name for our existing Posts SDL (`api/src/graphql/posts.sdl.{js,ts}`) and Service (`api/src/services/posts/posts.{js,ts}`). (To see where these files come from, go back to the [Creating a Post Editor section](getting-dynamic.md#creating-a-post-editor) in the _Getting Dynamic_ part.) Redwood names the query elements after the cell itself for convenience (more often than not you'll be creating a cell for a specific model), but in this case our cell name doesn't match our model name so we'll need to make some manual tweaks.

We'll have to rename them to `posts` in both the query name and in the prop name in `Success`:

```tsx title="web/src/components/ArticlesCell/ArticlesCell.tsx"
import type { ArticlesQuery, ArticlesQueryVariables } from 'types/graphql'

import type {
  CellFailureProps,
  CellSuccessProps,
  TypedDocumentNode,
} from '@redwoodjs/web'

export const QUERY: TypedDocumentNode<ArticlesQuery, ArticlesQueryVariables> =
  gql`
    query ArticlesQuery {
      // highlight-next-line
      posts {
        id
      }
    }
  `

export const Loading = () => <div>Loading...</div>

export const Empty = () => <div>Empty</div>

export const Failure = ({
  error,
}: CellFailureProps<ArticlesQueryVariables>) => (
  <div style={{ color: 'red' }}>Error: {error.message}</div>
)

// highlight-next-line
export const Success = ({
  posts,
}: CellSuccessProps<ArticlesQuery, ArticlesQueryVariables>) => {
  return (
    <ul>
      // highlight-next-line
      {posts.map((item) => {
        return <li key={item.id}>{JSON.stringify(item)}</li>
      })}
    </ul>
  )
}
```


Let's plug this cell into our `HomePage` and see what happens:

```tsx title="web/src/pages/HomePage/HomePage.tsx"
import { Metadata } from '@redwoodjs/web'

// highlight-next-line
import ArticlesCell from 'src/components/ArticlesCell'

const HomePage = () => {
  return (
    <>
      <Metadata title="Home" description="Home page" />
      // highlight-next-line
      <ArticlesCell />
    </>
  )
}

export default HomePage
```

The browser should actually show the `id` and a GraphQL-specific `__typename` properties for any posts in the database. If you just see "Empty" then return to the scaffold we created [last time](getting-dynamic.md#creating-a-post-editor) and add a couple. Neat!


In fact, let's use the aforementioned alias so that the name of our cell, and the data we're iterating over, is consistent:

```tsx title="web/src/components/ArticlesCell/ArticlesCell.tsx"
export const QUERY: TypedDocumentNode<ArticlesQuery, ArticlesQueryVariables> =
  gql`
    query ArticlesQuery {
      // highlight-next-line
      articles: posts {
        id
      }
    }
  `

export const Loading = () => <div>Loading...</div>

export const Empty = () => <div>Empty</div>

export const Failure = ({
  error,
}: CellFailureProps<ArticlesQueryVariables>) => (
  <div style={{ color: 'red' }}>Error: {error.message}</div>
)

export const Success = ({
  // highlight-next-line
  articles,
}: CellSuccessProps<ArticlesQuery, ArticlesQueryVariables>) => {
  return (
    <ul>
      // highlight-next-line
      {articles.map((item) => {
        return <li key={item.id}>{JSON.stringify(item)}</li>
      })}
    </ul>
  )
}
```

In addition to the `id` that was added to the `query` by the generator, let's get the `title`, `body`, and `createdAt` values as well:

```tsx title="web/src/components/ArticlesCell/ArticlesCell.tsx"
export const QUERY: TypedDocumentNode<ArticlesQuery, ArticlesQueryVariables> =
  gql`
    query ArticlesQuery {
      articles: posts {
        id
        // highlight-start
        title
        body
        createdAt
        // highlight-end
      }
    }
`
```

The page should now show a dump of all the data you created for any blog posts you scaffolded:

<img src="https://user-images.githubusercontent.com/300/145911009-b83fd07f-0412-489c-a088-4e89faceea1c.png" alt="Articles with all DB values" />

Now we're in the realm of good ol' React components, so just build out the `Success` component to display the blog post in a nicer format:

```tsx title="web/src/components/ArticlesCell/ArticlesCell.tsx"
export const Success = ({
  articles,
}: CellSuccessProps<ArticlesQuery, ArticlesQueryVariables>) => {
  return (
    // highlight-start
    <>
      {articles.map((article) => (
        <article key={article.id}>
          <header>
            <h2>{article.title}</h2>
          </header>
          <p>{article.body}</p>
          <div>Posted at: {article.createdAt}</div>
        </article>
      ))}
    </>
    // highlight-end
  )
}
```

And just like that we have a blog! It may be the most basic blog that ever graced the internet, but it's something! You can create/edit/delete posts and the world can view them on the homepage. (Don't worry, we've got more features to add.)

![Nicely formatted blog articles](https://user-images.githubusercontent.com/300/145911342-b3a4bb44-e635-4bc5-8df7-a824661b2714.png)

### Summary

To recap, what did we actually do to get this far?

1. Generate the homepage
2. Generate the blog layout
3. Define the database schema
4. Run migrations to update the database and create a table
5. Scaffold a CRUD interface to the database table
6. Create a cell to load the data and take care of loading/empty/failure/success states
7. Add the cell to the page

The last few steps will become a standard lifecycle of new features as you build a Redwood app.

So far, other than a little HTML, we haven't had to do much by hand. And we especially didn't have to write a bunch of plumbing just to move data from one place to another. It makes web development a little more enjoyable, don't you think?

We're going to add some more features to our app, but first let's take a detour to learn about how Redwood accesses our database and what these SDL and services files are for.
