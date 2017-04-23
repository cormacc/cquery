# cquery

cquery is a low-latency language server for C++. It is extremely scalable and
has been designed for and tested on large code bases like
[Chromium](https://chromium.googlesource.com/chromium/src/). It's primary goal
is to make working on large code bases much faster by providing accurate and
fast semantic analysis.

![Demo](/images/demo.png?raw=true)

There are rough edges (especially when editing), but it is already possible to
be productive with cquery. Here's a list of implemented features:
  * code completion
  * references
  * type hierarchy
  * calls to functions, calls to base and derived functions
  * rename
  * goto definition, goto base method
  * document symbol search
  * global symbol search

# Setup

## Building

Eventually, cquery will be published in the vscode extension marketplace and you
will be able to install and run it without any additional steps. To use cquery
you need to clone this repository, build it, and then run the vscode extension
in the `vscode-client` folder.

```bash
# Build cquery
$ git clone https://github.com/jacobdufault/cquery --recursive
$ cd cquery
$ ./waf configure
$ ./waf build

# Build extension
$ cd vscode-client
$ npm install
$ code .
```

After VSCode is running, update the `ServerOptions` `cwd` parameter to point to
the absolute path of your build directory.

You can hit then `F5` to launch the extension locally. Consider taking a look at
the options cquery makes available in vscode settings.

If you run into issues, you can view debug output by running the
(`F1`) `View: Toggle Output` command and opening the `cquery` output section.

## Project setup

### compile_commands.json (Best)

To get the most accurate index possible, you can give cquery a compilation
database emitted from your build system of choice. For example, here's how to
generate one in ninja. When you sync your code you should regenerate this file.

```bash
$ ninja -t compdb cxx cc > compile_commands.json
```

The `compile_commands.json` file should be in the top-level workspace directory.

### cquery.extraClangArguments

If for whatever reason you cannot generate a `compile_commands.json` file, you
can add the flags to the `cquery.extraClangArguments` configuration option.

### clang_args

If for whatever reason you cannot generate a `compile_commands.json` file, you
can add the flags to a file called `clang_args` located in the top-level
workspace directory.

Each argument in that file is separated by a newline. Lines starting with `#`
are skipped. Here's an example:

```
# Language
-xc++
-std=c++11

# Includes
-I/work/cquery/third_party
```

# Limitations

cquery is able to respond to queries quickly because it caches a huge amount of
information. When a request comes in, cquery just looks it up in the cache
without running many computations. As a result, there's a large memory overhead.
For example, a full index of Chrome will take about 10gb of memory. If you
exclude v8, webkit, and third_party, it goes down to about 6.5gb.

# License

MIT