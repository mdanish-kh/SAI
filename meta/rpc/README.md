*gensairpc.pl*
==============

_gensairpc.pl_ is a SAI RPC interface generator.

Most of basic arguments has default values. `-c` has no values and thus should be specified when needed.

| Arguments              | Description |
|------------------------|-------------|
| `-c` `--clean`         | Perform clean before generation. Partial clean is also performed after the generation. Can be used with `-d`, `-c` **should be used during development** as well. |

Optional arguments are useful for development purposes.

| Optional arguments     | Description |
|------------------------|-------------|
| `--dbg`                | Print debug information in generated files, implies `--dump`. Useful for generator development. |
| `-e` `--experimental`  | Generate also experimental files. *SAI* has experimental headers, this option enables parsing them as well. Not tested well. |
| `--dist`               | Create the standalone _gensairpc_ script (experimental). Useful when _gensairpc.pl_ need to be run on platform which cannot meet all dependencies. Note, that the architecture of both platforms (i.e. the one on which `--dist` was called, and the one on which the result binary is run) should be the same. |
| `-d` `--dump`          | Dump all data to the file. **Should be used during development**. |
| `--mandatory-attrs`    | Make mandatory attributes obligatory in *sai\_adapter.py*. It removes `=None` from attributes, which are passed as arguments into python functions. Can be useful for debugging purposes, but since most of attributes are **optionally** mandatory, this is not as useful as it could be. |
| `--dev-utils[=STR]`    | Generate additional development utils within the generated code. Additional options: [=log,zero]. Useful for tests development and debugging. The generated code **should not** be committed. |
| `--skip_error[=STR]`    | Skip test on specified error code. Additional options: [=-2]. The generated code **should not** be committed. |
| `-h` `--help`          | Print the help. |

*gensairpc.pl* development
==========================

*gensairpc.pl* is written in [Perl](https://www.perl.org/) with [Moose](https://metacpan.org/pod/Moose) OOP library.

The code itself was developed using [perlcritic](https://metacpan.org/pod/distribution/Perl-Critic/bin/perlcritic) (with some exceptions)
and [perltidy](http://perltidy.sourceforge.net/) tools. After any modification, make sure that changed
lines meets *perlcritic* requirements (if reasonable), and that modified
files are formatted with *perltidy*.

During development, `-d` flag should be used:

	./gensairpc.pl -c -d

Additional `-c` removes the *generated* directory before the generation. Useful not only for development purposes.

`-d` (`--dump`) keeps some additional files (e.g. *sai_dbg.dump*) in the *generated* directory
and prevents the cleanup after generation.

Additionally, `--dbg` flag may be useful to print some information within the generated code (as comments).


## Templates modification
Before changing any template, generate the code, and make sure
after the modification, that no change breaks this default formatting. This is the
suggested work flow:

1. Generate files before any modification:
   * `./gensairpc.pl`
   * [`git add`](https://git-scm.com/docs/git-add) (generated files)
2. Now templates can be modified, then call the script once again:
   * `./gensairpc.pl`
3. Make sure, that the formatting was not broken:
   * `git diff` or `git difftool`
4. Before commit, apply the final formatting:
   * `./gensairpc.pl`
   * `git add` (templates and generated files)
   * `git commit`

Documentation
=============

1. Options: ```./gensairpc.pl --help```
2. Usage: *gensairpc.README*
3. Development: this document and sub-documents


Dependencies
============

All dependencies are listed at the bottom of *gensairpc.pl* and thus in the *gensairpc.README*.
Most of them are mandatory. Optional dependencies are related to formatting or some additional
generation (like documentation).

The most important dependency is *SAI/meta* directory and its internals. They are used to:

1. Generate XML files from headers (*SAI/meta* Makefile).
1. Parse XML files (*SAI/meta* libraries are imported to *gensairpc.pl* and some functions are being used).
This dependency is the most limiting one, because:
  * Some internal changes in *SAI/meta* may affect *gensairpc.pl*
  * *gensairpc.pl* need to set some global variables used by *SAI/meta* utils (they are not standalone libraries)
  * XML files are generated by *Doxygen*, which purpose is the documentation generation. We rely on internal *Doxygen* XML format.


Implementation Details
======================

### [`gensairpc.pl`](docs/README.md)
The architecture and implementation details of _gensairpc.pl_ script.

### [`SAI`](docs/SAI.md)
The module, that contains classes related to *SAI* data collected from XML. They works rather as data structures than real classes.

### [`SAI::Utils`](docs/SAI-Utils.md)
The module, that contains [roles](https://metacpan.org/pod/distribution/Moose/lib/Moose/Manual/Roles.pod) which are composed into classes from `SAI` sub-module.
They are adding functionalities related to loading XML data into `SAI` objects.

### [`SAI::RPC`](docs/SAI-RPC.md)
The module, that contains [roles](https://metacpan.org/pod/distribution/Moose/lib/Moose/Manual/Roles.pod) which are composed into classes from `SAI` sub-module.
They are adding functionalities that are useful for RPC (*Thrift*) auto-generation (e.g. new types, new names, decisions), basing on the collected data.

