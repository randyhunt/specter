Spectre
=======

*CSS regression testing*. Adds [Resemble.js](http://huddle.github.com/Resemble.js/) image comparison functionality to [CasperJS](http://github.com/n1k0/casperjs) testing and navigation framework for [PhantomJS](http://github.com/ariya/phantomjs/), in order to produce automated visual regression testing.

### Why?

The problem with functional UI tests is that they make assertions on HTML markup, not the actual rendering. You can't know through automated tests if something has visually broke, too much margin, disabled state etc.  This situation is exacerbated by the increasing use of CSS3 for visual state changes that were traditionally built with JavaScript and DOM manipulation, ':target' pseudoclass or keyframes for example. Read more on Huddle's Engineering blog: [CSS Regression Testing](http://tldr.huddle.com/blog/css-testing/).

### How?

Specter takes screenshots captured by PhantomJS and compares them to baseline images using [Resemble.js](http://huddle.github.com/Resemble.js/) to test for rgb pixel differences with HTML5 canvas. Specter then generates image diffs to help you find the cause so you don't need to manually compare the new and old images.

Specter can only work when UI is predictable. If parts of your content come via AJAX, you may want to mock those responses. Take a look at [PhantomXHR](http://github.com/Huddle/PhantomXHR) for mocking XHR requests.

### Usage

Check out the [demo](http://github.com/Huddle/PhantomCSS/tree/master/demo) for a full working example (run `phantomjs demo/testsuite.js` from the command line).

Specter adds a global `specter` object to your casper tests. Just write your casper functional tests as normal, capturing screenshots as you go.

```javascript

/* global casper:false */

casper
.start('http://localhost/', function () {
    casper.viewport(1024, 600);
    specter.turn_off_animations();
    specter.screenshot("main > .content", "main");
    this.test.assertExists('form#login #id_username', 'username field');
    this.test.assertExists('form#login #id_password', 'password field');
})
.thenOpen('http://localhost/password-reset/', function () {
    specter.screenshot("main > .content", "password-reset");
})
.run(function(){
    this.test.done(0);
});

```

Specter will compare the after the functional tests have finished. Just invoke your tests by calling `specter` rather than `casperjs`.

Specter can run a single test file:

```bash
$ specter tests/test-login.js
```

Or, multiple files:

```bash
$ specter tests/test-login.js tests/test-homepage.js tests/test-checkout.js
```

Or, it can recursively run every test file in a specified directory:

```bash
$ specter tests
```


### Workflow

* Define what screenshots you need in your regular tests
* Find the screenshot directory and check that they look as you expect.  These images will be used as a baseline.  Subsequent test runs will report if the latest screenshot is different to the baseline
* Commit/push these baseline images with your normal tests (presuming you're using a version control system)
* Run the tests again.  New screenshots will be created to compare against the baseline image.  These new images can be ignored, they will be replaced every test run. They don't need to be committed
* If there are test failures, image diffs will be generated.


### Configuration

```
$ cat .specterrc

[paths]
testroot = static/styles/tests
baseline = static/styles/screenshots
diff = /tmp/specter/diff
fail = /tmp/specter/fail
```

--------------------------------------

Maintained by [Randy Hunt](http://github.com/letsgetrandy)
Specter is based on PhantomCSS, originally created by [James Cryer](http://github.com/jamescryer) and the Huddle development team.
