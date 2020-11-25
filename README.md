# envsub

```
$ envsub [options]
```

The `envsub` program substitutes the values of environment variables.
Unlike [envsubst](https://www.gnu.org/software/gettext/manual/html_node/envsubst-Invocation.html), which only supports `${NAME}` style substitution, the `envsub` program also supports `${NAME-default}` and `${NAME:-default}` style substitution.

The program pipes stdin to stdout one line at a time applying the required substitutions.

## Command line options

`-h`, `--help`:: Displays the help details of the command and exit.

`-p`, `--prefix`:: Allows configuring an alternative prefix for substitution patterns if the default of `${` would cause conflicts.

`-s`, `--suffix`:: Allows configuring an alternative suffix for substitution patterns if the default of `}` would cause conflicts.

*NOTE:* If your suffix starts with `:` or `-` you will not have a good time as this will conflict with the default value separator that is internal to the pattern.

`-g`, `--greedy-defaults`:: Enables greedy replacement of unmatched default values. With this option if you have `${FOO}` and there is no corresponding variable `FOO` then that will remain untouched but `${FOO:-BAR}` or `${FOO-BAR}` will be replaced as `BAR` whereas without this option they would remain untouched.

`-v`, `--var`:: Only replace the named variable. _Can be specified multiple times_ if you want to replace multiple variables. If not specified then all variables from the environment can be substituted.

`-V`, `--version`:: Display the program version and exit.

## Examples

TIP: When running `envsub` without the `-v` argument then only patterns that have environment variables will be searched for, so the `${NAME-default}` form will never be replaced if `NAME` is not defined.

Basic usage:
```
$ (echo 'FOO=${FOO} or ${FOO-unset} or ${FOO:-empty}') | (unset FOO && envsub)
FOO=${FOO} or ${FOO-unset} or ${FOO:-empty}
$ (echo 'FOO=${FOO} or ${FOO-unset} or ${FOO:-empty}') | (unset FOO && envsub -v FOO)
FOO=${FOO} or unset or empty
$ (echo 'FOO=${FOO} or ${FOO-unset} or ${FOO:-empty}') | (FOO= envsub)
FOO= or  or empty
$ (echo 'FOO=${FOO} or ${FOO-unset} or ${FOO:-empty}') | (FOO= envsub -v BAR)
FOO=${FOO} or ${FOO-unset} or ${FOO:-empty}
$ (echo 'FOO=${FOO} or ${FOO-unset} or ${FOO:-empty}') | (FOO=123 envsub)
FOO=123 or 123 or 123
```

Custom prefixes and suffixes:
```
$ (echo 'FOO=%FOO:-empty%') | (FOO= envsub -p '%' -s '%')
FOO=empty
```
