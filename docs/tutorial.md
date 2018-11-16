---
layout: config-options
---

# Tutorial

This document is intended to acquaint you with Figwheel's features and 
workflow.

This tutorial is based on the Clojure CLI tools. 

{::comment}
If you're using the
Windows Operating System you'll want to use the [Leiningen version of
this document](/quick_start_lein.html)
{:/comment}

## Install The Clojure CLI Tools

First we'll want to install the `clj` and `clojure` [command line
tools][CLI tools].

If you're on Mac OSX you can quickly install the Clojure tools
via [homebrew][brew].

In the terminal at the shell prompt execute the following command:

```shell
$ brew install clojure
```

If you've already installed Clojure, now is a great time to ensure
that you have the latest version installed with:

```shell
$ brew upgrade clojure
```

You can check that everything has been installed correctly by running
a Clojure REPL. In your terminal application at the shell prompt 
execute the following command:

```shell
$ clj
```

You should see a `user=>` prompt where you can evaluate Clojure code.

Try evaluating `(+ 1 2 3)` you should get a response of `6` along with
the next prompt asking you for more code. Type `Control-C` to get out
of the Clojure REPL.

If you were able to start a REPL, you have successfully installed Clojure!

> We'll use the acronym REPL frequently. It stands for [Read Eval
> Print Loop][REPL]

## Make A New Work Directory

Before we start working with ClojureScript, we'll make a new work directory.

```
workspace$ mkdir hello-cljs
```

## Specifying That You Want To Use Figwheel

Figwheel is a Clojure library, or rather it's a Jar of Clojure code. If you 
are familiar Ruby's `bundler` and `Gemfile`, Python's `pip` and 
`requirements.txt`, or Javascript's `npm/yarn` and `package.json` then the 
concept of specifying project dependencies should be familiar to you.

When using the Clojure CLI Tools project dependencies are specified in a
`deps.edn` file.

So in our new `hello-cljs` directory, we'll create a `deps.edn` file with
the following content:

```clojure
{:deps {com.bhauman/figwheel-main {:mvn/version "0.1.9"}}}
```

## Starting A ClojureScript REPL

Now let's use Figwheel to start a ClojureScript REPL. Make sure you
are still in the `hello-cljs` directory and execute the following command:

```clojure
clj -m figwheel.main
```

This command should fetch all the dependencies we need, boot up a
ClojureScript REPL and finally pop open a Browser window with a page
that looks like this:

![Repl host page in browser](https://s3.amazonaws.com/bhauman-blog-images/figwheel-main/fm-repl-host-page.png)

If you see the word **Connected** next to the CLJS logo within a green 
area, it means the page has successfully connected to the REPL that you just
launched. This webpage is the host JavaScript environment for the
REPL, and is where all the ClojureScript forms that you type into the REPL 
will be evaluated.

Speaking of the REPL, if you head back to the terminal window where
you launched `figwheel.main`, you should now see something like
this:

![figwheel repl prompt in terminal](https://s3.amazonaws.com/bhauman-blog-images/figwheel-main/figwheel-main-repl.png)

If you see this, you have successfully started a ClojureScript REPL
and you can now evaluate ClojureScript at the `cljs.user=>` prompt.

Let's evaluate some ClojureScript. Evaluate the following forms at the
prompt as demonstrated in the example REPL session below:

```clojure
cljs.user=> (println "Hello World!")
Hello World!
nil
cljs.user=> (range 5)
(0 1 2 3 4)
cljs.user=> (map inc (range 5))
(1 2 3 4 5)
cljs.user=> (filter odd? (map inc (range 5)))
(1 3 5)
cljs.user=> (js/alert "ClojureScript!")
nil
```

That last form should cause a JavaScript Alert to pop up in the
browser on our REPL host page.

## Amping Up The REPL

The REPL we just launched has simplistic terminal `readline` support,
meaning that it can handle editing a single line, and provide history
by pressing the up arrow. This will do fine for evaluating simple
forms, but we often want more fully featured `readline` support that can:

* syntax highlight Clojure code as you type it
* facilitate multi-line form editing 
* autocomplete the current function name that you're typing
* display function signatures as you type
* display the documentation for the function at the cursor 
* display the source code of the function at the cursor
* allows you to query for functions that are similar to the word at the cursor

My library [Rebel Readline][rebel] provides these features for Clojure
REPLs. Let's install it now as it'll be very helpful while exploring
how to work with Figwheel and ClojureScript.

To use Rebel Readline let's add `com.bhauman/rebel-readline-cljs
0.1.4` as another dependency in the `deps.edn` file:

```clojure
{:deps {com.bhauman/figwheel-main {:mvn/version "0.1.9"}
        com.bhauman/rebel-readline-cljs {:mvn/version "0.1.4"}}}
```

Now when you launch `figwheel.main`, it'll detect the presence of
`com.bhauman/rebel-readline-cljs` and use it when starting the
ClojureScript REPL.

To see it in action, again launch a REPL with `figwheel.main`, from the
`hello-cljs` directory:

```shell
$ clojure -m figwheel.main
```

> Its better to use the `clojure` command when using
> rebel-readline because the `clj` command provides it's own terminal
> line reader

After executing the above command, just like before, a Browser will open 
and a REPL will start. However, before the `cljs.user=>` prompt you 
will now see the following line:

```shell
[Rebel readline] Type :repl/help for online help info
```

This confirms that we're using Rebel Readline. 

If you type `:repl/help` command at the prompt, as you type you
immediately notice that `:repl/help` characters are syntax
highlighted. Upon pressing enter, you'll see a useful reference for
the REPL's capabilities displayed.

![rebel readline help](https://s3.amazonaws.com/bhauman-blog-images/figwheel-main/rebel-readline-help.png)

## Rebel Readline Feature Walkthrough

Let's quickly walk through how to use some of the **Clojure Key
Bindings** listed in the help above.

#### Autocomplete

Type `(ra` at the prompt, press the TAB, and don't press ENTER.

You'll see a list of choices that you can iterate through by pressing
the TAB key, and make your selection by pressing the ENTER key.
You can also keep typing characters to narrow the list of choices.

If you select `rand-int`, you'll now have `cljs.user=> (rand-int` on 
the command line. 

#### Inspecting Functions

Let's find out how to use `rand-int`. Press `Control-X Control-D` to
bring up the documentation for `rand-int`.  Upon doing so you'll see: 

![rebel redline displaying rand-int doc](https://s3.amazonaws.com/bhauman-blog-images/figwheel-main/demo-rebel-documentation.png)

One of the more helpful parts of this documentation is the line that
is displaying `([n])`. It's helpful but concise, and we should take a
moment to understand it.

`([n])` is a **list** of function signatures for the `rand-int`
function. It indicates that `rand-int` only has one function signature
`[n]`, which in turn indicates that `rand-int` takes a single
argument `n`. 

As for the type of `n`, we can infer from the function name and
documentation that `n` is most likely an integer. The Clojure/Script 
core libraries, and many others, use the following conventions when 
naming arguments:

* `f`, `g`, `h` - function
* `n` - integer, usually a size or count
* `index`, `i` - integer index
* `x`, `y` - numbers
* `xs` - sequence
* `m` - map
* `s` - string
* `re` - regular expression
* `coll` - a collection
* `pred` - a predicate closure
* `& more` - variable number of arguments

The argument name is `n` so we now know when we call `rand-int` we
should supply one integer argument.

If we want to understand `rand-int` further, we can directly examine
its source code.

Now, with your cursor anywhere within `rand-int` press
`Control-X Control-S`. You'll see the source code for `rand-int`
displayed.

![rand-int source in rebel](https://s3.amazonaws.com/bhauman-blog-images/figwheel-main/rand-int-source-in-terminal.png)

Now that we know that `rand-int` takes a single integer argument let's
call it. 

Let's see if `rand-int` works as we'd expect. Complete the REPL
line by typing `(rand-int 5)` and press enter and you should indeed get
a random number from 0 to 4.

If you press the up arrow you can get `(rand-int 5)` back at the REPL
prompt and you can press enter again to get a different random result.

Let's inspect a function with a more complex argument signature.

Enter `(range` at the prompt (use TAB completion if you like). Now
look at it's documentation with `Control-X Control-D` you'll notice
that the argument signature is described differently.

```clojure
([] [end] [start end] [start end step])
```

This indicates that `range` can be called with between 0 and 3
arguments. You can call `range` with no arguments `[]` which will
return an infinite sequence (not recommended at the REPL), you can
call it with one argument `[end]` specifying where a range starting at
`0` should end.  You can also call it with the other argument arities
`[start end]` and `[start end step]`.

Let's try this:

```clojure
cljs.user=> (range 4)
(0 1 2 3)
cljs.user=> (range 4 10)
(4 5 6 7 8 9)
cljs.user=> (range 4 10 2)
(4 6 8)
```

We intentionally didn't try to use `(range)` because it'll cause the
JavaScript environment to go into a tight loop and prevent further
REPL interaction.

It's a good exercise to experience this, so if you're up for it, try
entering `(range)` at the prompt. It should freeze the REPL because
the browser is now stuck in a tight loop trying to iterate through all
the integers up the maximum integer possible.

We can recover from this.

Sometimes it's easy to forget that the REPL is backed by a browser
tab, which is a very simple thing to reset. If your REPL gets stuck
in a tight loop, you can return to the REPL host page, take note of
the URL (most likely `http://localhost:9500/` in our case) and then
explicitly close the tab to kill the tight loop (a page reload often
doesn't work in this case) and then open a new tab at the same URL. 

At this point the REPL `(range)` eval should have timed out, and the 
REPL should be functioning normally.

> **TIP** If you're not in a tight loop and need to reset the state
> of the REPL, you can simply reload the REPL host page to reset to
> the original start state.

Okay, back from the brink? If not, you can kill the REPL with
`Control-C Control-D` and restart it.

#### Inline Evaluation

There's one more Rebel Readline feature that I'd like to go over
before moving on.

I'm assuming that you have a running REPL, and that you're back at the
`cljs.user=>` prompt.

At the prompt, enter the form `(+ 1 2 3 4)`, and after that when
your cursor is just after the last `)`, press `Control-X Control-E`.

You should see that the form was evaluated and the result `#_=>
10` was displayed just under the cursor line. Rebel Readline
allows you to evaluate any form, or sub-form, before pressing
ENTER, which will evaluate the form and potentially modify the application
state.

Let's try this again.  Press ENTER to get back to an empty prompt, and
type the form `(+ (+ 1 2 3) (+ 4 5 6))`.  Now place the cursor after 
the last paren of the sub-form `(+ 1 2 3)`. If you
press `Control-X Control-E` at this point you'll see `#_=> 6`. 
Experiment with evaluation of other parts of the form on this line.

What's the value of this feature? It allows you to work on larger
multi-line forms while verifying that the sub-forms are
doing what you expect.

> Having easy to parse forms is part of the magic of LISP. It
> allows tools to understand delimited forms without having to
> implement a complete parser for the language.

#### Multi-line Editing

By now, you may have noticed that you can create multi-line forms
at the REPL prompt in a fairly straight forward way.

Try entering the following form at the prompt and make sure you
format it so that it spans multiple lines (i.e., Pressing ENTER after
each line).

```clojure
cljs.user=> (+ 1
       #_=>    2
       #_=>    3)
```

You should notice that you were able to press ENTER to create newlines
within an **open form** without causing evaluation, but once you closed 
the form (by adding the last paren) pressing ENTER resulted in the 
evaluation of the form.

This concludes our tour of the Rebel Readline REPL features.

You can now exit the Rebel Readline REPL by pressing `Control-C Control-D`.

## Break Time

Once you have made it this far you have learned how to add
dependencies to `deps.edn` and how to start a `figwheel.main` REPL
with Clojure's CLI tools. You have also, learned how to include
[Rebel Readline][rebel] and how to use it to introspect your
environment.

This is more than enough to justify a break, may I suggest a nice walk
or perhaps a chat with a co-worker nearby?

## Working At The REPL

The ClojureScript REPL is a fantastic tool. It'll be very helpful
to understand a basic REPL driven workflow, as it's an important
skill that is complementary to the more automated hot-reloaded
workflow.

We're going to start up a generic `figwheel.main` REPL and then
start to compose, `require` and reload ClojureScript source files.

Again, we'll begin in our `hello-cljs` directory and start a REPL
with `figwheel.main` by executing the following command:

```shell
$ clojure -m figwheel.main
```

Once the REPL has started we'll turn our attention to creating a
file that contains some ClojureScript source code.

We'll have to create a file where it can be found, which means it'll
have to be named properly and reside on the classpath. 

By default, the Clojure tool adds the local `src` directory to the
classpath.

Create a file `src/hello/cruel_world.cljs` with the following
content:

```clojure
(ns hello.cruel-world)

(defn what-kind? []
  "Cruel")
  
(js/console.log (what-kind?))
```

The file layout of your project should now be:

```shell
hello-cljs
├── deps.edn
└── src
    └── hello
        └── cruel_world.cljs
```

Take note that in the ClojureScript file we're declaring a namespace
`hello.cruel-world` and that the path to our file mirrors this
namespace and it's rooted in the `src` directory, which is on the
classpath. This is what will allow the ClojureScript compiler to find
and compile our code.

> **TIP**: An extremely common mistake is to forget to replace hyphens
> `-` with underscores `_` when creating a namespace file path for a
> source file.  This peculiarity is required to support Java interoperability.

Now that we've created the file with the `hello.cruel-world`
namespace let's `require` it into our running REPL by executing the following
command:

```clojure
cljs.user=> (require 'hello.cruel-world)
nil
```

That `nil` response is exactly what we want. If there's a problem
finding or compiling a file the REPL will let you know.

Now we can call our `what-kind?` function defined in `hello.cruel-world` by
executing the following command:

```clojure
cljs.user=> (hello.cruel-world/what-kind?)
"Cruel"
```

As expected, when we call `(hello.cruel-world/what-kind?)` it returns
the string `"Cruel"`.

But it seems like a bit much to type out the entire namespace for each
call, so let's `require` `hello.cruel-world` again, creating a short alias
for it. Remember that you can press the up arrow to get back to the
original `require` statement and edit it.

```clojure
cljs.user=> (require '[hello.cruel-world :as world])
cljs.user=> (world/what-kind?)
"Cruel"
```

As you can see we created a short alias `world` for our namespace that we
can now use to invoke functions from `hello.cruel-world`.

Now let's change the file and reload it.

Alter the `what-kind?` function in the `src/hello/cruel_world.cljs`
file so that it returns `"Brilliantly Cruel"`. When you're done the
file should look like this:

```clojure
(ns hello.cruel-world)

(defn what-kind? []
  "Brilliantly Cruel")
  
(js/console.log (what-kind?))
```

Now we can head back to the REPL prompt and reload the
`hello.cruel-world` namespace like so:

```clojure
cljs.user=> (require '[hello.cruel-world :as world] :reload)
nil
```

And then verify that our changes were loaded:

```clojure
cljs.user=> (world/what-kind?)
"Brilliantly Cruel"
```

There's one more quick trick that you'll find helpful while working
at the REPL. You can change your current namespace (`cljs.user`) to
another loaded namespace.

For instance, we can change into the `hello.cruel-world` namespace
like so:

```clojure
cljs.user=> (in-ns 'hello.cruel-world)
nil
hello.cruel-world=>
```

You'll see the prompt change.  Now that your current namespace is
`hello.cruel-world` you can call functions in that namespace without
needing to provide a namespace.

For example:

```clojure
hello.cruel-world=> (what-kind?)
"Brilliantly Cruel"
```

Remember that you're working in a Browser environment so you can
interact with the browser DOM as well.

```clojure
hello.cruel-world=> (js/document.getElementById "app")
#object[HTMLDivElement [object HTMLDivElement]]
```

There's an HTML element with an `id` of `"app"` available on the
REPL host page. This element contains all of the markup and style for
the helper application. Let's override the helper app content, with
the string returned by our `what-kind?` function.

Make sure the REPL host page is visible while you type the following.

```clojure
hello.cruel-world=> (def app-element (js/document.getElementById "app"))
#'hello.cruel-world/app-element
hello.cruel-world=> (set! (.-innerHTML app-element) (what-kind?))
"Brilliantly Cruel"
```

After that, on the REPL host page, you should see the helper app
disappear, and be replaced by the words `Brilliantly Cruel`.

Well that was just a brief tour of a REPL driven workflow using a
trivial example, but it demonstrates that you can do quite a bit of 
application development with just a simple setup.

> **TIP**: There's a fantastic guide on
> [Programming at the REPL][program-at-repl] on the official Clojure
> website. Much of the guide is directly applicable to
> ClojureScript.  When you're ready to learn more it's an excellent
> resource.

> **TIP**: Much of what you learned above applies equally well to the
> Clojure language. If you would like to try your hand at Clojure,
> as well, you can get a working Rebel Readline Clojure REPL by typing
> `clojure -m rebel-readline.main` in the `hello-cljs` directory. If
> you want to create and load Clojure files, everything is the same as
> above except Clojure files end with `.clj`

## Starting The REPL Already Initialized With Your Code

While it's perfectly workable to `require` all the code you need at
the REPL, most of the time you'll want to initialize the REPL with
application specific code already loaded.

You can accomplish this by using the following command:

```clojure
$ clojure -m figwheel.main --compile hello.cruel-world --repl
```

This will compile and load the `hello.cruel-world` namespace into the
REPL environment. If `hello.cruel-world` required other namespaces
they would also be compiled and loaded.

You'll notice some differences in the REPL startup output this time:

![compile terminal output](https://s3.amazonaws.com/bhauman-blog-images/figwheel-main/fm-cruel-world-compile.png)

The output looks similar to launching `figwheel.main` without any
arguments, but there are some important differences.

The first thing of note is this warning:

![target classpath warning](https://s3.amazonaws.com/bhauman-blog-images/figwheel-main/fm-cruel-world-compile-warning.png)

This is expected. When we started the `figwheel.main` REPL without any
arguments, the compiled ClojureScript files are output into a
temporary directory. This directory is automatically added to the
classpath so that the compiled assets can be found and served by the
built-in webserver.

Running `figwheel.main` without arguments indicates that you're
likely experimenting with some code, not working on a local project.

When one starts compiling namespaces it indicates development of a 
project, and thus we'll want our compiled artifacts
to be local to the project (for later use). It's also time to start
being explicit about what is on our classpath. In order to provide a
smooth initial experience, `figwheel.main` will try to be helpful and
append the classpath with paths that should likely be there. When it
does add a classpath, Figwheel will print a warning because it's best
that you manage the classpath explicitly so that things work properly
when you're not using `figwheel.main`.

We can fix this classpath warning by adding both `"src"` and
`"target"` to the `:paths` key in our `deps.edn` file:

```clojure
{:deps {com.bhauman/figwheel-main {:mvn/version "0.1.9"}
        com.bhauman/rebel-readline-cljs {:mvn/version "0.1.4"}}
 :paths ["src" "target"]}
```

We've to include `"src"` as well as `"target"` because when you add
a `:paths` key to `deps.edn` the `src` path is no longer implicitly
added.

Now we'll return to run our REPL again from the `hello-cljs`
directory.

```clojure
$ clojure -m figwheel.main --compile hello.cruel-world --repl
```

You should see the familiar output when starting a `figwheel.main`
REPL minus the warning about `target`.

You should also see that your ClojureScript source code is now being
compiled to the local `target` directory and not to a temporary
directory.

You'll also notice this line:

![watching line in output](https://s3.amazonaws.com/bhauman-blog-images/figwheel-main/watching-line.png)

This line indicates that Figwheel is now watching the ClojureScript
files in the `src` directory for changes. When a change is detected
Figwheel will compile and reload the changed code into the JavaScript
environment, without us needing to use `(require 'hello.cruel-world
:reload)`.

Let's try this out.

First let's verify that `hello.cruel-world` is loaded.

```clojure
cljs.user=> (hello.cruel-world/what-kind?)
"Brilliantly Cruel"
```

Looks good! We successfully started the repl with our namespace
already available so we don't have to explicitly `require` it.

Now let's change the code. Go to the `src/hello/cruel_world.cljs`
source file and change the `what-kind?` function so it returns `"Cruel
No More"` and then save the file. It should look like this:

```clojure
(ns hello.cruel-world)

(defn what-kind? []
  "Cruel No More")
  
(js/console.log (what-kind?))
```
 
When you save the file, you should see a green `Successfully compiled`
message appear in the REPL. You can now check the REPL to see that
your code has indeed been reloaded automatically. (Don't forget that
you can use TAB to use autocomplete to help you enter the following)

```clojure
cljs.user=> (hello.cruel-world/what-kind?)
"Cruel No More"
```

This verifies that our code has been loaded. Now, we don't have to
explicitly reload a namespace as we work, we can simply save the file
we're working on and it'll be reloaded automatically.

Automatically reloading code on save makes a significant impact on
the efficiency of one's workflow.

## Feedback Is King

We'll take a look at the ways in which Figwheel provides feedback
while you're working.

When we've a workflow where our files are being watched and compiled
as we work on them, we've an opportunity to detect syntax/compile
errors earlier than if we waited to reload by hand.

Figwheel provides feedback for compile time errors and warnings in the
REPL and in the browser.

####  REPL Feedback

To experience this, arrange your REPL terminal and editor windows so that
they are side by side.

![image of terminal next to editor](https://s3.amazonaws.com/bhauman-blog-images/figwheel-main/repl-editor-side-by-side.png)

Now in the editor edit the the `src/hello/cruel_world.cljs` file again
by adding some bad code on a line at the end of the file that looks
like this `defn hello`. When you're done the file should look like
this:

```clojure
(ns hello.cruel-world)

(defn what-kind? []
  "Cruel No More")
  
(js/console.log (what-kind?))

defn hello
```

Now when you save the file you should see some warnings that look like
the following feedback in the REPL.

![screen shot of defn hello warnings](https://s3.amazonaws.com/bhauman-blog-images/figwheel-main/terminal-defn-hello-warnings.png)

Getting feedback like this as you're coding is more timely than
waiting until a file is compiled and loaded by hand, and allows you to
concentrate more on the problem with fewer interruptions from the
process.

Now let's cause a compile error by adding parenthesis around `defn
hello` so that it looks like this:

```clojure
(ns hello.cruel-world)

(defn what-kind? []
  "Cruel No More")

(js/console.log (what-kind?))

(defn hello)
```

Upon saving the file you'll see a compile error in the REPL.

![image of compile error](https://s3.amazonaws.com/bhauman-blog-images/figwheel-main/def-hello-compile-error.png)

So, we've demonstrated a workflow where you can edit your code and
quickly get feedback from the REPL that informs you of any compile
errors.

#### Heads-up Display Feedback

You may have noticed this already.  If you go back to the "Default
Figwheel Dev Page" in the Browser you'll also see the same error
above displayed in Figwheel's heads-up display.

![image of error in heads up display](https://s3.amazonaws.com/bhauman-blog-images/figwheel-main/defn-hello-error-heads-up.png)

Displaying compile time errors and warnings in the browser is an
important feature of Figwheel as we can only keep our attention in so
many places as we work. It's far too easy to miss compile time error
messages if they are tucked away in a terminal somewhere while you're
focused on the front-end you're working on.

Now, keeping the browser window visible, change the file and remove
the parenthesis from around the `(defn hello)` and press save. You'll now
see the compiler error replaced with a warning like this:

![image of warning](https://s3.amazonaws.com/bhauman-blog-images/figwheel-main/defn-hello-warning-heads-up.png)

And finally delete the remaining `defn hello` line and save the file
once again.

You'll notice that the warning goes away.

## The Build

The previous examples demonstrated how to set up a simple configuration
to productively work with ClojureScript. When we're working on 
a larger project we'll normally need to customize our environment beyond 
what is possible with the `deps.edn` file and the `figwheel.main` command 
line options.

There's also a need to **name** our *build* configurations so that we
can identify them and help Figwheel keep their compiled artifacts
separate.

To meet these needs `figwheel.main` relies on a **build file** to specify
ClojureScript compiler options, Figwheel options, and a stable name
for the configuration.

To help explain what a build file is and how it works, we'll start
with an example.

First let's reset things. Make sure you quit the REPL and then
delete the `target/public` directory to get rid of our compiled
assets.

Next, in the `hello-cljs` directory, create a new build file
called `cruel.cljs.edn` with the following content:

```clojure
{:main hello.cruel-world}
```

The content of the `cruel.cljs.edn` build file specifies the
[ClojureScript compiler options][compiler-options] for our cruel world
project. These options will be passed to the ClojureScript compiler
whenever source code needs compiling. There are quite a few
[compiler options][compiler-options] but the above is all we'll need
as `figwheel.main` provides enough default compiler options to allow
you to get started.

The `hello-cljs` directory should now look like this:

In the terminal at the shell prompt execute the following command:

```shell
hello-cljs/
├── cruel.cljs.edn
├── deps.edn
└── src
    └── hello
        └── cruel_world.cljs
```

It's easy to use a build file. From the `hello-cljs` directory, start
`figwheel.main`, using our new build file:

```shell
$ clojure -m figwheel.main --build cruel --repl
```

This will create a working environment that is practically identical
to when we previously called `clojure -m figwheel.main --compile
hello.cruel-world --repl`.

You'll notice that if you edit the `src/hello/cruel_world.cljs` file
you'll see the same hot reloading and feedback behavior as before.

In the startup output for the REPL, you'll notice a difference in the
names of the compiled artifacts.

![image of compiled artifacts output lines](https://s3.amazonaws.com/bhauman-blog-images/figwheel-main/build-name-output-difference.png)

Before the compiled output was getting compiled to
`target/public/cljs-out/main.js` and now it's getting compiled to
`target/public/cljs-out/cruel-main.js`.

As your project grows it'll more than likely have more than one
build, and having a name to identify and separate your build's
compiled artifacts is very helpful.

Also, as you progress with your project there's normally a need to
add more specific ClojureScript compiler options rather than rely the
default ones that `figwheel.main` supplies.  With a build file like
`cruel.cljs.edn` you'll have a logical place to add these options.

You'll also have a place to add [`figwheel.main` configuration
options][figwheel-options].

As an exercise, we'll add an option to modify `figwheel.main`'s
behavior. Let's say we'll be mainly relying on the heads up display
feedback and that we'd like to make Figwheel print the compiler errors
more concisely in the REPL.

We've seen that compile errors normally print out with some code
context and a pointer to where the error was detected.

![image of verbose error](https://s3.amazonaws.com/bhauman-blog-images/figwheel-main/verbose-defn-error-message.png)

We can make errors print out without the code context with the
[`:log-syntax-error-style`][syntax-error-style] option.

We'll configure this in our `cruel.cljs.edn` file. Go ahead and edit
the build file to look like this:

```clojure
^{:log-syntax-error-style :concise}
{:main hello.cruel-world}
```

The caret `^` character is very important and signifies that we're
adding **metadata** to the map that follows it. 

> Learn more about Clojure Metadata [here][learning-metadata] and see the
> [official reference][metadata]

Now when we start up the `cruel` build with `figwheel.main` again:

```shell
$ clojure -m figwheel.main --build cruel --repl
```

If we type an error in our code we'll see a much more concise message
like this:

![concise message image](https://s3.amazonaws.com/bhauman-blog-images/figwheel-main/concise-defn-error.png)

In summary, a build file is a useful way to specify compiler and
Figwheel options and assign that configuration a useful name. I
consider this the most useful way of working with `figwheel.main`.

#### Managing figwheel-main.edn

Sometimes you'll have several builds and you'll want them to share some
or all of your `figwheel.main` configuration. You can do
this by creating a `figwheel-main.edn` in your project directory.

For our current example, we can move our `:log-syntax-error-style`
configuration out of our `cruel.cljs.edn` file and place it in a
`figwheel-main.edn` file. Like this:

Example `hello-cljs/figwheel-main.edn`:
```clojure
{:log-syntax-error-style :concise}
```

If you try this you'll notice that the `:log-syntax-error-style
:concise` configuration still works. This configuration will now take
effect without any need to specify it in build files. 

It's important to note that the **metadata** configuration that you
add to your build files **will override** the configuration in
`figwheel-main.edn`

## Packaging Up A Single Compiled Artifact For Production

The ClojureScript compiler has four `:optimizations` options `:none`,
`:whitespace`, `:simple` and `:advanced`. The specified option determines 
the type of output that the compiler produces. For example, the default option
, which we've been using so far, is `:none`. This option produces many
individual files when we compile our ClojureScript.

If you look at the `target/public/cljs-out/cruel` directory you'll
see these files.

```shell
target
└── public
    └── cljs-out
        ├── cruel
        │   ├── cljs
        │   │   ├── core.cljs
        │   │   ├── core.js
        │   │   ├── core.js.map
        │   │   ├── pprint.cljs
        │   │   ├── pprint.cljs.cache.json
        │   │   ├── pprint.js
        │   │   ├── pprint.js.map
        │   │   ├── stacktrace.cljc
        │   │   ├── stacktrace.cljc.cache.json
        │   │   ├── stacktrace.js
        │   │   ├── stacktrace.js.map
        │   │   ├── test.cljs
        │   │   ├── test.cljs.cache.json
        │   │   ├── test.js
        │   │   └── test.js.map
        │   ├── cljs_deps.js
        │   ├── cljsc_opts.edn
        │   ├── clojure
        │   │   ├── data.cljs
        │   │   ├── data.cljs.cache.json
        │   │   ├── data.js
        │   │   ├── data.js.map
        │   │   ├── set.cljs
        │   │   ├── set.cljs.cache.json
        │   │   ├── set.js
        │   │   ├── set.js.map
        │   │   ├── string.cljs
        │   │   ├── string.cljs.cache.json
        │   │   ├── string.js
        │   │   ├── string.js.map
        │   │   ├── walk.cljs
        │   │   ├── walk.cljs.cache.json
        │   │   ├── walk.js
        │   │   └── walk.js.map
... and many more
```

When we want to deploy our final project we're normally going to want
to produce a single JavaScript file, to make load times more efficient.

All of the other `:optimizations` options produce a single file as output.

* `:whitespace` only optimizes whitespace
* `:simple` adds safe simple optimizations to `:whitespace` 
* `:advanced` adds state of the art JavaScript optimization to `:simple`

The `:advanced` optimization level is normally what you'll want when
you're ready to deploy code.

Let's first output a `:whitespace` optimized file.

First, delete the `target/public` directory.

```shell
$ rm -rf target/public
```

Because we just want to compile a file once and not start a watching
process we're going to use the `figwheel.main` `--build-once`
flag. We'll also specify the `--optimizations` level as `whitespace`.

Here's the long version of the command:

```shell
$ clojure -m figwheel.main --optimizations whitespace  --build-once cruel
```

and here's the command using abbreviated flags:

```shell
$ clojure -m figwheel.main -O whitespace -bo cruel
```

> **TIP**: You can use `clj -m figwheel.main --help` to learn all of
> the `figwheel.main` CLI flags and their abbreviations

If you execute the above command and then view
`target/public/cljs-out/cruel-main.js` you'll see that it's now a
large file that bundles all the required code.

You may think that this is a lot of code considering the size of our
source file. Unfortunately we're getting all the code that is needed
by the core ClojureScript library even though we're not using it.

We can remedy this by using `advanced` compilation which will perform
a static code analysis, DCE (Dead code elimination) and unused code 
removal.

```shell
$ clojure -m figwheel.main -O advanced -bo cruel
```

If you now view the content of `target/public/cljs-out/cruel-main.js`
you'll see that it's significantly smaller.

## Customizing index.html

In all of the examples above, we've used the host HTML page provided
by the `figwheel.main` helper application. You'll very quickly get
to the point where you want to supply an application-specific HTML 
page to host your application.

Let's look at how you can provide your own `index.html`.

The webserver used by Figwheel uses the classpath to find static
files. Anything that is on the classpath in a `public` directory will
be served as a static file.

Add a `resources` directory to the classpath by adding `"resources"`
to the `:paths` key in you `deps.edn` file. When you finish the file
should look like this:

```clojure
{:deps {com.bhauman/figwheel-main {:mvn/version "0.1.9"}
        com.bhauman/rebel-readline-cljs {:mvn/version "0.1.4"}}
 :paths ["src" "target" "resources"]}
```

Now let's make a `resources/public` directory that we'll use as a
web-root directory for our web assets.

```shell
$ mkdir -p resources/public
```

Now, we'll add a CSS file, because let's face it we're gonna need it.

Create a CSS file `resources/public/css/style.css` with the following
content:

```css
/* style */
body {
	color: red;
}
```

Then create an `resources/public/index.html` file with the following content:

```html
<!DOCTYPE html>
<html>
  <head>
    <!-- this refers to resources/public/css/style.css -->
    <link href="css/style.css" rel="stylesheet" type="text/css">
  </head>
  <body>
    <div id="app"></div>
	<!-- this refers to target/public/cljs-out/cruel-main.js -->
    <script src="cljs-out/cruel-main.js"></script>
  </body>
</html>
```

Once this file is in place, when you start the `cruel` build with
`figwheel.main` you'll now see your `index.html` file rather than
Figwheel's default helper app host page.

#### Live Reload CSS

You can get `figwheel.main` to watch and reload the css file above by
adding `:css-dirs ["resources/public/css"]` configuration to
your `cruel.cljs.edn` file as follows:

```clojure
^{:css-dirs ["resources/public/css"]}
{:main hello.cruel-world}
```

## Conclusion

You made it! Thanks for taking the time to learn more about
`figwheel.main`. I've hopefully provided you enough information to get
started with your ClojureScript explorations.

[REPL]: https://en.wikipedia.org/wiki/Read%E2%80%93eval%E2%80%93print_loop
[brew]: https://brew.sh/
[CLI Tools]: https://clojure.org/guides/getting_started#_installation_on_mac_via_code_brew_code 
[rebel]: https://github.com/bhauman/rebel-readline
[program-at-repl]: https://clojure.org/guides/repl/introduction
[compiler-options]: https://clojurescript.org/reference/compiler-options
[figwheel-options]: https://github.com/bhauman/figwheel-main/blob/master/doc/figwheel-main-options.md
[syntax-error-style]: https://github.com/bhauman/figwheel-main/blob/master/doc/figwheel-main-options.md#log-syntax-error-style
[learning-metadata]: https://en.wikibooks.org/wiki/Learning_Clojure/Meta_Data
[metadata]: https://clojure.org/reference/metadata
