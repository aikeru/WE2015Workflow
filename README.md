# Web Essentials 2014 Workflow in 2015

### Goals
Here are the features Web Essentials 2014 provides that we may want:

* Linting on save
* Minify JavaScript, CSS on save
* Transpile LESS, CoffeeScript, TypeScript on save

We're going to take an existing ASP.NET MVC project and add these features to it.

### One-time Setup
In the past Web Essentials used its own copy of node.js.
If you already use node.js, you can skip step 1. If you already use gulp, you can skip step 2.

#### Step 1:
Install node.js
https://nodejs.org/download/
How to check?
Open a new command session and ```run node -v```
My version is ```v0.12.0```
Run ```npm -v```
My version is ```2.5.1```

Did something break?
Check that the folder node is installed in is in your PATH. 64-bit node installs here:
```C:\Program Files\nodejs```
	
#### Step 2:
Install gulp globally.
```npm install -g gulp```

How to check?
Open a new command session and run gulp -v
My version is ```[14:37:25] CLI version 3.9.0```

Did something break?
Try running ```npm install -g gulp``` with admin credentials (run cmd.exe as admin).
Gulp and other global packages will be installed into ```C:\Users\<username>\AppData\Roaming\npm\```

### Project-Specific Setup
If you have installed node, npm and gulp globally, you may proceed.
Let's get a fresh gulp-watch setup so we can add other features to it.

#### Step 1: Create a package.json in the root of the web project
This file should be alongside folders like "Scripts", "Content" and "Global.cs".
In the file below we give some metadata about our project (which we'll ignore) and a "devDependencies" section that tells the node package manager (npm) which packages we need while developing and building our application.
As you create this file, Visual Studio 2015 should give you intellisense for it and suggest version arguments.
```json
{
  "version": "1.0.0",
  "name": "MyApplication",
  "devDependencies": { 
    "gulp":  "^3.8.10",
    "gulp-watch": "*"
  }
}
```
This should provide us with gulp itself, which must also be installed locally and gulp-watch, which is a fast plugin for reacting to file changes (perform x on save).

On saving package.json, the node modules should be installed automatically.

Installing the packages manually:
You should not need to do this. Open up a command window where the package.json was created from step one.
Run ```npm install``` in this folder. npm should create a ```node_modules``` folder if it does not already exists. Inside you should find folders for "gulp" and "gulp-watch". 

#### Step 2: Create a gulpfile.js in the root of the web project
This file should be in the same folder as package.json. Again, Visual Studio 2015 should give you intellisense for your ```require()``` calls.
```javascript
var gulp = require('gulp'),
  watch = require('gulp-watch');

var applicationScripts = 'Scripts/**/*.js';
var applicationStyles = 'Scripts/**/*.css';

gulp.task('watch', function () {
  watch([applicationScripts], function (files, callBack) {
    //gulp.start('mytask');
  });
});
```

After creating this file, let's open the Task Runner Explorer.
You should find it in View > Other Windows > Task Runner Explorer.
Press the circular arrow button (the refresh button) to refresh any changes to your gulpfile if needed.
You should see one task ```watch``` under Gulpfile.js > Tasks > watch.
Right-click on this task and select Bindings > Project Open.

You can force the watch task to end by either closing Visual Studio or by clicking the "x" on the tab where it is running and shows output. For the latter, Visual Studio will confirm if you really want to terminate it.

Did something break?
Try pressing the refresh button on the Task Runner Explorer.
If Visual Studio 2015 still does not pick up changes to your gulpfile.js, restart Visual Studio.
Try running the watch task directly from the Task Runner Explorer. An output window should display any errors in trying to run your gulp task.
When you bind your task to "project open", you should see this line added to your gulpfile.js:
```/// <binding ProjectOpened='watch' />```

### Transpiling LESS on Save
Whenever we save a .LESS file, we want to automatically transpile it to .CSS.
To do this we'll use the ```gulp-less``` plugin found here: https://www.npmjs.com/package/gulp-less

Let's open the package.json we created and change it to this:
```json
{
  "devDependencies": {
    "gulp": "^3.9.0",
    "gulp-watch": "^4.2.4",
    "gulp-eslint": "^0.13.2",
    "gulp-less": "^3.0.3"
  }
}
```

...and change our gulpfile.js as well...
```javascript
/// <binding ProjectOpened='watch' />
var gulp = require('gulp'),
  watch = require('gulp-watch'),
  less = require('gulp-less');

var applicationScripts = 'Scripts/**/*.js';
var applicationStyles = 'Scripts/**/*.css';
var lessSourceFiles = 'Content/**/*.less';
var lessOutput = 'Content'; //Put the .css files in the same place the .less is found

gulp.task('watch', function () {
  watch([applicationScripts], function (files, callBack) {
    //gulp.start('mytask');
  });
  watch([lessSourceFiles], function (files, callBack) {
    gulp.start('less');
  });
});

gulp.task('less', function () {
  return gulp.src(lessSourceFiles)
  .pipe(less())
  .pipe(gulp.dest('Content'));
});
```

Make sure the watch task is running (if it's not, just double-click on it or select 'Run' from the Task Runner Explorer window). Now, whenever you save a .less file, it should automatically create a .css file in the same place as well as import from any ```@import``` statements.