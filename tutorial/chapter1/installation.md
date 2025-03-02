# Installation & Starting Development

We'll use yarn ([yarn](https://yarnpkg.com/getting-started/install) is a requirement) to create the basic structure of our app:

```bash
yarn create redwood-app --ts ./redwoodblog
```

You'll have a new directory `redwoodblog` containing several directories and files. Change to that directory and we'll start the development server:

```bash
cd redwoodblog
yarn install
yarn redwood dev
```

A browser should automatically open to [http://localhost:8910](http://localhost:8910) and you will see the Redwood welcome page:

### First Commit


```bash
git init
git add .
git commit -m 'First commit'
```
