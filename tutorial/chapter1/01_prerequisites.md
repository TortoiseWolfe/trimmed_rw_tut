# Prerequisites

### Node.js and Yarn Versions

During installation, RedwoodJS checks if your system meets version requirements for Node and Yarn:

- node: "=20.x"
- yarn: ">=1.22.21"

If you're using a version of Node or Yarn that's **less** than what's required, _the installation bootstrap will result in an ERROR_. To check, please run the following from your terminal command line:

```bash
node --version
yarn --version
```

Please do upgrade accordingly. Then proceed to the Redwood installation when you're ready!

:::info Installing Node and Yarn

There are many ways to install and manage both Node.js and Yarn. If you're installing for the first time, we recommend the following:

**1. Node.js**
Using the recommended [LTS version from Nodejs.org](https://nodejs.org/en/) is preferred.

- `nvm` is a great tool for managing multiple versions of Node on one system. It takes a bit more effort to set up and learn, however. Follow the [nvm installation instructions](https://github.com/nvm-sh/nvm#installing-and-updating). (Windows users should go to [nvm-windows](https://github.com/coreybutler/nvm-windows/releases)). For **Mac** users with Homebrew installed, you can alternatively use it to [install `nvm`](https://formulae.brew.sh/formula/nvm). Or, refer to our how to guide [using nvm](../../how-to/using-nvm.md).

**2. Yarn**
As of Node.js v18+, Node.js ships with a CLI tool called [Corepack](https://nodejs.org/docs/latest-v18.x/api/corepack.html) to manage package managers. All you have to do is enable it, then you'll have Yarn:

```
corepack enable
yarn -v
```

The version of Yarn will probably be `1.22.21`, but don't worry—in your Redwood project, Corepack will know to use a modern version of Yarn because of the `packageManager` field in the root `package.json`.

**Windows:** Recommended Development Setup

- JavaScript development on Windows has specific requirements in addition to Yarn and npm. Follow our simple setup guide:

  [Recommended Windows Development Setup](../../how-to/windows-development-setup.md)

:::
