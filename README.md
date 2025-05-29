# Arista's EOS SDK

![EOS SDK logo](http://i.imgur.com/fNQ07mr.png)

The EOS Software Development Kit (EOS SDK) lets you program native,
high-performance apps that run on your Arista switch. These apps, or
"agents," harness the full power of EOS, including event-driven,
asynchronous behavior, high availability, and complete access to both
Linux and EOS's APIs.

This package provides a self-contained implementation of stubs for the EOS
SDK.  The stubs mock how the real EOS SDK behaves on a real switch, to a
certain extent.  They are useful to build and test software in your favorite
Linux environment, with your preferred build tools.


## Documentation
Documentation is hosted on this project's [wiki](https://github.com/aristanetworks/EosSdk/wiki). It includes a [quickstart guide](https://github.com/aristanetworks/EosSdk/wiki/Quickstart%3A-Hello-World), [build](https://github.com/aristanetworks/EosSdk/wiki/Build-and-Development-Environment) and [install](https://github.com/aristanetworks/EosSdk/wiki/Downloading-and-Installing-the-SDK) instructions, along with an [EOS overview](https://github.com/aristanetworks/EosSdk/wiki/Understanding-EOS-and-Sysdb) and high-level [SDK usage](https://github.com/aristanetworks/EosSdk/wiki/Using-the-SDK) information.

Detailed, per-release API Documentation is also available via the
release page.

## Releases

Releases are available via the [GitHub release
page](https://github.com/aristanetworks/EosSdk/releases). From that
page you can download specific tarballs for a given release so you can
build your agent. The corresponding EosSdk RPMs (that provide the
functionality for interacting with Arista devices) for a given EOS SDK
release is available via the [Arista download
page](https://www.arista.com/en/support/software-download) on a
per-EOS-release basis.

## Exploring the code

The directory structure is as follows: `eos/` contains the headers
that define the APIs you'll be using. In this directory, each `.h`
file is a module that provides access to a specific subset of EOS's
functionality. Most modules also have a companion file in
`eos/types/<module_name>.h`. This file defines the various value types
used by the module.

For a variety of `C++` and `Python` examples, see the `examples/`
directory. The stub `.cpp` files at the top level directory can be
ignored or extended to provide mock functionality, as you'd like.

## Building Go Bindings

The EOS SDK supports Go language bindings through SWIG (Simplified Wrapper and Interface Generator).

### Prerequisites

- SWIG 3.0 or later
- Go 1.11 or later
- GCC/G++ compiler
- The EOS SDK library (libeos.so)

### Generating Go Bindings

To generate Go bindings, use the `--go` flag with the build script:

```bash
# For 64-bit bindings (default)
./build.sh --go

# For 32-bit bindings
./build.sh -m32 --go
```

This will:
1. Generate Go wrapper code using SWIG
2. Apply necessary patches for CGO linking
3. Create the Go package in `go/src/eossdk/`

### Using the Go Bindings

After generating the bindings, you can use them in your Go code:

```go
import "eossdk"

func main() {
    sdk := eossdk.NewSdk()
    agentMgr := sdk.Get_agent_mgr()
    // Use the SDK...
}
```

To build your Go application:

#### Option 1: Using GOPATH (Go 1.10 and earlier style)

```bash
# Disable Go modules
export GO111MODULE=off

# Set environment variables
export GOPATH=/path/to/EosSdk/go
export CGO_CFLAGS="-I/path/to/EosSdk"
export CGO_LDFLAGS="-L/path/to/EosSdk/.libs -leos"

# For 32-bit builds, also set:
export GOARCH=386

# Build your application
go build your_app.go
```

#### Option 2: Using Go Modules (Go 1.11+ style)

If your application uses Go modules, you need to add a `replace` directive to your `go.mod` file:

```bash
# In your application directory, edit go.mod to add:
replace eossdk => /path/to/EosSdk/go/src/eossdk

# Then set the CGO environment variables:
export CGO_CFLAGS="-I/path/to/EosSdk"
export CGO_LDFLAGS="-L/path/to/EosSdk/.libs -leos"

# For 32-bit builds, also set:
export GOARCH=386
export CGO_CFLAGS="-I/path/to/EosSdk -m32"
export CGO_LDFLAGS="-L/path/to/EosSdk/.libs -leos -m32"

# Build your application
go build
```

**Note:** If the EOS SDK library was built in 32-bit mode (default), you must use `GOARCH=386` and add `-m32` to the CGO flags when building your Go application.

### Running Go Applications

When running your Go application, ensure the EOS SDK library is in your library path:

```bash
export LD_LIBRARY_PATH=/path/to/EosSdk/.libs:$LD_LIBRARY_PATH
./your_app
```

### Example

See `examples/MacTableIter.go` for a complete example of using the Go bindings.


