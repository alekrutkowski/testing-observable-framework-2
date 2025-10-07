<strong>This is a simple example of an  [Observable Framework](https://observablehq.com/framework/) app
which fetches the data from Eurostat (instead of using the pre-fetched data which is
usually done via "data loaders" located in the `data` directory).</strong>

The app is coded in [`src/index.md`](https://github.com/alekrutkowski/testing-observable-framework-2/blob/main/src/index.md?plain=1).
It can be compiled to a web page ([`dist`](https://github.com/alekrutkowski/testing-observable-framework-2/tree/main/dist)directory) with `npm run build`. \
(See https://observablehq.com/framework/getting-started#2-develop for instructons).

<strong>A version compiled with GitHub Actions is accessible at https://alekrutkowski.github.io/testing-observable-framework-2/.</strong> \
(See https://observablehq.com/framework/deploying for instructions).

For other usage examples see:

- Observablehq's "Observable Framework" markdown file example with Web-R (https://docs.r-wasm.org/webr), Grid.js table (https://gridjs.io), and a global spinner:<br>https://gist.github.com/alekrutkowski/e8052bb1ffbc812a6540eee20249ac59
- Observablehq's "Observable Framework" markdown file example with a nice input output layout (1:3), Excel file upload, processing, and download:<br>https://gist.github.com/alekrutkowski/0649e6dcb0fd4b18e4ea473f1d4309be
- Double range slider (2 values, e.g. min and max) for Observablehq's "Observable Framework" markdown:<br>https://gist.github.com/alekrutkowski/4249df138da5c2554a734b860bebff06
- Using selectize-like input in Observablehq's "Observable Framework" markdown:<br>https://gist.github.com/alekrutkowski/06fb805fc78ac882a15142dac60404e7
  
<br>

---

# Hello Framework

This is an [Observable Framework](https://observablehq.com/framework/) app. To install the required dependencies, run:

```
npm install
```

Then, to start the local preview server, run:

```
npm run dev
```

Then visit <http://localhost:3000> to preview your app.

For more, see <https://observablehq.com/framework/getting-started>.

## Project structure

A typical Framework project looks like this:

```ini
.
├─ src
│  ├─ components
│  │  └─ timeline.js           # an importable module
│  ├─ data
│  │  ├─ launches.csv.js       # a data loader
│  │  └─ events.json           # a static data file
│  ├─ example-dashboard.md     # a page
│  ├─ example-report.md        # another page
│  └─ index.md                 # the home page
├─ .gitignore
├─ observablehq.config.js      # the app config file
├─ package.json
└─ README.md
```

**`src`** - This is the “source root” — where your source files live. Pages go here. Each page is a Markdown file. Observable Framework uses [file-based routing](https://observablehq.com/framework/project-structure#routing), which means that the name of the file controls where the page is served. You can create as many pages as you like. Use folders to organize your pages.

**`src/index.md`** - This is the home page for your app. You can have as many additional pages as you’d like, but you should always have a home page, too.

**`src/data`** - You can put [data loaders](https://observablehq.com/framework/data-loaders) or static data files anywhere in your source root, but we recommend putting them here.

**`src/components`** - You can put shared [JavaScript modules](https://observablehq.com/framework/imports) anywhere in your source root, but we recommend putting them here. This helps you pull code out of Markdown files and into JavaScript modules, making it easier to reuse code across pages, write tests and run linters, and even share code with vanilla web applications.

**`observablehq.config.js`** - This is the [app configuration](https://observablehq.com/framework/config) file, such as the pages and sections in the sidebar navigation, and the app’s title.

## Command reference

| Command           | Description                                              |
| ----------------- | -------------------------------------------------------- |
| `npm install`            | Install or reinstall dependencies                        |
| `npm run dev`        | Start local preview server                               |
| `npm run build`      | Build your static site, generating `./dist`              |
| `npm run deploy`     | Deploy your app to Observable                            |
| `npm run clean`      | Clear the local data loader cache                        |
| `npm run observable` | Run commands like `observable help`                      |
