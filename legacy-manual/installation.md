# Installation

The canonical way to install CIDER is via `package.el` (Emacs's built-in package
manager), but it can be installed manually or via alternative package managers such
as `el-get`.

## Prerequisites

You'll need to have Emacs installed, preferably the latest stable
release. If you're new to Emacs you might want to go through
[the guided tour of Emacs](https://www.gnu.org/software/emacs/tour/index.html)
and the built-in tutorial (just press <kbd>C-h t</kbd>).

CIDER officially supports Emacs 25.1+, Java 8+ and Clojure(Script)
1.8+.  CIDER 0.17 (Andalucía) was the final release which supported
Java 7 and Clojure(Script) 1.7.

You'll also need a recent version of either the Clojure CLI tools or your
favorite build tool (Leiningen, Boot, or Gradle) to be able to start CIDER via
`cider-jack-in`. Generally it's a good idea to use the latest stable versions.

!!! Warning

    CIDER does not support ClojureCLR.

## Installation via package.el

CIDER is available on the two major `package.el` community
maintained repos -
[MELPA Stable](http://stable.melpa.org)
and [MELPA](http://melpa.org).

You can install CIDER with the following command:

<kbd>M-x package-install [RET] cider [RET]</kbd>

or by adding this bit of Emacs Lisp code to your Emacs initialization file
(`.emacs` or `init.el`):

```el
(unless (package-installed-p 'cider)
  (package-install 'cider))
```

If the installation doesn't work try refreshing the package list:

<kbd>M-x package-refresh-contents [RET]</kbd>

It's important to note that MELPA packages are built automatically
from the `master` branch, and that means you'll be right on the
leading edge of development. This has upsides and downsides; you'll
see new features first, but you might experience some bugs from
time to time. Nevertheless, installing from MELPA is a reasonable way
to obtain CIDER. The `master` branch is normally quite stable
and serious regressions there are usually fixed quickly.

If you have concerns about living on the leading edge of CIDER
deveopment, you can always pin CIDER to use MELPA Stable like this:

```el
(add-to-list 'package-pinned-packages '(cider . "melpa-stable") t)
```

!!! Tip

    If you don't want to (or can't) wait for MELPA to rebuild CIDER,
    you can easily build and install an up-to-date MELPA package locally yourself. Check out
    [this article](http://emacsredux.com/blog/2015/05/10/building-melpa-packages-locally/)
    for details on the subject.

!!! Note

    CIDER has dependencies (e.g. `queue` & `seq`) that are only available in the
    [GNU ELPA repository](https://elpa.gnu.org/). It's the only package repository
    enabled by default in Emacs and you should not disable it!

## Installation via use-package

`use-package` can be used to install CIDER via the `package.el`'s repositories
[MELPA Stable](http://stable.melpa.org) and [MELPA](http://melpa.org).

If you wanted to install the version of CIDER which is what is to be found in
the `master` branch, declare the following in your Emacs initialization file
(`.emacs` or `init.el`):

```el
(use-package cider
  :ensure t)
```

However, if you wanted to be a bit more conservative and only use the stable
releases of CIDER, you'd declare the following:

```el
(use-package cider
  :ensure t
  :pin melpa-stable)
```

After placing one of the above s-expressions, evaluate it, for it to take effect
by entering: <kbd>C-x C-e</kbd>.

For further configuration options with `use-package`, consult the
official [use-package repository](https://github.com/jwiegley/use-package).


## Installation via el-get

CIDER is also available for installation from
the [el-get](https://github.com/dimitri/el-get) package manager.

Provided you've already installed `el-get` you can install CIDER with the
following command:

<kbd>M-x el-get-install [RET] cider [RET]</kbd>

## Manual Installation

Installing CIDER manually is discouraged unless you plan to work with CIDER's
codebase. The manual installation is relatively involved as it requires manual
installation of the dependencies. Check out the section
[Hacking on CIDER](hacking_on_cider.md) for more details.

## CIDER's nREPL Middleware

Much of CIDER's functionality depends on its own [nREPL
middleware](https://github.com/clojure-emacs/cider-nrepl). Starting
with version 0.11, `cider-jack-in` (<kbd>C-c C-x (C-)j (C-)j</kbd>)
automatically injects this middle and other dependencies as required.

!!! Note

    In the past, if you were setting up CIDER, you might have had to
    modify `profiles.clj` or `profile.boot`. CIDER now handles
    everything automatically and you don't need to add anything
    special to these files.

!!! Tip

    If you don't want `cider-jack-in` to inject dependencies automatically, set
    `cider-inject-dependencies-at-jack-in` to `nil`. Note that you'll have to setup
    the dependencies yourself (see the section below), just as in CIDER 0.10 and older.

CIDER can also inject a Clojure dependency into your project, which is useful,
for example, if your project defaults to an older version of Clojure than that
supported by the CIDER middleware. Set `cider-jack-in-auto-inject-clojure`
appropriately to enable this.

If you prefer a standalone REPL, you will need to invoke
`cider-connect` instead of `cider-jack-in` and manually add the
dependencies to your Clojure project (explained in the following
section).

### Setting Up a Standalone REPL

#### Using Leiningen

!!! Note

    Make sure you're using Leiningen 2.9.0 or newer, as 2.9.0 is the first
    release to ship with nREPL 0.6.

Use the convenient plugin for defaults, either in your project's
`project.clj` file or in the :repl profile in `~/.lein/profiles.clj`.

```clojure
:plugins [[cider/cider-nrepl "x.y.z"]]
```

A minimal `profiles.clj` for CIDER would be:

```clojure
{:repl {:plugins [[cider/cider-nrepl "0.21.1"]]}}
```

!!! Warning

    Be careful not to place this in the `:user` profile, as this way CIDER's
    middleware will always get loaded, causing `lein` to start slower.  You really
    need it just for `lein repl` and this is what the `:repl` profile is for.

#### Using Boot

!!! Note

    Make sure you're using Boot 2.8.3 or newer, as 2.8.3 is the first
    release to ship with nREPL 0.6.

Boot users can configure the tool to include the middleware automatically in
all of their projects using a `~/.boot/profile.boot` file like so:

```clojure
(require 'boot.repl)

(swap! boot.repl/*default-dependencies*
       concat '[[cider/cider-nrepl "0.21.1"]])

(swap! boot.repl/*default-middleware*
       conj 'cider.nrepl/cider-middleware)
```

For more information visit [boot-clj wiki](https://github.com/boot-clj/boot/wiki/Cider-REPL).

### Using Embedded nREPL Server

If you're embedding nREPL in your application, you'll have to start the
server with CIDER's own nREPL handler.

```clojure
(ns my-app
  (:require [nrepl.server :as nrepl-server]
            [cider.nrepl :refer (cider-nrepl-handler)]))

(defn -main
  []
  (nrepl-server/start-server :port 7888 :handler cider-nrepl-handler))
```

It goes without saying that your project should depend on `cider-nrepl`.

!!! Note

    Prior to CIDER 0.18, CIDER and cider-nrepl were always released together
    and their versions had to match for things to work. But as the prominence
    of cider-nrepl grew and many other tools started using it, the two
    projects evolved separately and are no longer in tight
    lock-step. Any recent version of cider-nrepl should be compatible
    with a recent version of CIDER. You can check the required version
    of cider-nrepl for your version of CIDER by looking at
    `cider-required-middleware-version`.