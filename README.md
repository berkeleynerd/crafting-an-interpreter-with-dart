YOU SHOULD NOT READ THIS BOOK.

It is for my own purposes. Go read [crafting interpreters]: http://craftinginterpreters.com instead!

This repo is a WORK-IN-PROGRESS experimental repurposing of Bob Nystrom's excellent "[Crafting Interpreters][https://github.com/munificent/craftinginterpreters]" book and related sources. It contains the Markdown text of the original book modified to ommit the `clox` compiler and related chapters, It also re-implements the jlox interpreter using the Dart language and updates the verbiage accordingly because I am a creature driven by my odd quirks and OCD. Bob's super nifty build system using to to weave the book together into a web-site, ebook, and pdf have been modified accordingly. The TL;DR is that I'm considering adapting Bob's awesome work creating a harmonious technical book publishing paradigm to my own nefarious ends and this is the place where that experiment is happening.

### Prerequisites

This code is being developed on an OS X machine, but any POSIX system should work too. With a little extra effort, you should be able to get this working on Windows as well. I recommend using WSL. 

Most of the work is orchestrated by make. The build scripts, test runner, and other utilities are all written in [Dart][]. Instructions to install Dart are [here][install]. Once you have Dart installed and on your path, run:

```sh
$ make get
```

[dart]: https://dart.dev/
[install]: https://dart.dev/get-dart

This downloads all of the packages used by the build and test scripts.

### Building

Once you've got that setup, try:

```sh
$ make
```

If everything is working, that will generate the site for the book as well as compiling the interpreter `lox`. You can run the interpreter right from the root of the repo:

```sh
$ ./lox
```

### Hacking on the book

The Markdown and snippets of source code are woven together into the final HTML
using a hand-written static site generator.

The generated HTML is committed in the repo under `site/`. It is built from a combination of Markdown for prose, which lives in `book/`, and snippets of code that are woven together from the Dart implementations in `dart/`. (All of those funny looking comments in the source code are how it knows which snippet goes where.)

The script that does all the magic is `tool/bin/build.dart`. You can run that directly, or run:

```sh
$ make book
```

That generates the entire site in one batch. If you are incrementally working
on it, you'll want to run the development server:

```sh
$ make serve
```

This runs a little HTTP server on localhost rooted at the `site/` directory. Any time you request a page, it regenerates any files whose sources have been changed, including Markdown files, interpreter source files, templates, and assets. Just let that keep running, edit files locally, and refresh your browser to see the changes.

### Building the interpreter

You can build the interpreter like so:

```sh
$ make lox
```

This builds the final version of the interpreter as it appears at the end of the book.

You can also see what the interpreter looks like at the end of each chapter. (This is to make sure it is working even in the middle of the book.) This is driven by a script, `tool/bin/split_chapters.dart` that uses the same comment markers for the code snippets to determine which chunks of code are present in each chapter. It takes only the snippets that have been seen by the end of each chapter and produces a new copy of the source in `gen/`, one directory for each chapter's code. (These are also an easier way to view the source code since they have all of the distracting marker comments stripped out.)

Then, each of those can be built separately. Run:

```sh
$ make dart_chapters
```

And in the `build/` directory, you'll get an executable for each chapter, like
`chap14_chunks`, etc. Likewise:

## Testing

There is a full Lox test suite that available to ensure the interpreter in the book
does what it is supposed to do. The test cases live in `test/`. The Dart
program `tool/bin/test.dart` is a test runner that runs each of those test
files on a Lox interpreter, parses the result, and validates that that the test
does what it's expected to do.

There are various interpreters you can run the tests against:

```sh
$ make test       # The final versions of clox and jlox.
$ make test_lox   # The final version of jlox.
$ make test_dart  # Every chapter's version of jlox.
$ make test_all   # All of the above.
```

### Testing your implementation

You can use the test suite and the test runner to test your own Lox implementation. The test runner is at `tool/bin/test.dart` and can be given a custom interpreter executable to run using `--interpreter`. For example, if you had an interpreter executable at `my_code/boblox`, you could test it like:

```sh
$ dart tool/bin/test.dart lox --interpreter my_code/mylox
```

You still need to tell it which suite of tests to run because that determines the test expectations. If your interpreter should behave like lox, use "lox" as the suite name. If your interpreter is only complete up to the end of one of the chapters in the book, you can use that chapter as the suite, like "chap10_functions". See the Makefile for the names of all of the chapters.

If your interpreter needs other command line arguments passed to use, pass them to the test runner using `--arguments` and it will forward to your interpreter.

## Repository Layout

*   `asset/` – Sass files and jinja2 templates used to generate the site.
*   `book/` - Markdown files for the text of each chapter.
*   `build/` - Intermediate files and other build output (except for the site
    itself) go here. Not committed to Git.
*   `gen/` – Dart source files generated by GenerateAst.dart go here. Not
    committed.
*   `dart/` – Source code of lox, the interpreter written in Dart.
*   `note/` – Various research, notes, TODOs, and other miscellanea.
*   `note/answers` – Sample answers for the challenges. No cheating!
*   `site/` – The final generated site. Most content here is generated by build.dart,
    but fonts, images, and JS only live here. Everything is committed, even the
    generated content.
*   `test/` – Test cases for the Lox implementations.
*   `tool/` – Dart package containing the build, test, and other scripts.
