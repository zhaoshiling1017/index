<!-- __JSON: egrunner script.sh # LONG ONLINE

## Using `github.com/rogpeppe/gohack`

[`gohack`](https://github.com/rogpeppe/gohack) is a tool that helps you make temporary edits to your Go module's
dependencies. By default, dependencies are stored in a read-only cache. `gohack` adds a `replace` directive to directory
where you can "hack" the dependency's code, and then removes the directive when you are done. For more details on
`replace` directives, see [the Go modules
wiki](https://github.com/golang/go/wiki/Modules#when-should-i-use-the-replace-directive).

This example shows how to use `gohack`.

### Walk-through

Install `gohack`:

```
{{PrintBlock "install gohack" -}}
```

Create a module:

```
{{PrintBlock "setup" -}}
```

Add a simple dependency to a `main` package in our module:

```
{{PrintBlock "simple example" -}}
```

In this case, we use a specific version of our dependency:

```
{{PrintBlock "use a specific version of quote" -}}
```

Run as a quick "test":

```
{{PrintBlock "run example" -}}
```

Now let's assume we want to tweak the `rsc.io/quote` package, in particular we want to tweak the `Hello` function we are
using. We use `gohack` to do this.


"Hack" on `rsc.io/quote`:

```
{{PrintBlock "gohack quote" -}}
```

Verify our module is using the local "hack" copy:

```
{{PrintBlock "see replace" -}}
```

`gohack` puts "hacks" in `$HOME/$module`; we make our changes there:


```
{{PrintBlock "make edit" -}}
```

Rerun our example:

```
{{PrintBlock "rerun" -}}
```

Revert to the original `rsc.io/quote`:

```
{{PrintBlock "undo" -}}
```

More specific information about subcommands is available via `help`, for example:

```
{{PrintBlock "gohack help get" -}}
```

### Version details

```
{{PrintBlockOut "version details" -}}
```

-->

## Using `github.com/rogpeppe/gohack`

[`gohack`](https://github.com/rogpeppe/gohack) is a tool that helps you make temporary edits to your Go module's
dependencies. By default, dependencies are stored in a read-only cache. `gohack` adds a `replace` directive to directory
where you can "hack" the dependency's code, and then removes the directive when you are done. For more details on
`replace` directives, see [the Go modules
wiki](https://github.com/golang/go/wiki/Modules#when-should-i-use-the-replace-directive).

This example shows how to use `gohack`.

### Walk-through

Install `gohack`:

```
$ git clone https://github.com/rogpeppe/gohack /tmp/gohack
$ cd /tmp/gohack
$ go install
go: finding github.com/rogpeppe/go-internal v1.0.0-alpha
go: finding golang.org/x/tools v0.0.0-20180917221912-90fa682c2a6e
go: finding gopkg.in/errgo.v2 v2.1.0
go: finding github.com/kr/pretty v0.1.0
go: finding gopkg.in/check.v1 v1.0.0-20180628173108-788fd7840127
go: finding github.com/kr/text v0.1.0
go: finding github.com/kr/pty v1.1.1
go: downloading github.com/rogpeppe/go-internal v1.0.0-alpha
go: downloading golang.org/x/tools v0.0.0-20180917221912-90fa682c2a6e
go: downloading gopkg.in/errgo.v2 v2.1.0
```

Create a module:

```
$ cd $HOME
$ mkdir using-gohack
$ cd using-gohack
$ go mod init example.com/blah
go: creating new go.mod: module example.com/blah
```

Add a simple dependency to a `main` package in our module:

```
$ cat blah.go
package main

import (
	"fmt"
	"rsc.io/quote"
)

func main() {
	fmt.Println(quote.Hello())
}
```

In this case, we use a specific version of our dependency:

```
$ go get rsc.io/quote@v1.5.1
go: finding rsc.io/quote v1.5.1
go: finding rsc.io/sampler v1.3.0
go: finding golang.org/x/text v0.0.0-20170915032832-14c0d48ead0c
go: downloading rsc.io/quote v1.5.1
go: downloading rsc.io/sampler v1.3.0
go: downloading golang.org/x/text v0.0.0-20170915032832-14c0d48ead0c
```

Run as a quick "test":

```
$ go run .
Hello, world.
```

Now let's assume we want to tweak the `rsc.io/quote` package, in particular we want to tweak the `Hello` function we are
using. We use `gohack` to do this.


"Hack" on `rsc.io/quote`:

```
$ gohack get rsc.io/quote
rsc.io/quote => /root/gohack/rsc.io/quote
```

Verify our module is using the local "hack" copy:

```
$ go mod edit -json
{
	"Module": {
		"Path": "example.com/blah"
	},
	"Require": [
		{
			"Path": "rsc.io/quote",
			"Version": "v1.5.1"
		}
	],
	"Exclude": null,
	"Replace": [
		{
			"Old": {
				"Path": "rsc.io/quote"
			},
			"New": {
				"Path": "/root/gohack/rsc.io/quote"
			}
		}
	]
}
```

`gohack` puts "hacks" in `$HOME/$module`; we make our changes there:


```
$ cd $HOME/gohack/rsc.io/quote
$ cat <<EOD >quote.go
package quote

func Hello() string {
	return "My hello"
}
EOD
```

Rerun our example:

```
$ cd $HOME/using-gohack
$ go run .
My hello
```

Revert to the original `rsc.io/quote`:

```
$ gohack undo rsc.io/quote
dropped rsc.io/quote
$ go run .
Hello, world.
```

More specific information about subcommands is available via `help`, for example:

```
$ gohack help get
usage: get [-vcs] [-u] [-f] [module...]

The get command checks out Go module dependencies
into a directory where they can be edited.

It uses $HOME/gohack/&lt;module&gt; as the destination directory.
(TODO implement directory overriding)

By default it copies module source code from the existing
source directory in $GOPATH/pkg/mod. If the -vcs
flag is specified, it also checks out the version control information into that
directory and updates it to the expected version. If the directory
already exists, it will be updated in place.
```

### Version details

```
go version go1.11.1 linux/amd64
gohack commit 2a6a2af0593c531b42442998c71abd3ba7965ef3
```

<!-- END -->