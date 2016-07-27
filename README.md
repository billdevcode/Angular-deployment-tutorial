# Angular-deployment-tutorial
macOS, Yeoman, AngularJS 1.x, Grunt, Github, Heroku

**Purpose:** The purpose for writing this post is to share what I have learned over the three days (through trial and error) about using [Grunt](http://gruntjs.com). I hope that it’ll get you up and running within the hour. I will also be editing to this article as I learn more from the community and documentations.

**Context:** My team and I had seven days to learn enough about a new framework to build our final project at [Dev Bootcamp](http://devbootcamp.com), a coding bootcamp. We decided to use an AngularJS front-end that connected to our Rails API back-end. While it was easy to deploy our Rails API to Heroku, we were unsuccessful at deploying our front-end. Since we used [Yeoman](http://yeoman.io/) to scaffold our Angular template, the Yeoman system included Grunt. Grunt is essentially a task runner that automates tasks that you would do repeatedly. Since we had little time to learn about how Grunt worked, we did not successfully deploy our Web App to Heroku at the time. Since then, our Web App, UpLift, has been deployed to both Github pages and Heroku. The lesson that we learned is to deploy early and often. Here are the steps that I’ve compiled since that project. Think of this as building an Angular template Web App from scratch.

## Setup and steps on macOS:

I will use the **$** to represent the mac terminal prompt and **< >** as placeholder for your project's name.

* Install Node.js to your system by downloading [NodeJS](https://nodejs.org/en)
* To check if you already have a version of node:
```
$ node -v
```
* Since [node package manager](https://www.npmjs.com/) (npm) comes with the node install, all you’ll have to do is to do is to make sure that it’s up-to-date:
```
$ npm install npm -g
```
* To install Yeoman:
```
$ npm install -g yo
```
* To install the Angular generator:
```
$ npm install -g generator-angular
```
* Make your project folder and change into said project folder:
```
$ mkdir <your-project-name>-app && cd $_
```
* Track your files with Git
```
$ git init
```
* Add Github remote
```
$ git remote add origin <your-project-repo.git>
```
* Add, commit, and push to Github
* Inside your project folder:
```
$ yo angular <your-project-name>-app
```
* At this point, Yeoman will walk you through a basic setup prior to scaffolding your project. For my setup, I said **no** to Gulp, **no** to Sass/compass, and **yes** to Bootstrap. After that, I used the default Angular modules.
* If you wish to use [Karma](https://www.npmjs.com/package/grunt-karma) with Grunt to run tests:
```
$ npm install grunt-karma --save-dev
```
* To see what the scaffolded your-project-app looks like on your localhost, type:
```
$ grunt serve
```
* At this point, I would recommend that you spend some time looking through the **Gruntfile.js** in the **root of your project folder** to see all of the available tasks that you can type into your command line. For example, ```$ grunt test``` will run tests that you would write under the **test/spec** folder and ```$ grunt``` will minify (one of several tasks) a copied version of your files and place those files into a **dist** (distribution) folder. The advantage of having these automation task commands is that you can continuous build and test your Web App locally and deploy them quickly to check for matching functionality.

## Onto deployment

* Within your project folder:
```
$ npm install grunt-build-control --save-dev
```
* Then navigate to the [grunt-build-control](https://github.com/robwierzbowski/grunt-build-control) Github page, and use those instructions to complete your **Gruntfile.js**. I will outline what I’ve used here.
* Paste the following into your **Gruntfile.js**.
```
grunt.loadNpmTasks(‘grunt-build-control’);
```
* Next, refer to the **Usage** section on the grunt-build-control Github page and copy the example tasks into your **Gruntfile.js**. Below is my **Gruntfile.js** at this step of the tutorial. Do note that you’ll have to insert this block of tasks and make sure that each task is separated by a comma. This means that the task preceding ```buildcontrol: {…}``` would require a comma.
```
'use strict';

module.exports = function (grunt) {

  grunt.loadNpmTasks(‘grunt-build-control’); //<---INSERT HERE

  // Time how long tasks take. Can help when optimizing build times
  require('time-grunt')(grunt);

  // Automatically load required Grunt tasks
  require('jit-grunt')(grunt, {
    useminPrepare: 'grunt-usemin',
    ngtemplates: 'grunt-angular-templates',
    cdnify: 'grunt-google-cdn'
  });

  // Configurable paths for the application
  var appConfig = {
    app: require('./bower.json').appPath || 'app',
    dist: 'dist'
  };

  // Define the configuration for all the tasks
  grunt.initConfig({

    // Project settings
    yeoman: appConfig,

    ...// I've hidden the code in-between for brevity

    // Run some tasks in parallel to speed up the build process
    concurrent: {
      server: [
        'copy:styles'
      ],
      test: [
        'copy:styles'
      ],
      dist: [
        'copy:styles',
        'imagemin',
        'svgmin'
      ]
    },   // Don't forget the comma here

    buildcontrol: {
      options: {
        dir: 'dist',
        commit: true,
        push: true,
        message: 'Built %sourceName% from commit %sourceCommit% on branch %sourceBranch%'
      },
      pages: {
        options: {
          remote: 'git@github.com:example_user/example_webapp.git',
          branch: 'gh-pages'
        }
      },
      heroku: {
        options: {
          remote: 'git@heroku.com:example-heroku-webapp-1988.git',
          branch: 'master',
          tag: pkg.version
        }
      },
      local: {
        options: {
          remote: '../',
          branch: 'build'
        }
      }
    }, // Don't forget the comma here

    // Test settings
    karma: {
      unit: {
        configFile: 'test/karma.conf.js',
        singleRun: true
      }
    }
  });


  grunt.registerTask('serve', 'Compile then start a connect web server', function (target) {
    ...// I've hidden the code in-between for brevity
  });

  ...// I've hidden the code in-between for brevity
};

```


