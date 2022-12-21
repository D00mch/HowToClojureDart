# Introduction

On April 16th, the [ClojureDart](https://github.com/Tensegritics/ClojureDart) release made it possible for Clojure developers to create mobile, web, and desktop apps using Flutter.

While tools are not yet fully developed, with the absence of a REPL and autocompletion for dart-interop, it is still possible to use ClojureDart. Also, some of the Clojure magic is being shown already, for example, check [the `nest` macro](https://github.com/Tensegritics/ClojureDart/blob/main/doc/flutter-helpers.md#nest-macro) (update: `widget` macro can do the same) and compare [similar clj-code vs dart](https://twitter.com/cgrand/status/1517652416396730369?t=T2md7PwYw8kfbulzAlVhUQ&s=09). I've also written a post about the benefits of using Clojure: [Why Flutter needs Clojure](https://functional.works-hub.com/learn/why-flutter-needs-clojure-45b93). 

The goal of this article is to show how to write your first Flutter app with Clojure, what tools to use, and where to ask questions. The reader should keep in mind that minor clojure experience is required.

# The tools

For the ClojureDart journey you would need to [set up Flutter](https://docs.flutter.dev/get-started/install) on your machine, [Clojure cli](https://clojure.org/guides/deps_and_cli), and additionally I would recommend [Clj-kondo](https://github.com/clj-kondo/clj-kondo) (note that it's already built in lsp). The last would be helpful for [widget macro](https://github.com/Tensegritics/ClojureDart/blob/main/doc/flutter-helpers.md#widget-macro) warnings:

![clj-kondo-warnings](https://user-images.githubusercontent.com/14236531/166266665-9a51c70e-666b-4720-9b01-192ef0662629.gif)

# Choose your the editor

## VSCode — easy to start

The easiest option to start ([docs](https://calva.io/clojuredart)). Just install Calva, and you are ready to go:

https://user-images.githubusercontent.com/14236531/166266488-75e3d9ef-7e0a-421f-b87d-87d727195280.mp4

I would also recommend installing the Flutter extension ([docs](https://docs.flutter.dev/development/tools/vs-code)).

Pros:
- fast and easy to set up;
- Dart->Clojure converter support;
- unique [Joyride](https://marketplace.visualstudio.com/items?itemName=betterthantomorrow.joyride) plugin, that allows to run editor scripts with Clojure REPL.

## Intellij Idea — no support yet

The result you would get (typing `reduce-` to show clojure-autocompletion):

<img width="654" alt="idea example" src="https://user-images.githubusercontent.com/14236531/166266978-480a38a9-673e-440a-bf02-b9ce7ee4f209.png">

As you see, there are warnings on dart-interop, and it's impossible to get rid of them without turning on "Power Safe Mode". 

Pros:
- almost nothing to set up;
- support will be there, and everything will get to work.

Cons:
- Extremely slow startup (could be more than 1 minute until syncing, indexing, resolutions, etc. is completed on my air); 
- No way to turn off dart-interop warnings. 

Anyway, for setting the Idea up you would need 3 things:
[Cursive plugin](https://cursive-ide.com/). 
[Flutter plugin](https://docs.flutter.dev/development/tools/android-studio) (to be able to search through the dart code).
[Clojure-extras](https://plugins.jetbrains.com/plugin/18108-clojure-extras) plugin (for Clj-kondo linting support).

Also don't forget to set up `\*.cljd` files as Clojure files:
`preferences -> editor -> filetypes -> clojure file -> file patterns -> + -> \*.cljd`

## Vim

Same example with `reduce-`:

<img width="775" alt="vim example" src="https://user-images.githubusercontent.com/14236531/166267179-aac9e81f-8b2d-459d-81ca-d6170dc5a9e3.png">

Pros:
- speed, the ability to open tens (and hundreds) of projects at the same time in milliseconds;
- extended customization;
- REPL will be integrated out of the box with ClojureDart REPL release;
- the ability to write config in Lisp (Fennel).

Cons:
- need to learn how to use vim;
- need to spend some time on setting everything up;
- sometimes need to fix things after updates.

Below there are several options on how to set up Vim for ClojureDart.

### Nvim with lsp

Need to set up a [lsp-plugin](https://github.com/neovim/nvim-lspconfig) and 2 lsp servers: for [Clojure](https://github.com/neovim/nvim-lspconfig/blob/master/doc/server_configurations.md#clojure_lsp) and for [Flutter](https://github.com/akinsho/flutter-tools.nvim) (for the ability to search through the dart code). Need to add one line to set up `*.cljd`:
```vimscript
au! BufRead,BufNewFile *.cljd setfiletype clojure
```
I am using this config at the moment and besides dart-interop there are almost everything: refactorings, goto definition, find usages, autocompletion, docs, namespace cleaning.

[My config](https://github.com/d00mch/dotfiles/blob/master/.config/nvim/fnl/plugin/lspconfig.fnl) is written in Fennel. Going through this is out of the scope of this article. If you want to try using Lisp for configuration yourself, I would recommend starting with [aniseed](https://github.com/Olical/aniseed) plugin and the authors dotfiles. As the first config you could use [this project](https://github.com/rafaeldelboni/nvim-fennel-lsp-conjure-as-clojure-ide) or try [nyoom](https://github.com/shaunsingh/nyoom.nvim) setup.

### Vim with the VimIced plugin

You would need to set up everything with the plugin's [wiki](https://liquidz.github.io/vim-iced/) and use Clj-Kondo as the linter (use this [instruction](https://github.com/clj-kondo/clj-kondo/blob/master/doc/editor-integration.md#ale)). Until ClojureDart REPL is released, lots of features will not be supported.

Vim with Fireplace

I haven't used [Fireplace](https://github.com/tpope/vim-fireplace) recently (last 3 years). I guess the experience will be somewhat of VimIced. Be sure to write:

`au! BufRead,BufNewFile *.cljd setfiletype clojure`

## Emacs

ClojureDart authors are using Emacs, and [Clojure-mode](https://github.com/clojure-emacs/clojure-mode/blob/master/clojure-mode.el) has already added cljd (don't need to do anything yourself). I think the pros and cons will be similar to vim, with some details that are out of the scope. 

# The workflow

Good to start with the [hello-world](https://github.com/Tensegritics/ClojureDart/blob/main/doc/flutter-quick-start.md) and get comfortable with the tools. Here is a [video](https://www.youtube.com/watch?v=dfmRNTmfYVg&t=743s&ab_channel=ClojureDart) with the workflow.

I would open 3 windows: 
1. Dart file (could be a `\*.dart` file inside `lib/` directory) to be able to experiment with dart code, to check signatures, fields and methods.
2. Terminal to see the logs and to interact with the hot-reload; 
3. Emulator, in my experience Android is the good one. The easiest way to set up one is to install AndroidStuod and use Tools->DeviceManagement.

After you launch a watcher:
```shell
clj -M -m cljd.build flutter
```
Go to the loop: write code, save file, see feedback, repeat.

- Need a *hot-reload*, save file.
- Need a *hot-restart*, press Enter in the terminal. 
- Need to *cold-reborn*, remove `.clojuredart/` dir and restart the watcher.

## Using Libraries

As in the Flutter project, add libs in the `pubspec.yaml` file. Import it as a string like you would in the \*.dart files:
```clojure
(:require
    [clojure.string :refer [join]]
    ["package:graphql/client.dart" :as g])
```
And currently it's impossible to import a dart class without having it as a dependency in `pubspec.yml`.

## Building widget tree approach

In most of the cases you should write a function like [here](https://github.com/Tensegritics/ClojureDart/tree/main/samples) in the authors' examples; and not declare a class with deftype like I did [here](https://github.com/d00mch/minataurus/blob/custom-widget/src/minataurus/fab.cljd). And StatefulWidgets should be created with the [alpha/widget macro](https://github.com/Tensegritics/ClojureDart/blob/main/doc/flutter-helpers.md#widget-macro). Take a look at the [commit](https://github.com/d00mch/minataurus/commit/4102ae4b8c38e4fd0721a3a1ffac9d091c589be8) where I fixed it. Less code, and the code is more clear.

There is also a [tool](https://github.com/d00mch/DartClojure) to convert Dart code to Clojure code, which helps to rewrite widgets (not general Dart).

![Screen Recording 2022-05-29 at 19 38 44](https://user-images.githubusercontent.com/14236531/170881526-82983262-fd41-45e4-a90d-270859431890.gif)

## ClojureDart differs from Clojure

First, consider reading [the docs](https://github.com/Tensegritics/ClojureDart/blob/main/doc/differences.md) from authors upon the subject.

Next, you would definitely stumble upon the lack of libraries that return plain old  Clojure PersistentHashMap. Instead you will have to deal with dart Maps. Here in the [GraphQL example](https://github.com/d00mch/ClojureDartTeaExample/blob/main/src/tea/api.cljd) I wrote a function to convert dart Map into clojure PersistentHashMap.

# Resources

1. [Official docs](https://github.com/Tensegritics/ClojureDart/tree/main/doc) on ClojureDart 
2. [ClojureDart roadmap](https://github.com/Tensegritics/ClojureDart/projects/1)
3. [Samples](https://github.com/Tensegritics/ClojureDart/tree/main/samples) by the authors
4. [GraphQL + MVU sample](https://github.com/d00mch/ClojureDartTeaExample)
5. Custom widgets with deftype [sample](https://github.com/d00mch/minataurus/blob/custom-widget/src/minataurus/fab.cljd)
6. Community: Clojurians [channel](https://clojurians.slack.com), #clojuredart
7. YouTube [channel](https://www.youtube.com/channel/UCCkvOkh6pXzYqkFKDgoyWRg).
8. Dev's Twitter: [cgrand](https://twitter.com/cgrand), [BaptisteDupuch](https://twitter.com/BaptisteDupuch)
9. DartClojure [Workshop](https://clojure.stream/workshops/clojuredart)
