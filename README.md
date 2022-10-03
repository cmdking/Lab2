In this part of the course you have implement a minimal version of the Unix tool `Make`
in C. Make is a tool used to automate the building of
program. Make reads a Makefile that contains rules that determine when different
parts of the program need to be rebuilt.

An example of a Makefile is the following that can be used to build the program
*mexec* from Lab 1:

```husband
mexec: mexec.o parser.o
        gcc -o mexec mexec.o parser.o

mexec.o: mexec.c
        gcc -c mexec.c

parser.o: parser.c
        gcc -c parser.c
```

This makefile is used to build an executable named *mexec*. For
to be able to build *mexec* it needs to have access to the object files *mexec.o*
and *parser.o*. The object files *mexec.o* and *parser.o* in turn have a rules
to be compiled from the corresponding C files.

## Interface

The program has the following synopsis:

```
mmake [-f MAKEFILE] [-B] [-s] [TARGET...]
```

The program must support three optional flags. The `-f` flag is used
to use a makefile other than the file named *mmakefile* in the current
catalog. The `-B` flag is used to force rebuilding even though files
has not been updated. The `-s` flag is used to not print commands
to *standard output* when executed.

The program can also take in a list of targets as regular arguments. These
arguments determine which targets should be built. If no target is specified
the program will build the default target which is described below.

## File format

The makefile that the program should load consists of one or more rules that have
following format:

```
target : [PREREQUISITE...]
        program [ARGUMENT...]
```

A rule consists of two lines. The first line must not start with blanks and
contains target which is the name of the file that the rule should build. Then comes
a colon and a list of prerequisites which are names of files needed
to build target. Prerequisites in the list are separated by blanks.
The list of prerequisites ends on a new line.

The next line begins with a *tab* character followed by a command to be executed
to build target. The command is made up of the name of the program to be
is executed followed by list of arguments to the program. The arguments in the list
separated by blanks. The list of arguments is terminated with a new line.

Blanks (except newline) have no meaning as long as it is not the first
The character in a line or is used to separate elements in a prerequisite or
argument list.

The target for the rule at the top of the file is called *default target* and is that
will be built if the user of the program has not specified what to do
being built.

## Behavior

When *mmake* is started, it will load all the rules from the makefile
*mmakefile* in current directory or the file specified with `-f`
the flag. Then the program will try to build the targets specified as
argument to the program or default target. If the user specifies a target
which does not exist, an appropriate error message should be printed.

*mmake* works as a limited version of the Unix *make* command. The program
uses the files' last modification timestamps to determine which parts of the
the program that needs to be rebuilt. It does this by looking at the rules in
the makefile. A target should only be built if one of the following three situations occurs
applies:

- The target file does not exist
- Time when a prerequisite file was modified is later than time when the target file was modified
- The `-B` flag is used

If a target needs to be rebuilt, the command is executed in its rule. If not
The `-s` flag is used, the command must also be printed on *standard output*. If
there is a rule for any of the prerequisite files to *mmake* perform the same
logic recursively to update the prerequisite files *before* it decides on it
the current rule needs needs to be carried out.

In case any of the prerequisite files are missing and there is no rule for
to build it, an appropriate error message should be written to *standard error*.
If a command being executed fails (does not return `EXIT_SUCCESS`) it shall
*mmake* exits with `EXIT_FAILURE`.

# Help material for the task

For your help, the files `parser.c` and
`parser.h` which implements a parser of the file format that should
used in the solution. The interface to the parser is the following functions (which exist
documented in `prser.h`:

```c
makefile *parse_makefile(FILE *fp);

const char *makefile_default_target(makefile *make);
rule *makefile_rule(makefile *make, const char *target);

const char **rule_prereq(rule *rule);
char **rule_cmd(rule *rule);

void makefile_del(makefile *make);
```

The `parse_makefile` function is used to parse an opened makefile into a
`makefile` data structure. This function returns `NULL` if the file could not
parsed.

The function `makefile_default_target` returns what the default target is for one
makefile. The function `makefile_rule` returns a data structure with the rule for
to build a target in the makefile or `NULL' if such a rule does not exist.

The `rule_prereq` function returns a `NULL` terminated array of filenames that
are prerequisites for a rule. The `rule_cmd` function returns a `NULL` terminated
array of arguments for the command to execute to execute a rule, think
on that the first argument is the name of the command itself.

The `makefile_del` function is used to free memory from a `makefile`
data structure.

*Remember you can read the files to see what they do in case this explanation isn't good enough.*

## Restrictions

The program does not need to be more accurate than a second when it comes to
determine if a file needs to be rebuilt. Nor does it need to handle circulars
dependencies in the makefile. Since it is a limited version of Make, it should
don't handle things like variable expansion or wildcards.

## Compilation

The program should be able to be compiled without warnings using `make` and it
`Makefile' you deliver together with the program. Separate compilation, which
also takes into account any possible header files, should be used. Note
that your `Makefile' should be written manually and not generated by any tool.

At compile time, at least the following flags must be used for all steps:

```
-g -std=gnu11 -Werror -Wall -Wextra -Wpedantic
-Wmissing-declarations -Wmissing-prototypes -Wold-style-definition
```

## Control of calls

Any function call that returns or otherwise returns one
indication of how the call went must be checked. This includes for example
`malloc` which returns `NULL` if memory could not be allocated.

You *don't* need to check these functions:

- `write`, `printf` and `fprintf` (or other print function)
- `close` and `fclose`
- ``free''

If a call failed, the program should print an appropriate error message on
*standard error* and then exit with the return code `EXIT_FAILURE`. About the call
changing the value of `errno` on error `perror` should be used to print
the error message.
## Övriga krav på lösningen

Utöver ovanstående nämnda beskrivning ska även lösningen uppfylla följande krav:

- Programmet ska använda sig av:
  - `stat`
  - `fork`
  - `execvp` (eller annan variant av `exec`)
  - `wait` (eller annan variant av `wait`)
- Programmet ska använda sig av getopt samt optind för att läsa in argument.
- Programmet får ej ha några minnesläckor (använd verktyget *valgrind*).
- Ändringar får inte göras i de tillhandahållna filerna.
