## Description

pflag is a drop-in replacement for Go's flag package, implementing
POSIX/GNU-style --flags.

pflag is compatible with the GNU extensions to the POSIX recommendations
for command-line options. See
http://www.gnu.org/software/libc/manual/html_node/Argument-Syntax.html

pflag is available under the same style of BSD license as the Go language,
which can be found in the LICENSE file.

## Installation

pflag is available using the standard `go get` command.

Install by running:
    go get github.com/ogier/pflag

Run tests by running:
    go test github.com/ogier/pflag

## Usage

pflag is a drop-in replacement of Go's native flag package. If you import
pflag under the name "flag" then all code should continue to function
with no changes.

    import flag "github.com/ogier/pflag"

There is one exception to this: if you directly instantiate the Flag struct
there is one more field "Shorthand" that you will need to set.
Most code never instantiates this struct directly, and instead uses
functions such as String(), BoolVar(), and Var(), and is therefore
unaffected.

Define flags using flag.String(), Bool(), Int(), etc. Example:
    var ip *int = flag.Int("flagname", 1234, "help message for flagname")
If you like, you can bind the flag to a variable using the Var() functions.
    var flagvar int
    func init() {
        flag.IntVar(&flagvar, "flagname", 1234, "help message for flagname")
    }
Or you can create custom flags that satisfy the Value interface (with
pointer receivers) and couple them to flag parsing by
    flag.Var(&flagVal, "name", "help message for flagname")
For such flags, the default value is just the initial value of the variable.

After all flags are defined, call
    flag.Parse()
to parse the command line into the defined flags.

Flags may then be used directly. If you're using the flags themselves,
they are all pointers; if you bind to variables, they're values.
    fmt.Println("ip has value ", *ip);
    fmt.Println("flagvar has value ", flagvar);

After parsing, the arguments after the flag are available as the
slice flag.Args() or individually as flag.Arg(i).
The arguments are indexed from 0 up to flag.NArg().

The pflag package also defines some new functions that are not in flag,
that give one-letter shorthands for flags. You can use these by appending
'P' to the name of any function that defines a flag.
    var ip = flag.IntP("flagname", "f", 1234, "help message")
    var flagvar bool
    func init() {
        flag.BoolVarP("boolname", "b", true, "help message")
    }
    flag.VarP(&flagVar, "varname", "v", 1234, "help message")
Shorthand letters can be used with single dashes on the command line.
Boolean shorthand flags can be combined with other shorthand flags.

Command line flag syntax:
    --flag    // boolean flags only
    --flag=x
    --flag x  // non-boolean flags only
The last form is not permitted for boolean flags because the
meaning of the command
    cmd --flag *
will change if there is a file called 0, false, etc.  You must
use the --flag=false form to turn off a boolean flag.

Unlike the flag package, a single dash before an option means something
different than a double dash. Single dashes signify a series of shorthand
letters for flags. All but the last shorthand letter must be boolean flags.
    -f          // f must be boolean
    -abc        // all flags must be boolean
    -abcn=1234
    -abcn 1234  // n must be non-boolean
    -abcn1234   // n must be non-boolean
    -Ifile      // I must be non-boolean

Flag parsing stops after the terminator "--". Unlike the flag package,
flags can be interspersed with arguments anywhere on the command line
before this terminator.

Integer flags accept 1234, 0664, 0x1234 and may be negative.
Boolean flags may be 1, 0, t, f, true, false, TRUE, FALSE, True, False.
Duration flags accept any input valid for time.ParseDuration.

The default set of command-line flags is controlled by
top-level functions.  The FlagSet type allows one to define
independent sets of flags, such as to implement subcommands
in a command-line interface. The methods of FlagSet are
analogous to the top-level functions for the command-line
flag set.

## More info

You can get a full reference of the pflag package through go's standard
documentation system, for example by running `godoc -http=:6060` and
browsing to http://localhost:6060/pkg/github.com/ogier/pflag after
installation.
