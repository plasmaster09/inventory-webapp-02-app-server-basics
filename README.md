# Part 02: Web app server basics

This tutorial follows after:
[Part 01: Protyping our web pages](https://github.com/atcs-wang/inventory-webapp-01-static-prototypes)

Technologies: [NodeJS](https://nodejs.org/en/), [Express](https://expressjs.com/)

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

> Strictly speaking, the `package-lock.json` is only necessary if exact package versioning is important. If only the `package.json` is available, `npm` may install any acceptable above-minimum version of direct dependencies, and will figure out the indirect deprendencies.