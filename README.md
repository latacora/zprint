# zprint

__zprint__ is a library and command line tool providing a variety
of pretty printing capabilities for both Clojure code and Clojure/EDN
structures.  It can meet almost anyone's needs.  As such, it supports
a number of major source code formatting approaches.

[![cljdoc badge](https://cljdoc.org/badge/zprint/zprint)](https://cljdoc.org/d/zprint/zprint/CURRENT)

### Overview

**zprint** does far more than just properly indent code.  **Before**:

```clojure
(defn change-start-column [new-start-column style-vec [inline-comment-index
  start-column spaces-before :as comment-vec]] (if (zero? inline-comment-index)
  style-vec (let [delta-spaces (- new-start-column start-column) new-spaces
  (+ spaces-before delta-spaces) previous-element-index (dec
  inline-comment-index) [s c e :as previous-element] (nth style-vec
  previous-element-index) new-previous-element (cond (= e :indent) [(str "\n"
  (blanks new-spaces)) c e] (= e :whitespace) [(str (blanks new-spaces))
  c e 26] :else nil)] (assoc style-vec previous-element-index
  new-previous-element))))
```

**After**:

```clojure
(defn change-start-column
  [new-start-column style-vec
   [inline-comment-index start-column spaces-before :as comment-vec]]
  (if (zero? inline-comment-index)
    style-vec
    (let [delta-spaces (- new-start-column start-column)
          new-spaces (+ spaces-before delta-spaces)
          previous-element-index (dec inline-comment-index)
          [s c e :as previous-element] (nth style-vec previous-element-index)
          new-previous-element
            (cond (= e :indent) [(str "\n" (blanks new-spaces)) c e]
                  (= e :whitespace) [(str (blanks new-spaces)) c e 26]
                  :else nil)]
      (assoc style-vec previous-element-index new-previous-element))))
```

### *Recent Additions!* 


  * Comment wrapping has been considerably altered.  When working
  on the stability fixes for `1.2.5`, the largest remaining problem
  was comment wrapping causing changes to the formatting in subsequent
  runs.  In addition, the comment wrapping has been very simplistic
  since its inception, leaving wrapped comments looking pretty bad.
  There is a new capability called `{:comment {:smart-wrap? true}}`
  which will now word wrap comments cleanly.  It will also repair
  most of the problems that the simplistic wrapping produced in the
  past.  It is now the default, in no small part to repair the
  problems of the past.  If you are working to minimize changes
  when running zprint, I would recommend running it once over your
  code before you disable it, as will clean up most of the problems
  that were added by zprint in the past.  You can disable it with
  `{:comment {:smart-wrap? false}}`.  You can also configure it to
  minimize the amount of word wrapping it does, while still allowing
  it to do much better than the previous default by using `{:style
  :minimal-smart-wrap}`.  You need to have `{:comment {:smart-wrap?
  true}}` to use `:minimal-smart-wrap`.  See the reference manual for
  more details on how to configure smart wrap.
  * You can now specify some keys to come last in a map as well as
  some keys to appear first in a sorted map.  The `{:map {:key-order [...]}}`
  configuration places all of the keys prior to the distinguished key
  `:|` at the front of the map, and all of the keys after the `:|` key
  at the end of the map.  
  * Configurable Styles: As the number of styles that call `:option-fn`
  to return guides or just to do complex things has grown, many of
  these styles have also begun to accept a configuration map as
  their first argument.  This has worked well, but has in turn
  required a new style in the `:style-map` for every unique combination
  of values in the configuration map for the option-fn (or has
  required moderately complex configuration in the `:fn-map` or
  elsewhere). The processing for styles has been enhanced to
  allow configuration of styles when they are used, instead of
  pre-configuring them as additional styles.  See the CHANGELOG
  for some details, and the reference manual for many more.
  * If you were using zprint as a library, and you kept the configuration
  you wanted to use with zprint in a file of your own, there was no safe
  way to give that configuration to zprint if it contained function 
  definitions -- which it might need to for `:option-fn` values.  Now
  you can pass a string value of an options map to `set-options!`,
  and it will read and 'compile' that options map (including any functions)
  using the Small Clojure Interpreter (sci) built into zprint, the same
  way that zprint handles reading external configuration files.
  * Made considerable improvements in multi-format-pass "stability".  Thus, if
  you format the same file multiple times, it is considerably less likely 
  to change the second time.  The biggest issues were when using `:repect-nl`,
  though some affected every formatting approach.  The only downside
  is that the tuning for "hangs" had to change a bit -- so now more things
  qualify to hang as opposed to flow.  The change isn't dramatic, but if you
  prefer the previous behavior it is still available (without the new
  stability) by using: `:style :original-tuning`.
  * Inline comments (i.e., end of line comments) when aligned in a group 
  flow left to end up one space beyond the widest code.  Single inline comments
  did not, yielding odd inconsistencies.  Now single line inline comments
  also flow left to end up one space beyond the code.  You can turn all of
  the alignment support for inline comments off by using
  `:comment {:inline-align-style :none}` if you don't like this approach.
  * [All changes](./CHANGELOG.md)

## See zprint:

  * [__classic zprint__](./doc/types/classic.md) -- ignores whitespace 
  in function definitions and formats code with a variety of heuristics 
  to look as good as hand-formatted code 
  ([_see examples_](./doc/types/classic.md))
  * [__respect blank lines__](./doc/types/respectbl.md) -- similar to 
  classic zprint, but blank lines inside of function defintions are retained, 
  while code is otherwise formatted to look beautiful
  ([_see examples_](./doc/types/respectbl.md))
  * [__indent only__](./doc/types/indentonly.md) -- very different from 
  classic zprint -- no code ever changes lines, it is only correctly 
  indented on whatever line it was already on
  ([_see examples_](./doc/types/indentonly.md))

In addition, zprint is very handy [__to use at the REPL__](./doc/types/repl.md).

## Use zprint:

  * [to format whole files](./doc/using/files.md)
  * [while using an editor](./doc/using/editor.md)
  * [at the REPL](./doc/using/repl.md)
  * [with a team](./doc/using/team.md)
  * [with different formatting for different projects](./doc/using/project.md)
  * [to format a range of lines in a file](./doc/using/range.md)
  * [to format a babashka script](./doc/using/babashka.md)
  * [to correct indentation but not otherwise reformat a file](./doc/using/indent.md)
  * [and have it run even faster](./doc/using/fasthang.md)
  * [from inside a Clojure(script) program](./doc/using/library.md)

## Get zprint:

  * [a standalone binary for macOS](./doc/getting/macos.md)    _starts in <50 ms_
  * [a standalone binary for Linux](./doc/getting/linux.md)    _starts in <50 ms_
  * [a VS Code extension for zprint](./doc/getting/vscode.md)
  * [an uberjar for any Java enabled platform](./doc/getting/uberjar.md)    _starts in several seconds_
  * [an accelerated uberjar for any Java enabled platform](./doc/getting/appcds.md)    _starts in about 1s_
  * [a library to use at the REPL](./doc/using/repl.md)
  * [other ways to access zprint](./doc/getting/other.md)

## Get something other than the default formatting:

### Without learning how to configure zprint:

Maybe one of the existing "styles" will meet your needs.  All you have to
do is put `{:style ...}` on the command line or as the third argument
to a zprint call.  For example, `{:style :community}` or 
`{:style :respect-bl}`.

Some commonly used styles:

  * [Format using "community" standards](./doc/reference.md#community)
  * [Respect blank lines](./doc/reference.md#respect-bl)
  * [Indent Only](./doc/reference.md#indent-only)
  * [Respect all newlines](./doc/reference.md#respect-nl)
  * [Detect and format hiccup vectors](./doc/reference.md#hiccup)
  * [Justify all pairs](./doc/reference.md#justified)
  * [Backtranslate `quote`, `deref`, `var`, `unquote` in structures](./doc/reference.md#backtranslate)
  * [Detect keywords in vectors, if found respect newlines](./doc/reference.md#keyword-respect-nl)
  * [Sort dependencies in project.clj](./doc/reference.md#sort-dependencies)
  * [Support "How to ns"](./doc/reference.md#how-to-ns)

## Learn how to alter zprint's formatting behavior:

  * [How do I change zprint's behavior?](./doc/altering.md)

### I want to change...

  * [how user defined functions are formatted](./doc/options/fns.md)
  * [the indentation in lists](./doc/options/indent.md)
  * [the configuration to track the "community" standard](./doc/options/community.md)
  * [how blank lines in source are handled](./doc/options/blank.md)
  * [how map keys are formatted](./doc/options/maps.md)
  * [the colors used for formatting source](./doc/options/colors.md)
  * [how the second element of a pair is indented](./doc/options/pairs.md)
  * [how comments are handled](./doc/options/comments.md)
  * [how blank lines are handled at the top level](./doc/options/toplevel.md)
  * [how vectors are formatted based on their content](./doc/options/vectors.md)
  * [how constants are defined when formatting constant pairs](./doc/options/constantpairs.md)
  * [the options map by defining functions to format based on content](./doc/options/optionfns.md)
  * [anything else...](./doc/reference.md#introduction-to-configuration)

## Usage

[![cljdoc badge](https://cljdoc.org/badge/zprint/zprint)](https://cljdoc.org/d/zprint/zprint/CURRENT)

### Clojure 1.9, 1.10, 1.10.3, 1.11.1:

__Leiningen ([via Clojars](http://clojars.org/zprint))__

[![Clojars Project](https://img.shields.io/clojars/v/zprint.svg)](https://clojars.org/zprint)

### Clojurescript:

zprint has been tested in each of the following environments:

  * figwheel-main 0.2.16 (Clojurescript 1.11.4)
  * shadow-cljs 2.18.0
  * `planck` 2.26.0 (Clojurescript 1.10.914)

It requires `tools.reader` at least 1.0.5, which all of the environments
above contain.

### Clojure 1.8:

The last zprint release built with Clojure 1.8 was [zprint "0.4.15"].

In addition to the zprint dependency, you also need to
include the following library when using Clojure 1.8:

```clojure
[clojure-future-spec "1.9.0-alpha17"]
```

## The zprint Reference

  * [Entire reference document](./doc/reference.md)
  * [What does zprint do?](./doc/reference.md#what-does-zprint-do)
  * [Features](./doc/reference.md#features)
  * The zprint [API](./doc/reference.md#api)
  * Configuration
    * [ Configuration uses an options map](./doc/reference.md#configuration-uses-an-options-map)
    * [ Where to put an options map](./doc/reference.md#where-to-put-an-options-map)
    * [ __Simplified Configuration__ -- using `:style`](./doc/reference.md#style-and-style-map)
      * [  Respect blank lines](./doc/reference.md#respect-bl)
      * [  Indent Only](./doc/reference.md#indent-only)
      * [  Format using "community" standards](./doc/reference.md#community)
      * [  Respect all newlines](./doc/reference.md#respect-nl)
      * [  Detect and format hiccup vectors](./doc/reference.md#hiccup)
      * [  Justify all pairs](./doc/reference.md#justified)
      * [  Backtranslate `quote`, `deref`, `var`, `unquote` in structures](./doc/reference.md#backtranslate)
      * [  Detect keywords in vectors, if found respect newlines](./doc/reference.md#keyword-respect-nl)
      * [  Sort dependencies in project.clj](./doc/reference.md#sort-dependencies)
      * [  Support "How to ns"](./doc/reference.md#how-to-ns)
      * [  Add newlines between pairs in `let` binding vectors](./doc/reference.md#map-nl-pair-nl-binding-nl)
      * [  Add newlines between `cond`, `assoc` pairs](./doc/reference.md#map-nl-pair-nl-binding-nl)
      * [  Add newlines between extend clauses](./doc/reference.md#extend-nl)
      * [  Add newlines between map pairs](./doc/reference.md#map-nl-pair-nl-binding-nl)
      * [  Prefer hangs and improve performance for deeply nested code and data](./doc/reference.md#fast-hang)
    * [ Options map format](./doc/reference.md#options-map-format)
      * [  Option Validation](./doc/reference.md#option-validation)
      * [  What is Configurable](./doc/reference.md#what-is-configurable)
	* [   Generalized Capabilities](./doc/reference.md#generalized-capabilites)
	* [   Syntax Coloring](./doc/reference.md#syntax-coloring)
	* [   Function Classification for Pretty Printing](./doc/reference.md#function-classification-for-pretty-printing)
	  * [    Changing or Adding Function Classifications](./doc/reference.md#changing-or-adding-function-classifications)
	  * [    Replacing functions with reader-macros](./doc/reference.md#replacing-functions-with-reader-macros)
	  * [    Controlling single and multi-line output](./doc/reference.md#controlling-single-and-multi-line-output)
	  * [    A note about two-up printing](./doc/reference.md#a-note-about-two-up-printing)
	  * [    A note on justifying two-up printing](./doc/reference.md#a-note-on-justifying-two-up-printing)
    * [ Formatting large or deep collections](./doc/reference.md#formatting-large-or-deep-collections)
    * [ Widely Used Configuration Parameters](./doc/reference.md#widely-used-configuration-parameters)
    * [ __Configurable Elements__](./doc/reference.md#configurable-elements)
      * [:agent](./doc/reference.md#agent-atom-delay-fn-future-promise)
      * [:array](./doc/reference.md#array)
      * [:atom](./doc/reference.md#agent-atom-delay-fn-future-promise)
      * [:binding](./doc/reference.md#binding)
      * [:comment](./doc/reference.md#comment)
      * [:delay](./doc/reference.md#agent-atom-delay-fn-future-promise)
      * [:extend](./doc/reference.md#extend)
      * [:fn](./doc/reference.md#agent-atom-delay-fn-future-promise)
      * [:future](./doc/reference.md#agent-atom-delay-fn-future-promise)
      * [:list](./doc/reference.md#list)
      * [:map](./doc/reference.md#map)
      * [:object](./doc/reference.md#object)
      * [:pair](./doc/reference.md#pair)
      * [:pair-fn](./doc/reference.md#pair-fn)
      * [:promise](./doc/reference.md#agent-atom-delay-fn-future-promise)
      * [:reader-cond](./doc/reference.md#reader-cond)
      * [:record](./doc/reference.md#record)
      * [:set](./doc/reference.md#set)
      * [:spec](./doc/reference.md#spec)
      * [:style](./doc/reference.md#style-and-style-map)
      * [:style-map](./doc/reference.md#style-and-style-map)
      * [:tab](./doc/reference.md#tab)
      * [:vector](./doc/reference.md#vector)
      * [:vector-fn](./doc/reference.md#vector-fn)

### Testing and Development

Information on testing and development can be found 
[here](./doc/reference.md#testing-and-development).

Note: Changed the default branch to `main`.

### Contributors

A number of folks have contributed to zprint, not all of whom
show up on GitHub because I have integrated the code or suggestions manually.
__Thanks for all of the great contributions!__

  * Tests running in babashka: @borkdude
  * Additional colors and color-map entries: @RingMan
  * Updated `rewrite-cljs` dependency to `0.4.5` @rundis/
  * Readme updates: @mathiasn, @Quezion, @vemv, @arichiardi, @bhurlow, @kommen.
  * `--url` and `--url-only`: @coltnz
  * Use `UTF-8` locale to build the native image: @mynomoto
  * Suggestion/encouragement to implement `:respect-bl`: @griffis
  * Thread safety suggestions: @fazzone
  * `:option-fn` and `:fn-format` for enhanced vector formatting: @milankinen
  * Fixed missing require in `spec.cljc`: @Quezion
  * Corrected readme: @griffis
  * Fixed nested reader conditional: @rgould1
  * Clarified and added useful example for clj usage: @bherrmann7
  * Sublime text plugin instructions: @ekinnear
  * Use body indentation for the `ns` macro: @pesterhazy
  * Suggested fix for international chars and graalVM native image: @huahaiy

Thanks to everyone who has contributed fixes as well as everyone who has
reported an issue.  I really appreciate all of the help making zprint better
for everybody!

### Acknowledgements

At the core of `zprint` is the `rewrite-clj` library originally
created by Yannick Scherer, ported to Clojurescript by Magnus
Rundberget, and recently merged into a single, supported, documented,
and updated library by Lee Read.  This is a great library!  I would not have
attempted `zprint` if `rewrite-clj` didn't exist to build upon.

Additionally, allowing options maps containing functions to be read
from files safely is made possible by `sci`, the Small Clojure Interpreter
by Michael Borkent (@borkdude).  This is a very well designed and
implemented addition to Clojure that required almost no effort to integrate
into zprint.

## License

Copyright © 2016-2023 Kim Kinnear

Distributed under the MIT License.  See the file LICENSE for details.
