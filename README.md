golang-vfstemplate
=======================

Golang library for parsing template files from virtual http filesystem (httpfs)

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

## Examples

### Using with statik and echo

Assume that we have the folloring directory structure:

```
.
static/
static/home.html
main.go
```
```go
//go:generate statik -src static -dest . -p staticfiles -f
package main

import (
	"html/template"
	"log"
	"net/http"

	"github.com/labstack/echo/v4"
	"github.com/rakyll/statik/fs"
	vfstemplate "github.com/shakahl/golang-vfstemplate"

	_ "myproject/staticfiles"
)


type Template struct {
	templates *template.Template
}

func (t *Template) Render(w io.Writer, name string, data interface{}, c echo.Context) error {
	return t.templates.ExecuteTemplate(w, name, data)
}

func main() {

	statikFS, err := fs.New()
	if err != nil {
		log.Fatal(err)
	}

	staticFileServer := http.StripPrefix("/static/", http.FileServer(statikFS))

	t := &Template{
		templates: template.Must(vfstemplate.ParseGlob(statikFS, nil, "/views/*.html")),
	}

	e := echo.New()
	e.Renderer = t
	e.GET("/static/*", echo.WrapHandler(staticFileServer))
	e.GET("/", func(c echo.Context) error {
		return c.Render(http.StatusOK, "home.html", map[string]interface{}{
			"Title":    "Welcome",
		})
	})
	e.Logger.Fatal(e.Start(":8080"))
}

```

## Credits

This project is based on [shurcooL/httpfs](https://github.com/shurcooL/httpfs) maintained by [Dmitri Shuralyov](https://github.com/dmitshur).

## License

[MIT](./LICENSE)
