# godoc-custom-fork

This repository contains the following features that are currently
unavailable in the upstream version of godoc:

- Viewing 'main' packages in godoc
- Parsing comments using [Slippery-Slope Markdown](https://github.com/doing-hacking/slippery-slope-markdown)
  - Supports bold text using `**bold**` syntax
  - Supports unordered lists using `-` syntax

## Install

The easiest way to install this godoc fork into your actual workflow
is to delete the `$GOPATH/src/golang.org/x/tools/godoc` folder and replace
it with the contents of this folder. Keep in mind, this will make your Go
repository inconsistent. (as godoc is packaged with the official go repo)

```
go get github.com/doing-hacking/slippery-slope-markdown

mkdir -p ~/repos && cd ~/repos
git clone git@github.com:doing-hacking/godoc-custom-fork.git
cd godoc-custom-fork
rm -rf $GOPATH/src/golang.org/x/tools/godoc/
mkdir $GOPATH/src/golang.org/x/tools/godoc/
cp -a . $GOPATH/src/golang.org/x/tools/godoc/
go install golang.org/x/tools/cmd/godoc
```

If you've installed godoc into your go workspace, you may need to run godoc
like this for changes to take effect:
```
$GOPATH/bin/godoc -http=:8080
```

## Future Hacks

There are some things I want to add to this repository if I find the time.

- Draw special attention to `//TODO: ` comments
- Change all internal package references so that this package can be used
  alongside the existing godoc package (rather then replacing it with the
  super-hacky shell code above)

# godoc

This directory contains most of the code for running a godoc server. The
executable lives at golang.org/x/tools/cmd/godoc.

## Development mode

In production, CSS/JS/template assets need to be compiled into the godoc
binary. It can be tedious to recompile assets every time, but you can pass a
flag to load CSS/JS/templates from disk every time a page loads:

```
godoc --templates=$GOPATH/src/golang.org/x/tools/godoc/static --http=:6060
```

## Recompiling static assets

The files that live at `static/style.css`, `static/jquery.js` and so on are not
present in the final binary. They are placed into `static/static.go` by running
`go generate`. So to compile a change and test it in your browser:

1) Make changes to e.g. `static/style.css`.

2) Run `go generate golang.org/x/tools/godoc/static` so `static/static.go` picks
up the change.

3) Run `go install golang.org/x/tools/cmd/godoc` so the compiled `godoc` binary
picks up the change.

4) Run `godoc -http=:6060` and view your changes in the browser. You may need
to disable your browser's cache to avoid reloading a stale file.
