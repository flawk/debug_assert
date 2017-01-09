# debug_assert [![badge](https://img.shields.io/badge/conan.io-debug_assert%2F1.0-green.svg?logo=data:image/png;base64%2CiVBORw0KGgoAAAANSUhEUgAAAA4AAAAOCAMAAAAolt3jAAAA1VBMVEUAAABhlctjlstkl8tlmMtlmMxlmcxmmcxnmsxpnMxpnM1qnc1sn85voM91oM11oc1xotB2oc56pNF6pNJ2ptJ8ptJ8ptN9ptN8p9N5qNJ9p9N9p9R8qtOBqdSAqtOAqtR%2BrNSCrNJ/rdWDrNWCsNWCsNaJs9eLs9iRvNuVvdyVv9yXwd2Zwt6axN6dxt%2Bfx%2BChyeGiyuGjyuCjyuGly%2BGlzOKmzOGozuKoz%2BKqz%2BOq0OOv1OWw1OWw1eWx1eWy1uay1%2Baz1%2Baz1%2Bez2Oe02Oe12ee22ujUGwH3AAAAAXRSTlMAQObYZgAAAAFiS0dEAIgFHUgAAAAJcEhZcwAACxMAAAsTAQCanBgAAAAHdElNRQfgBQkREyOxFIh/AAAAiklEQVQI12NgAAMbOwY4sLZ2NtQ1coVKWNvoc/Eq8XDr2wB5Ig62ekza9vaOqpK2TpoMzOxaFtwqZua2Bm4makIM7OzMAjoaCqYuxooSUqJALjs7o4yVpbowvzSUy87KqSwmxQfnsrPISyFzWeWAXCkpMaBVIC4bmCsOdgiUKwh3JojLgAQ4ZCE0AMm2D29tZwe6AAAAAElFTkSuQmCC)](http://www.conan.io/source/debug_assert/1.0/Manu343726/testing)

debug_assert is a simple, C++11, header-only library that provides a very flexible `DEBUG_ASSERT()` macro.
How many times did you write an assertion macro yourself, because `assert()` is controlled globally and cannot be enabled for certain parts of the program only?
This library solves the problem by providing a flexible, modular assertion macro.

## Features

* No dependencies. It only requires `std::abort()` and - unless `DEBUG_ASSERT_NO_STDIO` is defined - `std::fprintf()`.
* Single, small header file that just needs to be copied into your own project.
* Customizable assertion handling - assertion failure will call a user-defined function, with user-defined arguments.
* Modular - enable or disable assertions for different parts of the same program.
* Support for levels - give levels to your assertion macro and only enable certain levels of assertions.
* Little preprocessor use - just a single assertion macro which is needed to get the stringified expression and source location. Enabling/Disabling is controlled by compile time programming instead of preprocessor conditionals.
* Fast - even though a disabled assertion will still expand to something,
there is no overhead with even basic optimizations enabled and very little without optimization (just the code to read `__FILE__` and `__LINE__`). To be precise: It will only evaluate the assertion expression if the assertion is enabled!

## Overview

The basic usage of the library is like so:

```cpp
DEBUG_ASSERT(1 + 1 == 2, my_module{}); // basic
DEBUG_ASSERT(1 + 1 == 2, my_module{}, debug_assert::level<2>{}); // with level
```

Where `my_module` is a user-defined tag type that will both control the assertion level and the handler code.
It looks like this:

```cpp
struct my_module
: debug_assert::default_handler, // use the default handler
  debug_assert::set_level<-1> // level -1, i.e. all assertions, 0 would mean none, 1 would be level 1, 2 level 2 or lower,...
{};
```

A module handler must have `static` function `handle()` that takes a `debug_assert::source_location`, the stringified expression and any additional arguments you pass to `DEBUG_ASSERT()` (besides the `debug_assert::level`).

See `example.cpp` for more information and [read the blogpost](https://foonathan.github.io/blog/2016/09/16/assertions.html).

### CMake

For convenience you can also use CMake to setup the include directory, set the C++11 flag and have options that map to the customizable macros.
Simple call `add_subdirectory(path/to/debug_assert)` and then `target_link_libraries(my_target PUBLIC debug_assert)`.
It will not actually build something, only setup the flags.
The options are named like the macros.

## Documentation

> Generated by [standardese](https://github.com/foonathan/standardese).

# Header file `debug_assert.hpp`<a id="debug_assert.hpp"></a>

<pre><code class="language-cpp">#define <a href='doc_debug_assert.md#debug_assert.hpp'>DEBUG_ASSERT_MARK_UNREACHABLE</a>

#define <a href='doc_debug_assert.md#debug_assert.hpp'>DEBUG_ASSERT_ASSUME</a>(Expr)

#define <a href='doc_debug_assert.md#debug_assert.hpp'>DEBUG_ASSERT_FORCE_INLINE</a>

#define <a href='doc_debug_assert.md#DEBUG_ASSERT_CUR_SOURCE_LOCATION'>DEBUG_ASSERT_CUR_SOURCE_LOCATION</a>

#define <a href='doc_debug_assert.md#DEBUG_ASSERT'>DEBUG_ASSERT</a>(Expr, ...)

#define <a href='doc_debug_assert.md#DEBUG_UNREACHABLE'>DEBUG_UNREACHABLE</a>(...)

namespace <a href='doc_debug_assert.md#debug_assert.hpp'>debug_assert</a>
{
    struct <a href='doc_debug_assert.md#debug_assert::source_location'>source_location</a>;
    
    template &lt;unsigned Level&gt;
    struct <a href='doc_debug_assert.md#debug_assert::level-Level-'>level</a>;
    
    template &lt;unsigned Level&gt;
    struct <a href='doc_debug_assert.md#debug_assert::set_level-Level-'>set_level</a>;
    
    struct <a href='doc_debug_assert.md#debug_assert::allow_exception'>allow_exception</a>;
    
    struct <a href='doc_debug_assert.md#debug_assert::no_handler'>no_handler</a>;
    
    struct <a href='doc_debug_assert.md#debug_assert::default_handler'>default_handler</a>;
}</code></pre>

## Macro `DEBUG_ASSERT_CUR_SOURCE_LOCATION`<a id="DEBUG_ASSERT_CUR_SOURCE_LOCATION"></a>

<pre><code class="language-cpp">#define DEBUG_ASSERT_CUR_SOURCE_LOCATION</code></pre>

Expands to the current [debug\_assert::source\_location](doc_debug_assert.md#debug_assert::source_location).

## Macro `DEBUG_ASSERT`<a id="DEBUG_ASSERT"></a>

<pre><code class="language-cpp">#define DEBUG_ASSERT(Expr, ...)</code></pre>

Usage: \`DEBUG\_ASSERT(\<expr\>, \<handler\>, \[\<level\>\], \[\<handler-specific-args\>\]. Where:

  - `<expr>` - the expression to check for, the expression `!<expr>` must be well-formed and contextually convertible to `bool`.
  - `<handler>` - an object of the module specific handler
  - `<level>` (optional, defaults to `1`) - the level of the assertion, must be an object of type [debug\_assert::level\<Level\>](doc_debug_assert.md#debug_assert::level-Level-).
  - `<handler-specific-args>` (optional) - any additional arguments that are just forwarded to the handler function.

It will only check the assertion if `<level>` is less than or equal to `Handler::level`. A failed assertion will call: `Handler::handle(location, expression, args)`. `location` is the [debug\_assert::source\_location](doc_debug_assert.md#debug_assert::source_location) at the macro expansion, `expression` is the stringified expression and `args` are the `<handler-specific-args>` as-is. If the handler function returns, it will call \[std::abort()\].

*Notes*: Define `DEBUG_ASSERT_DISABLE` to completely disable this macro, it will expand to nothing. This should not be necessary, the regular version is optimized away completely.

## Macro `DEBUG_UNREACHABLE`<a id="DEBUG_UNREACHABLE"></a>

<pre><code class="language-cpp">#define DEBUG_UNREACHABLE(...)</code></pre>

Marks a branch as unreachable.

Usage: `DEBUG_UNREACHABLE(<handler>, [<level>], [<handler-specific-args>])` Where:

  - `<handler>` - an object of the module specific handler
  - `<level>` (optional, defaults to `1`) - the level of the assertion, must be an object of type [debug\_assert::level\<Level\>](doc_debug_assert.md#debug_assert::level-Level-).
  - `<handler-specific-args>` (optional) - any additional arguments that are just forwarded to the handler function.

It will only check the assertion if `<level>` is less than or equal to `Handler::level`. A failed assertion will call: `Handler::handle(location, "", args)`. and `args` are the `<handler-specific-args>` as-is. If the handler function returns, it will call \[std::abort()\].

*Notes*: Define `DEBUG_ASSERT_DISABLE` to completely disable this macro, it will expand to `DEBUG_ASSERT_MARK_UNREACHABLE`. This should not be necessary, the regular version is optimized away completely.

## Struct `debug_assert::source_location`<a id="debug_assert::source_location"></a>

<pre><code class="language-cpp">struct source_location
{
    const char* <a href='doc_debug_assert.md#debug_assert::source_location'>file_name</a>;
    
    unsigned <a href='doc_debug_assert.md#debug_assert::source_location::line_number'>line_number</a>;
};</code></pre>

Defines a location in the source code.

**Members:**

  - <a id="debug_assert::source_location::line_number"></a>`line_number` - \< The file name. \< The line number.

## Class template `debug_assert::level`<a id="debug_assert::level-Level-"></a>

<pre><code class="language-cpp">template &lt;unsigned Level&gt;
struct level
{
};</code></pre>

Tag type to indicate the level of an assertion.

## Class template `debug_assert::set_level`<a id="debug_assert::set_level-Level-"></a>

<pre><code class="language-cpp">template &lt;unsigned Level&gt;
struct set_level
{
    static const unsigned <a href='doc_debug_assert.md#debug_assert::set_level-Level-'>level</a> = Level;
};</code></pre>

Helper class that sets a certain level. Inherit from it in your module handler.

## Struct `debug_assert::allow_exception`<a id="debug_assert::allow_exception"></a>

<pre><code class="language-cpp">struct allow_exception
{
    static const bool <a href='doc_debug_assert.md#debug_assert::allow_exception'>throwing_exception_is_allowed</a> = true;
};</code></pre>

Helper class that controls whether the handler can throw or not. Inherit from it in your module handler. If the module does not inherit from this class, it is assumed that the handle does not throw.

## Struct `debug_assert::no_handler`<a id="debug_assert::no_handler"></a>

<pre><code class="language-cpp">struct no_handler
{
    template &lt;typename ... Args&gt;
    static void <a href='doc_debug_assert.md#debug_assert::no_handler::handle(constdebug_assert::source_location&,constchar*,Args&&...)'>handle</a>(const <a href='doc_debug_assert.md#debug_assert::source_location'>source_location</a>&amp;, const char*, Args&amp;&amp;...) noexcept;
};</code></pre>

Does not do anything to handle a failed assertion (except calling [std::abort()](http://en.cppreference.com/mwiki/index.php?title=Special%3ASearch&search=std::abort())). Inherit from it in your module handler.

### Function template `debug_assert::no_handler::handle`<a id="debug_assert::no_handler::handle(constdebug_assert::source_location&,constchar*,Args&&...)"></a>

<pre><code class="language-cpp">template &lt;typename ... Args&gt;
static void handle(const <a href='doc_debug_assert.md#debug_assert::source_location'>source_location</a>&amp;, const char*, Args&amp;&amp;...) noexcept;</code></pre>

*Effects*: Does nothing.

*Notes*: Can take any additional arguments.

-----

## Struct `debug_assert::default_handler`<a id="debug_assert::default_handler"></a>

<pre><code class="language-cpp">struct default_handler
{
    static void <a href='doc_debug_assert.md#debug_assert::default_handler::handle(constdebug_assert::source_location&,constchar*,constchar*)'>handle</a>(const <a href='doc_debug_assert.md#debug_assert::source_location'>source_location</a>&amp; loc, const char* expression, const char* message = nullptr) noexcept;
};</code></pre>

The default handler that writes a message to `stderr`. Inherit from it in your module handler.

### Function `debug_assert::default_handler::handle`<a id="debug_assert::default_handler::handle(constdebug_assert::source_location&,constchar*,constchar*)"></a>

<pre><code class="language-cpp">static void handle(const <a href='doc_debug_assert.md#debug_assert::source_location'>source_location</a>&amp; loc, const char* expression, const char* message = nullptr) noexcept;</code></pre>

*Effects*: Prints a message to `stderr`.

*Notes*: It can optionally accept an additional message string.

*Notes*: If `DEBUG_ASSERT_NO_STDIO` is defined, it will do nothing.

-----

-----


## Acknowledgements

Thanks a lot to [@Manu343726](https://github.com/Manu343726) and [@verri](https://github.com/verri).

