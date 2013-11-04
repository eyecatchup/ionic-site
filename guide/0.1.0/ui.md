---
layout: guide
title: "Building the UI for Toderp"
---

Previous: <a href="installation.html">Installing Ionic</a>

# Chapter 3: Building our UI

Now that we've got everything installed and a new Cordova project created, let's jump in and start building a real app!

The Todo list app is pretty much a right of passage for frameworks, so we will walk through the process of building one with Ionic.

Since every Ionic app is basically a web page, we need to have an `index.html` file in our app which defines the first page that loads in the app. Let's create `www/index.html` and initialize it with this:

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>Toderp</title>
    <meta name="viewport" content="initial-scale=1, maximum-scale=1, user-scalable=no">

    <link rel="stylesheet" href="/css/ionic.css">

    <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.2.0-rc.3/angular.min.js"></script>
    <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.2.0-rc.3/angular-touch.js"></script>
    <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.2.0-rc.3/angular-animate.js"></script>

    <script src="/js/ionic.js"></script>
    <script src="/js/ionic-angular.js"></script>

    <!-- Needed for Cordova/PhoneGap -->
    <script type="text/javascript" src="cordova.js"></script>
  </head>
  <body>
    <script>
      document.addEventListener('deviceready', function() {
        ionic.Platform.detect();
      });
    </script>
  </body>
</html>
```

Now that we have our starting place, we need to figure out what the interface layout of our application is going to be. We have a ton of choices when it comes to UI design on mobile. There are a few standard layouts, like tabs and side menus, but there are practically infinite custom layouts that we could implement if we really wanted to (which Ionic encourages!). For the sake of this example, we are going to pick a simple Side Menu layout which lets us drag and expose a side menu with center content.

In the center content area, we will list the various tasks that we have to get completed for the current project. We can add new tasks, or edit existing ones. If we drag the center content over to the right, we expose the left side menu which lets us choose the current project we want to edit, or create new projects.  Take a look at the mockup below for an example of what we are trying to build:

<img src="http://ionicframework.com.s3.amazonaws.com/guide/0.1.0/3-mockup.png" alt="Mockup">

To create side menus in Ionic, we can use the [Side Menu Controller](http://ionicframework.com/docs/controllers/side-menu). Feel free to read up on it, but the markup needed is simple. Edit the `index.html` file and change the `<body>` content to look like this:

```html
<body>
  <side-menu>
    <pane side-menu-content>
    </pane>
    <menu side="left">
    </menu>
  </side-menu>
</body>
```

In the code above, we've added our `<side-menu>` controller which will handle the draggng and exposing of the side menu. Inside of the controller we have a `<pane side-menu-content>` which is the center content area of the app, and a `<menu side="left">` which is a left, initially hidden, side menu.

Now, if you ran this code (more on testing in a bit), you wouldn't see anything! There are two reasons for that: The first is we haven't created an AngularJS app to turn the custom tags (like `<side-menu>`) into anything functional. And, because we don't have any content yet.

Let's fix that. First, we need to create a new AngularJS module and tell Angular to initialize it. Let's create a new file located at `www/js/app.js`. Put this code into the file:

```javascript
angular.module('todo', ['ionic'])
```

This is the Angular way of creating an application, and we are telling angular to include the `ionic` module which includes all of the Ionic code which will process the tags above and make our app come to life.

Now, go back to `index.html` and right before the ending `</head>` tag, add:

```html
<script src="js/app.js"></script>
```

This includes the script we just created.

Now, we still won't see anything if we ran the code, because we need to add some content to the application. Let's go ahead and add a header for both the center content area and the left menu.

Update the body content to be:

```html
<body ng-app="todo">
  <side-menu>

    <!-- Center content -->
    <pane side-menu-content>
      <header class="bar bar-header bar-dark">
        <h1 class="title">ToDo</h1>
      </header>
      <div class="content has-header">
      </div>
    </pane>

    <!-- Left menu -->
    <menu side="left">
      <header class="bar bar-header bar-dark">
        <h1 class="title">Projects</h1>
      </header>
    </menu>

  </side-menu>
</body>
```

## Testing your work

Now, since we actually have something to look at, we need to talk about the testing and development process for our app. There are three ways to test your app as you develop: in a desktop WebKit browser, in the iOS or Android simulator, or in a mobile browser on your phone.

Unless you are using custom Cordova plugins, or a lot of Cordova specific code, it's very easy to test in the desktop browser. The easiest thing is to `cd` into the `www` folder, and run

```bash
$ python -m SimpleHTTPServer 8000
```

This will start a tiny web server hosting all of the files in the `www` folder. All you have to do to test it is open Chrome or Safari and navigate to `http://localhost:8000`. You should see something that looks like this:

<img src="http://ionicframework.com.s3.amazonaws.com/guide/0.1.0/3-running.png" style="border: 1px solid #ccc; border-radius: 4px;" alt="Running">

You can also test right in the simulator using the cordova commands from the previous chapter. For example, to test in the iOS simulator, `cd` into the root of the project (one level up from the `www` folder), and run:

```bash
$ cordova build ios
$ cordova emulate ios
```

Substitute ios with android for Android testing. If you want to get advanced, you can also open up the project file for a specific platform by opening the required XCode or Android Eclipse project in `platforms/PLATFORM` inside the root of your project. Then, you can build and test from inside the platform-specific IDE. Note: if you go this route, I recommend still working inside of the root `www` folder, and when you've made changes to this folder, run the command:

```bash
$ cordova prepare ios
```

Which will update the ios specific project with the code from the `www` folder. Note: this will overwrite any changes you've made to the `platforms/ios/www` and other platform-specific folders.