# Getting Dynamic

### Creating the Database Schema

First let's define the data structure for a post in the database. Open up `api/db/schema.prisma` and add the definition of our Post table (remove any "sample" models that are present in the file, like the `UserExample` model). Once you're done, the entire schema file should look like:

```javascript title="api/db/schema.prisma"
datasource db {
  provider = "sqlite"
  url      = env("DATABASE_URL")
}

generator client {
  provider      = "prisma-client-js"
  binaryTargets = "native"
}

// highlight-start
model Post {
  id        Int      @id @default(autoincrement())
  title     String
  body      String
  createdAt DateTime @default(now())
}
// highlight-end
```

This says that we want a table called `Post` and it should have:

- An `id` column of type `Int` lets Prisma know this is the column it should use as the `@id` (for it to create relationships to other tables) and that the `@default` value should be Prisma's special `autoincrement()` method letting it know that the DB should set it automatically when new records are created
- A `title` field that will contain a `String`
- A `body` field that will contain a `String`
- A `createdAt` field that will be a `DateTime` and will `@default` to `now()` when we create a new record (so we don't have to set the time manually in our app, the database will do it for us)

### Migrations

Now we'll want to snapshot the schema changes as a migration:

```bash
yarn rw prisma migrate dev
```

From now on we'll use the shorter `rw` alias instead of the full `redwood` argument.

You'll be prompted to give this migration a name. Something that describes what it does is ideal, so how about "create post" (without the quotes, of course). This is for your own benefit—neither Redwood nor Prisma care about the migration's name, it's just a reference when looking through old migrations and trying to find when you created or modified something specific.

After the command completes you'll see a new subdirectory created under `api/db/migrations` that has a timestamp and the name you gave the migration. It will contain a single file named `migration.sql` that contains the SQL necessary to bring the database structure up-to-date with whatever `schema.prisma` looked like at the time the migration was created. So, you always have a single `schema.prisma` file that describes what the database structure should look like right _now_ and the migrations trace the history of the changes that took place to get to the current state. It's kind of like version control for your database structure, which can be pretty handy.

In addition to creating the migration file, the above command will also execute the SQL against the database, which "applies" the migration. The final result is a new database table called `Post` with the fields we defined above.

### Prisma Studio

A database is a pretty abstract thing: where's the data? What's it look like? How can I access it without creating a UI in my web app? Prisma provides a tool called [Studio](https://www.prisma.io/studio) which provides a nice web app view into your database:


(Ours won't have any data there yet.) To open Prisma Studio, run the command:

```bash
yarn rw prisma studio
```

A new browser should open to [http://localhost:5555](http://localhost:5555) and now you can view and manipulate data in the database directly!


Click on "Post" and you'll see an empty database table. Let's have our app start putting some posts in there!

### Creating a Post Editor

We haven't decided on the look and feel of our site yet, but wouldn't it be amazing if we could play around with posts without having to build a bunch of pages that we'll probably throw away once the design team gets back to us? As you can imagine, we wouldn't have thrown around this scenario unless Redwood had a solution!

Let's generate everything we need to perform all the CRUD (Create, Retrieve, Update, Delete) actions on posts so we can not only verify that we've got the right fields in the database, but that it will let us get some sample posts in there so we can start laying out our pages and see real content. Redwood has a _generator_ for just this occasion:

```bash
yarn rw g scaffold post
```

Let's point the browser to [http://localhost:8910/posts](http://localhost:8910/posts) and see what we have:

### Creating a Blog Homepage

We could start replacing these pages one by one as we settle on a look and feel for our blog, but do we need to? The public facing site won't let viewers create, edit or delete posts, so there's no reason to re-create the wheel or update these pages with a look and feel that matches the public facing site. Why don't we keep these as our admin pages and create new ones for the public facing site.

Let's think about what the general public can do and that will inform what pages we need to build:

1. View a list of posts (without links to edit/delete)
2. View a single post

Starting with #1, we already have a `HomePage` which would be a logical place to view the list of posts, so let's just add the posts to the existing page. We need to get the content from the database and we don't want the user to just see a blank screen in the meantime (depending on network conditions, server location, etc), so we'll want to show some kind of loading message or animation. And if there's an error retrieving the data we should handle that as well. And what about when we open source this blog engine and someone puts it live without any content in the database? It'd be nice if there was some kind of blank slate message until their first post is created.

Oh boy, our first page with data and we already have to worry about loading states, errors, and blank slates...or do we?
