# inspect
`inspect` is a small Go package that makes it easy to inspect information about `Go` source code.

`inspect` wraps functionality from standard `Go` packages such as `go/ast`, `go/parser` and `go/token`, to make it easy to inspect information about `Go` source code.

## Examples: 

#### Encode all standard library package's information into a single json file.

```go
package main

import (
	"encoding/json"
	"log"
	"os"
	"os/exec"
	"path/filepath"
	"strings"

	"github.com/radovskyb/inspect"
)

func main() {
	// Find the current Go intallation.
	gobin, err := exec.LookPath("go")
	if err != nil {
		log.Fatalln(err)
	}

	// Get the path of the root of the Go standard library packages.
	pkgsRoot := filepath.Join(strings.TrimSuffix(gobin, filepath.Join("bin", "go")), "src")

	// Get all packages.
	pkgs, err := inspect.ParsePackagesFromDir(pkgsRoot, true)
	if err != nil {
		log.Fatalln(err)
	}

	delete(pkgs, "main")

	jsonFile, err := os.Create("functions.json")
	if err != nil {
		log.Fatalln(err)
	}
	encoder := json.NewEncoder(jsonFile)
	encoder.SetIndent("", "\t")
	if err := encoder.Encode(pkgs); err != nil {
		log.Fatalln(err)
	}
}
```
