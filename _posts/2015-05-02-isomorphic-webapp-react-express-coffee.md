---
layout:     post
title:			"Creating an Isomorphic Web Application with ReactJS and Express"
date:				2015-05-02 13:31:27
summary:    A brief overview of one of the most-adored features of Facebook's React Framework.
categories: ReactJS Isomorphic
---

> [React] is a Javascript framework for building user interfaces.

## Introduction

In our fast-changing world, technology is rapidly taking giant leaps forward. For the people who are just beginning to take part in this new "gold rush" of web development, it is sometimes inevitable to feel a sense of desperation of not being able to keep up with the evolution. In this article, I will attempt to give a brief overview of one of the most-adored features of Facebook's [React] framework—**isomorphism**(a.k.a. server side rendering).


*Note: This tutorial assumes that the reader has some foundational understanding in React. If not, try to follow along or take a look [here](https://facebook.github.io/react/docs/getting-started.html).*

Some terms: [CoffeeScript], [JSX], [getInitialState], [Gulp]

[React]: https://facebook.github.io/react/
[CoffeeScript]: http://coffeescript.org/
[Gulp]: http://gulpjs.com/
[JSX]: https://facebook.github.io/react/jsx-compiler.html
[getInitialState]: https://facebook.github.io/react/docs/component-specs.html#getinitialstate

## What are we creating?

[Demo] | [Code]

We will be creating a minimal skeleton of an isomorphic web application, that is, a React application that pre-renders on the server.

[Demo]: http://react-starter-coffee.herokuapp.com/
[Code]: https://github.com/ben-z/react-starter-coffee-tutorial

## Why are we making an isomorphic web application?

The isomorphic approach has many benefits over client-only or server-only applications:

1. SEO Friendly - unlike traditional AngularJS applications, isomorphic React components render on the server, which enables web crawlers that don't run client scripts such as the Google bot see the rendered webpage instead of something like `<h1>{% raw %}{{TITLE}}{% endraw %}</h1>`.

2. Dynamic - after rendering on the server, the static HTML gets sent to the client, and the client automatically appends dynamic components such as event listeners (button click listeners) to the page.

3. Maintainable - the client and the server share the same code, so when something goes wrong, or when a feature needs to be added, only one set of code is altered.

## Step 1: Understanding the Folder Structure

Everything starts with an idea, then a plan. Here's our plan:

```
.
├── /build/                     # The folder for compiled output
├── /src/                       # The source code of the application
│   ├── /app/                   # Javascript files
│   |   ├── /components/        # components of the application
│   |   └── /index.cjsx         # Entry-point / wrapper of the application
│   ├── /scss/                  # Stylesheets, written in scss
│   └── /server.coffee          # Server-side scripts
│── gulpfile.js                 # Configuration file for automated builds
└── package.json                # Information about the project (Dependencies, etc.)
```

1. The `src` folder is where we place our code.
  * `server.coffee` contains code to start the server, it is the only file that is considered non-isomorphic.
  * `app` contains the components of our application, everything in here is written in [CoffeeScript] and [JSX]. CoffeeScript is an awesome language that compiles into javascript, and JSX is a syntax that allows us to put HTML code into javascript so it is more readable, more on that later.
  * `scss` is where we put our stylesheets. Like its name, we are writing our style code in [SCSS]. If you are not familiar with SCSS, worry not, it is virtually identical to CSS, except for its support for mixins and other features.
2. The `build` folder is where our compiled code lives.
  * `server.coffee` is compiled into `server.js`.
  * CoffeeScript-JSX files (`.cjsx`) are compiled into Javascript files (`.js`).
  * SCSS files (`.scss`) are compiled into CSS files (`.css`).
3. `gulpfile.js` is where we tell the program to compile the files in `src` to `build`, it will be discussed in more depth later on.
4. `package.json` is where we store all the information about our project, such as its name, version, author, dependencies, homepage, and repository.

[SCSS]: http://sass-lang.com/

## Step 2: Set up `package.json`

1. Make a new folder named **MyApp** and place it on the Desktop.
2. Open **Terminal** or something similar, and type in:

    ```bash
    $ cd ~/Desktop # Press enter
    $ cd MyApp # Remember not to type in the dollar sign
    ```

3. Then execute:

    ```bash
    $ npm init
    ```

    This allows us to enter our information about the application. Follow the prompt.

    ![Output of 'npm init']({{site.baseurl}}assets/isomorphic-webapp-react-express-coffee/npm-init.png)

4. Check inside the **MyApp** folder, you should see a file named `package.json`.

## Step 3: Create `Server.coffee`

Now we can set up our server script, and we will be using [Express] as our server framework.

1. Open **MyApp** with your favorite text editor, I will be using [Atom] because it is free and open source.

2. Create a folder named **src**.

3. In the folder, create a new file named `server.coffee`.

4. The code:

    ```coffeescript
    # Express server
    express = require 'express'
    # Create an Express object named 'app'
    app = express()
    # Port of the server
    port = process.env.PORT || 3000
    # Entry point of our app
    index = require './app/index'

    # serve static files
    app.use '/js', express.static(__dirname+'/'+(process.env.JS_FOLDER || 'app'))
    app.use '/css', express.static(__dirname+'/'+(process.env.CSS_FOLDER || 'css'))

    # This starts the server
    app.listen port, ->
      console.log 'Listening on http://localhost:' + port
    ```

    Above snippet should be fairly straight-forward:
    * We create an `app` object from the `express` library.
    * We specify the server port from an environment variable with a fallback to port `3000`.
    * We set the entry point of this app as `./app/index` (the `.cjsx` extension is omitted because Express is smart enough to figure it out.)
    * The `express.static` functions tell the express app to server static `js` and `css` contents to the client, fulfilling our 'isomorphic' principle.
    * `app.listen` starts the server.

[Express]: http://expressjs.com/
[Atom]: https://atom.io/

## Step 4: The `app` Folder

Since we have just created the server code, we are ready to dive into the real application, the React components.

##### index.cjsx

1. Create an **app** folder in **src**.

2. Create `index.cjsx` in the folder.
  * `.cjsx` stands for **CoffeeScript JSX**. It combines these two awesome utilities into one file.
3. The contents of `index.cjsx`:

    ```coffeescript
    # Node does not have a define function, so we use amdefine
    if typeof define != 'function'
      define = require('amdefine')(module)

    # RequireJS implementation, works on Node as well as in the browser
    define (require) ->

      React = require('react')

      # Index is a wrapper for all components of the website
      Index = React.createClass(
        # Specifying prop types
        propTypes:
          component: React.PropTypes.string.isRequired
          title: React.PropTypes.string
          data: React.PropTypes.object

        # Render the template and pass data to child
        render: ->
          # The component should always have the same name as the folder
          C = require './components/' + @props.component + '/' + @props.component
          # Get the title, use default if null
          t = @props.title or 'React Coffeescript Starter Template'
          # Get default props for child, use {} if null
          data = @props.data or {}
          # Pre-render content
          preRendered = React.renderToString(<C {...data} />)
          # Render script for the client
          s = '
          require.config({
              baseUrl: "/js/",
              paths:{
                  "react": "https://cdnjs.cloudflare.com/ajax/libs/react/0.13.2/react",
              },
              waitSeconds: 7
          });

          require(["react","components/'+@props.component+'/'+@props.component+'"], function(React, page){
            React.render(
              React.createElement(page,'+safeStringify(data)+'),
              document.getElementById("content")
            );
          });';

          return (
            <html>
              <head>
                <meta name="viewport" content="width=device-width, initial-scale=1" />
                <title>{t}</title>
                <link rel="stylesheet" href="css/main.css" />
              </head>
              <body>
                <div id="content" dangerouslySetInnerHTML={__html:preRendered} />
                <script data-main="index" src="https://cdnjs.cloudflare.com/ajax/libs/require.js/2.1.17/require.js"></script>
                <script dangerouslySetInnerHTML={__html:s} />
              </body>
            </html>
          )
      )

      # A better JSON.stringify
      safeStringify = (obj) ->
        JSON.stringify(obj)
                .replace(/<\/script/g, '<\\/script')
                .replace(/<!--/g, '<\\!--');

      return Index

    ```

    The `define = require('amdefine')(module)` at the top makes isomorphism possible: the client-side Javascript doesn't support the `require` keyword, so we have to bring it in with [RequireJS], but at the same time, the server (NodeJS) doesn't support the `define` keyword of RequireJS, so we need to use the `amdefine` package. The `if typeof define != 'function'` means "if the `define` keyword is not present(on the server), then we use the `amdefind` package."

    **Index** accepts three props:

    1. the `name` of the child component

    2. the `title` of the page

    3. the `props` of the child component

    The rest of the code should be straight-forward if you have previous experiences with React. The `s` variable contains client javascript and is injected into client HTML so that it can be triggered in the browser.

[RequireJS]: http://requirejs.org/

##### An aside about **JSX**

  Note how we used the following code in our javascript:

  ```coffeescript
  return (
    <html>
      <head>
        <meta name="viewport" content="width=device-width, initial-scale=1" />
        <title>{t}</title>
        <link rel="stylesheet" href="css/main.css" />
      </head>
      <body>
        <div id="content" dangerouslySetInnerHTML={__html:preRendered} />
        <script data-main="index" src="https://cdnjs.cloudflare.com/ajax/libs/require.js/2.1.17/require.js"></script>
        <script dangerouslySetInnerHTML={__html:s} />
      </body>
    </html>
  )
  ```

  It is almost identical to regular HTML, which is the highlight of JSX. The non-jsx code would look like:

  ```coffeescript
  return (
    React.createElement 'html', null, React.createElement('head', null, React.createElement('meta',
    name: 'viewport'
    content: 'width=device-width, initial-scale=1'), React.createElement('title', null, t), React.createElement('link',
    rel: 'stylesheet'
    href: 'css/main.css')), React.createElement('body', null, React.createElement('div', { id: 'content' }, preRendered), React.createElement('script',
    'data-main': 'index'
    src: 'https://cdnjs.cloudflare.com/ajax/libs/require.js/2.1.17/require.js'), React.createElement('script', null, s))
  )
  ```

  and it can be very difficult to read. Luckily, Facebook has its own [JSX Compiler](https://facebook.github.io/react/jsx-compiler.html) that we can use to view the difference between the two right in the browser.

##### Components

  1. Create a folder **components** in **app**

  2. Create a folder **welcome** in **components**.

  3. Create `welcome.cjsx` inside the **welcome** folder.

  4. The contents of `welcome.cjsx`:

    ```coffeescript
    # Node does not have a define function, so we use amdefine
    if typeof define != 'function'
      define = require('amdefine')(module)

    # RequireJS implementation, works on Node as well as in the browser
    define (require) ->

      React = require 'react'

      Welcome = React.createClass(
        _handleClick: ->
          alert 'hiii'
        render: ->
          <div>
            <div id="top-bar">
              <a href="#">Some Link</a>&nbsp;|&nbsp;
              <a href="#">Some Link</a>&nbsp;|&nbsp;
              <a href="#">Some Link</a>&nbsp;|&nbsp;
              <button onClick={@_handleClick}>A button</button>
            </div>
            <h1>Hello, world.</h1>
            <p>This is a React starter template written in coffeescript.</p>
            <p>Hey, I have a <a href="https://benzhang.xyz/">blog</a>!</p>
            <p><i className="fa fa-star"></i> Star this project on <a href="https://github.com/ben-z/React-Express-Starter-Template"> Github</a></p>
          </div>
      )

      return Welcome
    ```

    This is a typical React component, with a top bar and a button that greets you when clicked.

## Step 5: Adding a route

  How should the server respond to requests? In this section we will tell the server to display our welcome page.

  In `server.coffee`, add this below `index = require'./app/index`:

  ```coffeescript
  React = require 'react'
  ```

  and add these lines before `app.listen`:

  ```coffeescript
  # Routes
  app.get '/', (req, res) ->
    rendered = React.renderToStaticMarkup(React.createElement(index, {component:'welcome'}))
    res.send(rendered)
  ```

  Above tells the program to create a React element from Index and ask the element to render the `welcome` component. Then React is instructed to render this element to a string, which then gets passed to the client through `res.send`.

  At this point our entire `server.coffee` should look something like this:

  ```coffeescript
  # Express server
  express = require 'express'
  # Create an Express object named 'app'
  app = express()
  # Port of the server
  port = process.env.PORT || 3000
  # Entry point of our app
  index = require './app/index'
  # This is a ReactJS app
  React = require 'react'

  # serve static files
  app.use '/js', express.static(__dirname+'/'+(process.env.JS_FOLDER || 'app'))
  app.use '/css', express.static(__dirname+'/'+(process.env.CSS_FOLDER || 'css'))

  # Routes
  app.get '/', (req, res) ->
    rendered = React.renderToStaticMarkup(React.createElement(index, {component:'welcome'}))
    res.send(rendered)

  # This starts the server
  app.listen port, ->
    console.log 'Listening on http://localhost:' + port
  ```

## Step 6: Setting Up Gulp

  This step should be one of the most exciting in the tutorial, because we finally get the see what we've made in action!

  1. In the root of the project, create a file named `gulpfile.js`

  2. The contents of `gulpfile.js`:

    ```js
    var cjsx = require('gulp-cjsx');
    var gulp = require('gulp');
    var gutil = require('gulp-util');
    var coffee = require('gulp-coffee');
    var spawn = require('child_process').spawn;
    var node;
    var del = require('del');
    var fs = require('fs');
    var sass = require('gulp-sass');

    var buildDir = './build/';

    // Compile Coffeescript jsx files
    gulp.task('cjsx', function() {
      gulp.src('./src/**/*.cjsx')
        .pipe(cjsx({bare: true}).on('error', gutil.log))
        .pipe(gulp.dest(buildDir));
    });

    // Compile Coffeescript
    gulp.task('coffee', function() {
      gulp.src('./src/**/*.coffee')
        .pipe(coffee({bare: true}).on('error', gutil.log))
        .pipe(gulp.dest(buildDir))
    });

    gulp.task('build',['cjsx', 'coffee']);

    // Start the server in ./build
    gulp.task('server', function() {
      if (node) node.kill();
      if(!fs.existsSync(buildDir+'server.js')){
        console.log(buildDir+'server.js not found, retrying...');
        return setTimeout(function () {
          if(!fs.existsSync('./build')){
            console.log('./build folder not found, please run \'gulp build\'');
          }else{
            gulp.start('server');
          }
        }, 2000);
      }
      node = spawn('node', [buildDir+'server.js'], {stdio: 'inherit'});
      node.on('close', function (code) {
        if (code === 8) {
          console.log('Error detected, waiting for changes...');
        }
      });
    });

    // Reloads the server
    gulp.task('reload', function(){
      if (node) {
        console.log('Reloading Server');
        gulp.start('server');
      }
    });

    // Watches for file changes
    gulp.task('watch', function(){
      gulp.watch('./src/**/*.cjsx', ['cjsx','reload']);
      gulp.watch('./src/**/*.coffee', ['coffee','reload']);
      gulp.watch('./src/.env.yml', ['env','reload']);
    });

    gulp.task('develop', ['build', 'server', 'watch']);

    gulp.task('clean', function () {
      del([
        './build'
      ]);
    });

    gulp.task('start_server', ['build', 'server']);

    // kill the node process on exit
    process.on('exit', function() {
        if (node) node.kill();
    });
    ```

    Since Gulp is not the focus of our tutorial, this script basically compiles all of our files in the **src** directory and puts them into **build**, and it has the ability to start the server and watch for changes while we develop.

    Now try to run:

    ```bash
    $ gulp build
    ```

    You should get an error message, either `Local gulp not found` or `command not found`. Don't worry, we haven't installed our dependencies.

## Step 7: Dependencies

Our app depends on other libraries such as Express, React, and Gulp, and we need to tell the app to use them.

##### Gulp

```bash
$ npm install -g gulp --save
```

This command installs gulp globally (so we can run `gulp` directly) and saves the dependency in `package.json`.

##### Other Dependencies

```bash
$ npm install --save amdefine basscss-sass express react del gulp gulp-cjsx gulp-coffee gulp-sass gulp-util
```

Note that `gulp` appeared in both commands. It is not an error, we need the global gulp command as well as a local copy of it. The 'node_modules' folder is where all the dependency files live.

## Step 8: Back to Gulp

Now it's time to build our project, run:

```bash
$ gulp build
```

A new folder named 'build' should appear in the directory. Run the project using:

```bash
$ node build/server.js
```

Navigate to the URL and you will see your project live! Click on the button and it says 'hiii'.

![App without styles]({{site.baseurl}}assets/isomorphic-webapp-react-express-coffee/app-without-styles.png)

## Step 9: CSS

To make our website a bit more beautiful, we will add some styling to it.

1. Create a folder **scss** in **src**.

2. In the folder create a file named `main.scss`.

3. Contents of `main.scss`:

    ```scss
    @import "https://cdnjs.cloudflare.com/ajax/libs/normalize/3.0.3/normalize.min.css";
    @import "https://maxcdn.bootstrapcdn.com/font-awesome/4.2.0/css/font-awesome.min.css";
    body {
      text-align: center;
    }
    a {
      color:#0074d9;
      text-decoration:none;

      &:hover {
        text-decoration:underline
      }
    }
    ```

    This is some very simple styling with a font package from [Font Awesome].

4. Add a script to `gulpfile.js` to compile the stylesheets:

    ```js
    gulp.task('scss', function () {
      gulp.src('./src/scss/*.scss')
        .pipe(sass())
        .pipe(gulp.dest(buildDir+'css'));
    });
    ```

    Don't forget to change the build command so that it compiles `scss`:

    ```js
    gulp.task('build',['cjsx', 'coffee', 'scss']);
    ```

    And the watch command:

    ```js
    // Watch for file changes
    gulp.task('watch', function(){
      gulp.watch('./src/**/*.cjsx', ['cjsx','reload']);
      gulp.watch('./src/**/*.coffee', ['coffee','reload']);
      gulp.watch('./src/scss/*.scss', ['scss']);
      gulp.watch('./src/.env.yml', ['env','reload']);
    });
    ```

[Font Awesome]: http://fortawesome.github.io/Font-Awesome/

## Step 10: Wrapping Up

Finally, run:

```bash
$ gulp build
$ node build/server.js
```

or more simply:

```bash
$ gulp develop
```

You will see that the project is prettier than ever before!

![The pretty app]({{site.baseurl}}assets/isomorphic-webapp-react-express-coffee/the-pretty-app.png)


### Conclusion

Congratulations on your first isomorphic React application! If you have read this far, you are truly determined to make the best of this tutorial, thank you so much! Please feel free to let me know of any questions, concerns, or comments below. [Create an issue] if you find a bug and [send a pull request] if you have improvements to make!

[Create an issue]: https://github.com/ben-z/react-starter-coffee-tutorial/issues
[send a pull request]: https://github.com/ben-z/react-starter-coffee-tutorial/pulls

[View the complete source code here](https://github.com/ben-z/react-starter-coffee-tutorial)

### Next Steps & Further Reading

* [Express + React Starter Template (Coffeescript)](https://github.com/ben-z/react-starter-coffee) - template from this tutorial plus MongoDB integration and multiple components.
* [Getting Started with React.js](http://facebook.github.io/react/)
* [Isomorphic JavaScript: The Future of Web Apps](http://getawesomeness.com/get/react)
* [React.js Wiki on GitHub](https://github.com/facebook/react/wiki)
* [React.js Questions on StackOverflow](http://stackoverflow.com/questions/tagged/reactjs)
* [React.js Discussion Board](https://groups.google.com/forum/#!forum/reactjs)
