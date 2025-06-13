# Misc utilities

Documentation may or may not be forthcoming for these.  Some do have a
usage message though.  Most are short enough that you can easily read the
source code to see what they do.

Also relevant: [git-util](https://github.com/mjdominus/git-util) [pbmutil](https://github.com/mjdominus/pbmutil)

## Validated as useful

`menupick` is a filter reads a list of items from stdin, prints a menu
of the items on the terminal, repeatedly prompts the terminal for a
selection of items, and prints the selected items on stdout when the
prompting is over.  For example: `emacs $(ls | menupick)`.

> Responses to the prompt are a series of numbers, which identify
> items that are added to the current selection, and numbers with
> prefixed `!` marks, which are removed from the current selection.
> Any number of such items can be entered at once, sepearated by
> whitespace.  Ranges such as '12-23' are allowed.  '*' is an
> abbreviation for all the items on the current page.
>
> An empty line, or a line that ends with `!`, terminates the
> prompting.  If an input line contains a single `?`, the list of
> currently-selected items will be printed before the next prompt;
> if it contains `??` the menu will be redisplayed, with selected
> items marked.
>
> If the number of items is large, lines containing `j` or `n` move
> to the next page of the menu, and `k` or `p` move to the previous
> page.

`pomo` is a pomodoro timer. `pomo 10` will do a 10 minutes countdown.

`path` prints out your `$PATH` variable, one entry per line, so that
you can iterate over the components with `for` or pipe them into
`grep` or whatever. A future version will perform the opposite
transformation.

`count` generates a report of the unique lines in the input, sorted by
frequency of occurrence.

`sample` selects and prints a random sample of lines from the input.
`sample 0.23` and `sample 23%` select each line with probability of
23%.  (They do not guarantee to emit exactly 23% of all lines.)
`sample 23` selects and emits exactly 23 lines, using the standard
reservoir sampling algorithm.

`lr` performs rudimentary statistical analysis of its input,
including, primarily, linear regression.  The input should be two
columns, of X and Y values, or one column, of Y values (and the X
values are implied to be 1, 2, 3, etc.).  The command then prints out
the means, variances, and standard deviations of the inputs, the
correlation between the two columns, and the slope and intercept of
the best-fit line through the data.  The `-lr` flag suppresses
everything but the linear regression results; the `-mean` flag
suppresses everything but the means, and so forth.

`yorn` reads lines from standard input, prompts _on the terminal_ for
a 'y' or 'n' response, and prints the selected lines on standard
utput.

## Not validated yet

`addup` is a filter that adds up a series of numbers and prints the
total.  `addup` _n_ splits each line into fields and totals the n'th
field in each line.

`also` is a wrapper around `guessfile` and `menupick`, described
below.  It guesses which files you might have meant, presents a menu
to let you select which ones you actually meant, and then opens emacs
buffers for those files.

`date-arith` takes an argument list like `2014-12-23 +12`, interprets
`+12` as “plus 12 days”, and prints the resulting date in the same
format.

`do-over` is a triviality: it runs a certain command every time you
hit enter. I don't know why I like it so much.

`dt` is for managing the names of KDE desktops.  The System Settings
widget for doing this is clumsy.  Basic use is `dt [ -n number ] new-name`.
If `-n` is omitted, it sets the name of the current desktop.

`dus` is like the standard `du` program, but lists the sizes of files
as well as the totals for each directory.  `dus -n` sorts the output
by size.

`every 15 command args...` runs the command every 15 seconds. Normally
each run starts 15 seconds after the last one ended; `every -s` starts
each command 15 seconds after the last one _started_.  `-v` is
verbose; `-x` tells it to exit if any of the runs fail.

`f` is the most useful of the bunch.  `f 6` is the same as `awk
'{print $6}'`, except with 80% less typing.  `f -1` works.
The special syntax `f 3..` or `f -3..` tells `f` to print all the
fields form the specified one nutil the end.

`files` lists the files in the current Git repository that match a
pattern.  Pattern semntics are fine-tuned to my preferences.  For
example, `files foo` finds all the files whose base name _contains_
`foo`, but `files foo*.py` finds all the files whose base name
_begins_ with `foo` and ends with `.py`. With no arguments it lists
all files, and it always omits gitignored files.

`getstore _url_ _file_` fetches the file at the given URL and stores
it in the specified file.  If you omit the filename, it will be
inferred from the URL.

`guessfile` takes  a list of patterns
and prints out name of  files matching one of the patterns.
Patterns are shell glob style,
with some modifications: if the pattern contains `::`, this is
translated to a slash (for searching for Perl modules) and patterns
are _unanchored_, except that if one contains a suffix like `.txt` it
is anchored to the end.  Only the base filename is matched against the
pattern, unless the pattern contains a slash, in which case the full
path is matched.  Environment variables control the directories to be
searched (default `.` unless overridden by the `-d` flag) and to be
ignored (default `.git`).  `-i` makes the match case-insensitive.  The
`withsome` command, inspired by [Ronald Schmidt's command of the
same name](http://software-path.com/withsome.html), is a wrapper
around `guessfile` and `menupick`: `withsome command pat` guesses
files matching `pat`, filters the guesses with `menupick`, and then
runs the `command` on the selected files.

`ifchanged` watches a file using the Linux "inotify" facility and runs
a command whenever the file changes.

With arguments, `localtime` converts the arguments from epoch time to
local time and prints the conversions one per line.  Without
arguments, it is a filter, replacing an epoch time at the beginning of
any input line to the corresponding local time.

`indir` runs a command in some other directory.  It saves you from
having to chrid there manually and then chdir back, and may save a
conceptual step.

`lastdl` searches the `$HOME/Downloads` directory for the most recent
downloaded file, and simply prints its name. This is so that you can
easily do `mv $(lastdl) .` or something of that sort. The shell
mis-handles filenames that contain whitespace, so `lastdl` will rename
the file to a whitespace-less one before printing the name.

`numfilter` copies standard input to standard output, but one or more
of the fields are examines for numerals, which are then reformatted to
human-friendly format. Use `-f` to select which field.  Use `-F`
to select which formatter is used.  The default converts `1234` to
`1.2k` and similarly.  Use `-b` to use powers of 2 instead of powers
of 10.

`psgrep` runs `ps` and greps the output, but leaves intact the header
line that explains what the columns mean.  It also omits itself and
its own `ps` from the output.

`randword` selects a random line from its input, by default a
dictionary file.  With `-n` it selects more than one line.  The outputs
are selected equiprobably, and the input is not stored in memory.
It may soon be replaced with `sample` (see below).

`reso` prints a list of the available monitor resolutions, _grouped by
aspect ratio_, so that you can select a resolution that has the same
aspect ratio as the current one.

`rmdups` removes duplicate files from one or more directories.
Currently, it only removes with names of the form `foo (1).bar`, and
only if there is an identical file named `foo.bar` in one of the
directories.

`runN` runs a command repeatedly with different arguments, by default
on process at a time, but the `-n N` option tells it to run up to _N_
jobs in parallel.  [This version, by Aaron
Crane](http://aaroncrane.co.uk/2008/07/runN/), has a better interface
than [my original
implementation](http://blog.plover.com/prog/runN.html).

`shutup command args...` runs the comand with its standard output and
standard error directed to `/dev/null`.  If the command wants to
communicate with the user, it must do so by another method.  `shutup`
does _not_ detach the terminal, so the subcommand is free to redirect
its output to `/dev/tty` if it likes

`sort-natural` sorts its input lines like `sort`, but arranges to sort
the numeric parts of the lines numerically. For example,

        a a1 a10 a100 a10x a2 a21 a3 ab b

sorts into the order

        a a1 a2 a3 a10 a10x a21 a100 ab b

with the numeric parts of the `a` sequence in numerical order.

`sortbylength` is a filter that sorts its input lines from shortest to
longest.

`spot` reads an input and emphasizes the differences from line to line
by erasing the parts of each line that match the previous line.  Try
`ls -l | spot` to see what it does.

`stopafter 12 command args...` runs the specified command with the
specified args, but sets an alarm clock to go off after 12 seconds,
which will interrupt the process (unless the command reschedules the
alarm or handles or ignores the alarm signal.)

`stouch` adjusts the last-modified dates on a set of files so that
they appear to have been written in the order given on the command
line.  This is useful for dealing with stupid photo gallery programs
that insist on displaying files in chronological order.

`suf` removes the suffix of a filename and replaces it with a
different suffix.  For example, `mv $i $(suf $i .jpg)` moves `img.gif`
to `img.jpg`.  People usually do this with `basename` but `suf` is
shorter and easier.

`trim` trims the trailing whitespace from each line in the files named
in the arguments, and rewrites the files in place.  With no arguments,
it works as a filter.

`with-memory-limit` runs a command with its memory limited by the
`rlimit` facility.

`xar` is a stripped-down version of `xargs`. `xargs mv % %.bak` reads
lines from standard input, trims off a trailing newline, if any, and
runs the command `mv foo foo.bak` for each line `foo`.  If the
command template contains no `%` signs, an extra `%` argument is
implied at the end.

`z command args...` tries to run the command on the specified
arguments, but if any of those arguments appear to be compressed
files, it creates a pipe, runs `gzip -cd` to decompress the file into
the pipe, and attaches the command to the pipe instead.  So for
example

        perl -lne 'print if /octopus/' *

doesn't work if some of the files in the current directory are
compressed, but

        z perl -lne 'print if /octopus/' *

does.
