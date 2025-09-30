# Welcome to MkDocs

For full documentation visit [mkdocs.org](https://www.mkdocs.org).

## Commands

* `mkdocs new [dir-name]` - Create a new project.
* `mkdocs serve` - Start the live-reloading docs server.
* `mkdocs build` - Build the documentation site.
* `mkdocs -h` - Print help message and exit.

## Project layout

    mkdocs.yml    # The configuration file.
    docs/
        index.md  # The documentation homepage.
        ...       # Other markdown pages, images and other files.

I proven write code using :fontawesome-brands-golang: and :simple-python:

```go title="say_hello" linenums="1" hl_lines="5-7"
func hello() string {
    return "Hello"
}

func calculate(a int, b int) int {
    return a * b
}
```