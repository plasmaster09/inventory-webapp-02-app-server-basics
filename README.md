# Part 02: Web app server setup and basics

This tutorial follows after:
[Part 01: Protyping our web pages](https://github.com/atcs-wang/inventory-webapp-01-static-prototypes)

Technologies: [NodeJS](https://nodejs.org/en/), [Express](https://expressjs.com/)

MDN has a very good [starting tutorial for Express](https://developer.mozilla.org/en-US/docs/Learn/Server-side/Express_Nodejs/development_environment
); it is more fleshed out in parts, but could be a good additional reference.

The second layer of a web app is the **web application server**, or just "web server" or "app server", for short. In this tutorial, we will create a simple web server that serves your static prototype pages; in later sections. Later, this server will be greatly improved and expanded.

# Setting up a NodeJS / Express project

> This is adapted from [expressjs.com's installation instructions](https://expressjs.com/en/starter/installing.html).

If you haven't before, you will need to install NodeJS on your machine. Download an installer for the latest Long Term Support (LTS) version from the [NodeJS website](https://nodejs.org/en/), and run it.

Once installed, you can confirm your version of Node and the Node Package Manager (npm) with these terminal commands:
```
> node -v
> npm -v
```

Navigate to your project's root directory, and run the `npm init` command to create a  `package.json` file for your application. 
```
> npm init
```

This command prompts you for a number of things, such as the name and version of your application. For now, you can simply hit RETURN to accept the defaults for most of them, *except for this one*:
```
entry point: (index.js)
```
Enter `app.js`. (You can do another filename or change this later if you want)

Once the prompts are finished, note the contents of the `package.json` file.

> **What is the `package.json` file for?**
`package.json` functions a bit like a settings/configuration file for your whole project. Its most important function is keeping track of the package dependencies of your project, and is important going forward.

Now we can install the `express` package with `npm install`, like this:
```
> npm install express
```

It might take a moment to complete, but afterwards you should notice a few changes.

1. There is now a new subdirectory called `node_modules`, which contains a large number of other subdirectories, each of which correspond to an installed package. Although we only told `npm` to install `express`, it also installed all of `express`'s dependencies. (As of the time of writing this, installing `express` results in 50 packages total being installed). 

2. `package.json` has a new section called `dependencies`, which lists express and its (minimum) version. 
It might look something like this:
```json
"dependencies": {
    "express": "^4.17.2"
}
```
3. There is also a new file called `package-lock.json`. This contain a detailed list of every single installed packages and their exact versions.

Any time you install or update a package with `npm`, the files go into `node_modules`, and both `package.json` and `package-lock.json` are updated. The Node Package Manager (NPM) ensures that all your package dependencies are organized and tracked, and the versions are maintained nicely.

The biggest benefit of the Node Package Manager becomes clear when trying to share your project - say, as a git repository. Because all those installed packages in `node_modules` can be very big, we prefer not to track them or upload them to Github. 

Create a `.gitignore` file and add this line:

```
node_modules
```

Now, the packages themselves will not be tracked by git, and not uploaded to Github. *However, both `package.json` and `package-lock.json` are tracked.* So, if anyone clones your repository, they will *not* download a copy of all your package dependencies - but they *will* have the list of every dependency and their versions! 

Here's the fun part: you can (re-)install every dependency listed in `package.json` and `package-lock.json` with a single command:

```
npm install
```

This example's repository is the same - if you clone it, simply `npm install` to get dependencies set up before running.

> Strictly speaking, the `package-lock.json` is only necessary if exact package versioning is important. If only the `package.json` is available, `npm` may install any acceptable above-minimum version of direct dependencies, and will figure out the indirect dependencies.

# A "Hello world" example server

> This is adapted from [expressjs.com's "Hello World" instructions](https://expressjs.com/en/starter/hello-world.html), which includes a similar example which is live-editable in-browser!

Create a file called `app.js`. This will be our main point of entry for the web application server. 

Add the following code:

```js
//set up the server
const express = require( "express" );
const app = express();
const port = 8080;

// define a route for the default home page
app.get( "/", ( req, res ) => {
    res.send( "<h1>Hello world!</h1>" );
} );

// start the server
app.listen( port, () => {
    console.log(`App server listening on ${ port }. (Go to http://localhost:${ port })` );
} );
```

Then, run it from the terminal with: 
```
> node app.js
```

Open your browser and go to `http://localhost:8080`. You should see the "Hello world!" message.

You can type `Ctrl-C` in the terminal to stop the server. 

## What's going on with "Hello World"?

> Before continuing, you should have a basic understanding of HTTP and its associated vocabulary: 
> 1. A **client** web browser visiting a website sends an **HTTP request** to the site's web **server**, which is hosted at a certain **domain** (like `www.google.com` or `www.youtube.com`). 
> 2. In addition to a **domain**, HTTP requests are also made to a particular **endpoint** of the server, which is a **path** (like `/search` or `/video`) and a specific request **method** (like `GET` or `POST`). 
>    - Most of the request info is encoded in the **URL** of the website page: for example, the URL** of `atcs-wang.github.io/stuff.html` shows the domain is `atcs-wang.github.io` and the path is `/stuff.html`. (The default method is `GET`.)
> 3. The web server should be constantly listening for requests from clients, then send back an appropriate **HTTP response** with a **status code** (like `200 OK`, or `404 NOT FOUND`) and a **body** with the requested data (like HTML pages).
>
> If you need a refresher on how this process and vocabulary, watch this short video: _____

Even though this is basically the simplest possible express web server you can write, there's quite a lot going on! Let's break down a few key parts.

- The first section sets up the server: line 1 imports the "express" module using `require()`, which is used to create a server (`app`). A `port` number is also defined. During development/testing, the port can be any number from 1024 to 65353.

- The second section performs the "routing": configuring the server to handle certain HTTP request **endpoints** (aka **path** and **method**). The general pattern for defining a "route" looks like this:
    ```js
    app.METHOD(PATH, HANDLER)
    ```
  Here, `app.get('/', ... ` tells the app to listen for requests to the `/` **path** (aka the **root** path) with the `GET` **method**. (This is simply a request for the homepage.) The handler function looks like: 
  ```js
  (req, res) => {    
      res.send( "<h1>Hello world!</h1>" );
  }`
  ```
  This sends a response (via the object `res`) with a bit of HTML as the **body**. (The **status code** is automatically set to `200 OK`)
  
  > NOTE: We'll get into routing more deeply soon; if you wish, ExpressJS has a [more detailed primer](https://expressjs.com/en/guide/routing.html).

- Finally, the last section tell the server to start running; this makes the server "loop" repeatedly, listening for HTTP requests on the given `port` number. It prints a message to the console confirming that it has started.  (It doesn't stop until you manually stop it with `Ctrl-C` in the terminal.)
  > NOTE: Making the message string with backticks (\`) instead of typical single or double quotes lets us interpolate the value of `port` into the string with ``` `${ port }` ```. These kinds of strings are called "Template literals", and are very convenient for constructing strings with variable values.

- When we run `node app.js`, the server is merely running on your local computer or network - not configured receive requests from the wider internet. (Later, we'll get a cloud server with a **domain** for that). That's fine for now - browsers use the special pseudo-domain `localhost:PORT` to send HTTP requests to local servers running on certain ports.


> ### A quick note about `express-generator`
>After the "Hello World" app, many `express` tutorials (including the [MDN one](https://developer.mozilla.org/en-US/docs/Learn/Server-side/Express_Nodejs/development_environment) linked above) introduce a tool called `express-generator`, which sets up a 'skeleton' project and provides a lot of "boilerplate" code (that is, code that many projects start with). This can help jump start the building of a project, but can be bit overwhelming for a first-time learner.
>
>We won't use `express-generator` in this tutorial; rather, we'll build up our app's code and structure organically, understanding each new part as it becomes relevant and necessary. Eventually, our final structure will be very similar to `express-generator` produces, at which point you might better appreciate the tool for future projects.
>
> If you're curious enough to dig in now, Express has some [terse starting instructions for express-generator](https://expressjs.com/en/starter/generator.html), and MDN's has a [second part to their express tutorial](https://developer.mozilla.org/en-US/docs/Learn/Server-side/Express_Nodejs/skeleton_website) that explains the output of `express-generator` nicely.

# Making a simple app server for our prototypes 

Let's update to the "Hello World!" server to actually serve our prototype pages. 

## Adding and exploring more routes

The existing route handles the "/" path, which is the homepage.Let's start by adding two more routes:

```js
// define a route for the default home page
app.get( "/", ( req, res ) => {
    res.send( "<h1>Hello world!</h1>" );
} );

// define a route for the stuff inventory page
app.get( "/stuff", ( req, res ) => {
    res.send( "<h1>This is the stuff inventory page.</h1>" );
} );

// define a route for the item detail page
app.get( "/stuff/item", ( req, res ) => {
    res.send( "<h1>This is the item detail page.</h1>" );
} );
```

If you haven't yet, stop your old server (`Ctrl-C` in the terminal) and run it again (`node app.js`). If you forget to stop your old server, you'll get an error like `Error: listen EADDRINUSE: address already in use :::8080`. 

In your browser, go to `localhost:8080/stuff` and `localhost:8080/stuff/item` and see the corresponding messages. 

Then, try a path that wasn't defined in the routing, like `locahost:8080/notworking`. You should see a message that says `Cannot GET /notworking`. Express automatically handles any requests to endpoints we didn't set up routes for by sending this simple message.

If you use Chrome or Firefox, you can open your browser's Developer Tools and see the network activity. (right-click on your browser, choose **Inspect** and open the **Network** tab). This helps you see details about HTTP requests and responses, such as status codes. After opening, try hitting both valid and invalid routes again. The valid ones should have a status code of `200 OK` , and the invalid ones a `404 NOT FOUND`.

## Sending files in the responses

Right now, of course, the server only sends snippets of HTML. We want to send our prototype HTML pages, which are the files `index.html`, `stuff.html`, and `item.html`. 

First, let's organize our prototype files by placing them into a new subdirectory (aka folder) called `views`.

At this point, your file structure should look something like this:
```
|-node_modules
| |-...
|-views
| |-index.html
| |-item.html
| |-stuff.html
|-.gitignore
|-app.js
|-package-locks.json
|-package.json
```

Then, update the routing code to this:

```js
// define a route for the default home page
app.get( "/", ( req, res ) => {
    res.sendFile( __dirname + "/views/index.html" );
} );

// define a route for the stuff inventory page
app.get( "/stuff", ( req, res ) => {
    res.sendFile( __dirname + "/views/stuff.html" );
} );

// define a route for the item detail page
app.get( "/stuff/item", ( req, res ) => {
    res.sendFile( __dirname + "/views/item.html" );
} );
```

The `res.sendFile` method does exactly what you think it does: it sends a file, given by an absolute path, in the HTTP response. 

> To give the absolute path of the file, we use a special variable that exists in NodeJS called `__dirname`, which contains the path of the root directory of the program. Appended to that is the *relative* path of each file in the project: the containing `views` directory, then the file name. Keeping track of where everything is located is tricky - which is why organized file structure is important!

Restart the server, and try visiting your `localhost` pages in the browser again. Ta-da! You should see your prototype pages appear instead of the little fragments.

### Fixing page links

The only issue now is that the link-buttons that navigate between the pages themselves don't work correctly. They need to use the same routes we defined in the server.

The first is in `index.html`. Find this part:
```html
<a class="btn" href="stuff.html">...
```
and change the `href` so it says
```html
<a class="btn" href="/stuff">...
```
Now the "Get started!" button on the homepage should link to the stuff inventory page.

The second and third are in `stuff.html`. Find both instances of:
```html
<a class="btn-small waves-effect waves-light" href="item.html">
```
and change the `href` so it says
```html
<a class="btn-small waves-effect waves-light" href="/stuff/item">
```
Now the two "Info/Edit" buttons on the stuff inventory page should link to the item detail page.
