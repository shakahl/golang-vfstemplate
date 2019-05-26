golang-vfstemplate
=======================

## Installation

```
go get github.com/shakahl/golang-vfstemplate
```

## Usage

Parsing templates by glob pattern:

```
template.Must(vfstemplate.ParseGlob(myhttpfs, nil, "/views/*.html")
```

Parsing templates by specifying a list of file paths:

```
vfstemplate.ParseFiles(myhttpfs, nil, "/views/first.html", "/views/second.html")
```

