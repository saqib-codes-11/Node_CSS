<!-- WDI6 3:02 -->

# SASSy Node
Using SASS in a node project, for fun and profit

## Why is this Important?
SASS makes CSS easier, and modularized - used correctly, you can start to mimic the MVC style of file organization you’re using on your backend (or f/e frameworks like Angular). Also, it’s a huge draw for employers. Everyone knows SASS is better than straight CSS, but very few devs have taken the time to learn and implement it in their projects. Knowing it will give you a head start in the job search!

## Objectives
1. Add Node/Gulp/Sass to an existing project
1. Write modularized SASS for DRY styles
1. Compile SASS using Node

## Set Up
<!-- The starter code in this folder (`app`) is the solution code from our [Angular Directives Lab](https://github.com/den-materials/angular-directives-lab). It’s just a frontend, but that’s all we need to get started! -->
We have starter code that gives us a nice little front end to experiment with. Let’s start by initializing our repo with NPM and installing Gulp.

### 1. Initialize Node

Hopefully this step isn’t too distant a memory - but in case you’ve forgotten, `cd` into the `app` folder and run `npm init -y`.

### 2. Run your Angular project

Start a Python Simple Server to run your project:
`python -m SimpleHTTPServer`

### 3. Install Gulp

1. First, cd into the app folder, `cd` into it, and `npm init -y` your project
You’ll want to install gulp globally. You can do this by running the `npm install gulp -g` command.
1. We also need to include gulp in our project. To do that, we will run `npm install gulp --save-dev`. We use `--save-dev` because a production environment should not be concerned with build tools.
1. Touch a `gulpfile.js`
1. Define our default task:
```
var gulp = require('gulp');

//define a task with the name of 'default'
// and a callback to perform when the task is ran
gulp.task('default', function() {
  console.log('I am the default task. Hear me roar');
});
```
1. In your terminal, run `gulp`! You should see our default task run.
<!-- WDI6 3:12 handed to devs -->

### 4. Compile Sass

Now let's compile some Sass to CSS.  We need to start by requiring the `gulp-sass` plugin for Gulp.

```bash
npm install --save-dev gulp-sass
```

Then we can require it in our `gulpfile.js`.

```js
//gulpfile.js
var gulp = require('gulp');
var sass = require('gulp-sass');
```

Define a `styles` task that looks in a `/sass` directory for all `.scss` files, logs any errors that occur, and then compiles the result into a `/css` directory.

```js
gulp.task('styles', function() {
    gulp.src('sass/**/*.scss')
        .pipe(sass().on('error', sass.logError))
        .pipe(gulp.dest('./css/'));
});
```
Create a `/sass` directory and `touch` a `main.scss` file inside.

Run `gulp styles` and it will find your `.scss` file and output a corresponding `.css` file for you.

Lastly let's add the task we just created to gulp's `default` task and have it run the task upon file changes by watching the file. Now every time you save, your `.scss` will trigger the `styles` gulp task and compile the changes for you!
<!-- WDI6 3:28 turning over to devs -->
<!--11:32 when turning over to devs -->

To do this, at the bottom of your file add:

```js
gulp.task('default',function() {
    gulp.watch('sass/**/*.scss',['styles']);
});
```

Now we can run our Sass compilation with just `gulp`.

<!-- WDI6 3:39 turning over to devs -->

### 5. What the hell, let's add Browser Sync as well

Gulp is so cool. In addition to Sass Compiling, we can give our apps other neat tricks, such as using `Browser Sync` to reload our page every time a page is saved.

Start by installing `Browser Sync`:
`npm install browser-sync gulp --save-dev`

Next, we can rewrite our `gulpfile.js` to have `Browser Sync` watch our file, and reload every time a page changes:

```js
var gulp = require('gulp');
var sass = require('gulp-sass');
var browserSync = require('browser-sync').create();

// Static Server + watching scss/html files
gulp.task('serve', ['sass'], function() {

    browserSync.init({
        server: "./"
    });

    gulp.watch("sass/**/*.scss", ['sass']);
    gulp.watch("*.html").on('change', browserSync.reload);
});

// Compile sass into CSS & auto-inject into browsers
gulp.task('sass', function() {
    return gulp.src("sass/**/*.scss")
        .pipe(sass())
        .pipe(gulp.dest("./css/"))
        .pipe(browserSync.stream());
});

gulp.task('default', ['serve']);
```
>The above functions were contructed using [Browser Sync's Documentation](https://browsersync.io/docs/gulp)

You can stop your Python Simple Server now - `Browser Sync` is running the show now! Restart `gulp` and watch it go!
<!-- WDI6 3:52 turning over to devs -->

## Refactoring with Sass
If we make a change in our `main.scss` file, then save it, we should see a new file pop into our CSS folder - `main.css`. Let's make that our new stylesheet, by changing the link in `index.html`:

```html
<link rel="stylesheet" href="css/main.css">
```

Go back to the browser, and you should see that our styles have all but dissapeared! That's because our pre-existing styles are still in `style.css`. Let's move them over to our SCSS file. Remember, all CSS is valid SCSS. But we can do better! Let's take this code, and see how DRY we can make it with SASS.

### Nesting
Let's start by [nesting](http://sass-lang.com/guide#topic-3) whatever style calls we can. Take a few minutes to nest the styles as deeply as you can (but, it's best practice to not go more than 4 levels deep).


Here's how I did it:
``` css

body {
  text-rendering: optimizeLegibility;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
    .container {
      margin: 4rem;
        .row {
            margin: 0;
        }
    } 
}

header {
  border-bottom: 1px solid black;
  padding-bottom: 1rem;
  margin-bottom: 1.618rem;
    .navbar {
      border-radius: 0;
    }
    h1 {
      font-weight: bold;
      letter-spacing: -2px;
      max-width: 100px;
        &:after {
          content: "™";
          font-size: 1rem;
          vertical-align: super;
          margin-left: 0.192rem;
        }
    }
}

.card {
  background: #231e1e;
  border-color: black;
  color: white;
  padding: 1rem;
  min-height: 16rem;
  position: relative;
  border: 1px solid rgba(0, 0, 0, 0.125);
  border-radius: 0.25rem;
    h6 {
      position: absolute;
      font-size: 0.6rem;
      margin-top: 1.618rem;
      opacity: 0.2;
      bottom: 0.618rem;
      left: 1rem;
        &:after {
          content: "™";
          font-size: 1rem;
          vertical-align: super;
          margin-left: 0.192rem;
          font-size: 60%;
          margin-left: 1px;
        }
    }
}

footer {
  text-align: center;
  margin-top: 2rem;
  color: silver;
  font-size: 0.6rem;
    .heart {
      color:#cf2e31;
    }
}

```

Notice in the above example that I broke the page into components - body(or structure), header, cards, and footer. This will help us scale our project if and when we add new functionality.

### Variables

[Variables](http://sass-lang.com/guide#topic-2) are a great way of reducing repeition in our code. Let's look for any vaules in our SCSS that are used more than once, and turn them into variables. Remember, the variable syntax for SASS is as follows:

`$my-variable: value;`

For starters, I see more than one place where padding has been set to 1rem. This is the perfect value to turn into a variable, because not only will it make it easier to change the value in the future, but it can serve to set up a design pattern that is easy to follow. So, let's set up a variable to define what the default padding/margin space should be:

`$space: 1rem;`

We've now set a system-wide standard for how to space any future objects on our page. No more guessing or eyeballing!
Let's take a moment to set up that variable, and set all relevant calls to use it:

`padding: $space;`

> DO NOT replace values of `left` and `font-size` with `$space`. While it will totally work, it confuses our semantics - font size isn't a form of 'space'. If you want to set up variables for these values, do so seperately, with uniquely-identifiable names.

My SCSS looks like this now:
```css
$space: 1rem;

body {
  text-rendering: optimizeLegibility;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
    .container {
      margin: 4rem;
        .row {
            margin: 0;
        }
    } 
}

header {
  border-bottom: 1px solid black;
  padding-bottom: $space;
  margin-bottom: 1.618rem;
    .navbar {
      border-radius: 0;
    }
    h1 {
      font-weight: bold;
      letter-spacing: -2px;
      max-width: 100px;
        &:after {
          content: "™";
          font-size: 1rem;
          vertical-align: super;
          margin-left: 0.192rem;
        }
    }
}

.card {
  background: #231e1e;
  border-color: black;
  color: white;
  padding: $space;
  min-height: 16rem;
  position: relative;
  border: 1px solid rgba(0, 0, 0, 0.125);
  border-radius: 0.25rem;
    h6 {
      position: absolute;
      font-size: 0.6rem;
      margin-top: 1.618rem;
      opacity: 0.2;
      bottom: 0.618rem;
      left: 1rem;
        &:after {
          content: "™";
          font-size: 1rem;
          vertical-align: super;
          margin-left: 0.192rem;
          font-size: 60%;
          margin-left: 1px;
        }
    }
}

footer {
  text-align: center;
  margin-top: 2rem;
  color: silver;
  font-size: 0.6rem;
    .heart {
      color:#cf2e31;
    }
}
```

### FUNctions

Variables are fine being used as they are, but we can also use them in functions, similar to JS. While there's a lot of [insane things](http://12devs.co.uk/articles/handy-advanced-sass/) you can do with this power, you'll usually be using it for very simple calculations. For instance, the margin on our `footer` is `2rem`, or double our `$space` variable. Why not make it dynamic with a little math?

`margin-top: $space*2;`

>Now, this is where you'll have to start making personal calls, based on your app. Do you always want margin to be double the value of padding? It's easy to paint yourself into a corner using variables and functions, so make sure you have a plan for the future of your app before you make everything dynamic.

### Mixins

[Mixins](http://sass-lang.com/guide#topic-6) allow us to seperate out some of the most tedius, repetative CSS calls, and assign values to them with simple parameters. For instance, in almost every block level element, you'll find yourself declaring values for the background color, text color, and border color, like we do on `.card`:

```css
    .card {
        background: #231e1e;
        border-color: black;
        color: white;
    }
```

What if, in the future, we wanted different types of cards, with different colors? Instead of declaring these for each value, we can write a 'colors' mixin that handles all three values for us:

```css
    @mixin colors ($background, $border, $color) {
      background: $background;
      border-color: $border;
      color: $color;
    }
```

Now when we start our `.card` declaration, all we need to do is include the mixin, along with our values:

```css
    .card {
        @include colors(#231e1e, black, white);
    }
```

### Importing partials

Taking a good look at our styles, we can start to see components growing out of our code. In fact, `.card` is something that could really take on a life of it's own if any more were added. Maybe it belongs in it's own file?

In Sass, we can use `@import` to seperate out our styles into semantic bits, the same way we do with our backend code in JS. Let's make a new scss file just for card styles:

`touch sass/_card.scss`

Notice that I added an underscore before the file name - this tells Sass it's a `partial `. 

<!-- Partials don't get compiled into their own document, but instead are meant only to be brought into other SCSS documents raw. As a result, Gulp will ignore changes in this file and only recompile when `main.scss` is saved.

Next, we can remove the entirety of our `.card` declaration, and move it to `card.scss`.
-->
This is a lot neater, but it's not connected yet. Add an import declaration into `main.scss`, right where the code used to be:

`@import '_card';`

Now whenever we save `main.scss`, we'll also be importing `_card.scss` into the mix.
<!--
>Protip: `_card.scss` doesn't get compiled when you save it - it only gets compiled when `main.scss` is saved by default (you don't see this in our build, though, because Browser Sync is watching EVERY file for changes). This is because it's a partial - if you hate this, remove the underscore to make it a full-blown scss document. But be warned - your site will now be making 2 HTTP requests, one for each scss document.
-->

### Extending/Inheritance

Sometimes, our classes will be so similar, that we'll want to use most (if not all) of their existing styles. With Sass, we can simply `extend` our classes by mentioning them in another class:

```CSS
    .message {
      border: 1px solid #ccc;
      padding: 10px;
      color: #333;
    }

    .success {
      @extend .message;
      border-color: green;
    }

    .error {
      @extend .message;
      border-color: red;
    }

    .warning {
      @extend .message;
      border-color: yellow;
    }
```

I don't see a reason to do this in our code though . . . not yet at least . . .

## Client Call!

Did you hear that telephone just now? It was our client over at Cards Against Assembly, and they have some urgent changes they need made to the site! Turns out, no one seems to know what this game is all about, so they need you to add a new card right below the header:

```html
    <section id="instructions" class="row">
      <div class='instruction_card col-sm-12 col-md-12 col-lg-12'>
        <h4>What is Cards Against Assembly?</h4>
        <p>
          Cards Against Assembly is a party game for General Assembly Students. Unlike most of the party games you've played before, Cards Against Assembly is as amazing and wonderful as you and your friends.
        </p>
        <p>
          The game is simple. Each round, one player asks a question from a black card, and everyone else answers with their funniest white card.
        </p>
      </div>
    </section>
```

Obviously, your layout wasn't built for this. But because you future-proofed your code with Sass, I bet it won't be that hard to add! Here's the client requests:

1. Make it look exactly like the other `.card`s, only with inverted colors -it shoudl have a white background and a `#231e1e` border and text.
2. Add the standard amount of margin to it.

How dry can you make the code for this new page addition? It's possible to do it with only 3 CSS rules. Look back at what we've learned in this lesson to create this new style rule with the fewest calls possible.

## Further Reading

- [More on the Import Directive](http://vanseodesign.com/css/sass-the-import-directive/)
- [Sass Basics](http://sass-lang.com/guide)
- [More on the Mixin Directive](https://www.sitepoint.com/sass-basics-the-mixin-directive/)
- [Sass and Compass Color Functions](http://jackiebalzer.com/color)





