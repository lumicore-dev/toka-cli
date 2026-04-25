# toka-cli

A lightweight, zero-dependency command-line argument parsing library for [Toka](https://github.com/toka-lang/toka).

## Features

- **Flags**: Boolean switches like `-v` / `--verbose`
- **Options**: Key-value arguments like `-o file` / `--output file`
- **Positional arguments**: Arguments without any flag prefix
- **Automatic `--help` and `--version`**: Built-in help text generation
- **Default values**: Specify default values for options
- **Required options**: Enforce mandatory arguments
- **No external dependencies**: Pure Toka standard library only

## Installation

Copy `cli.tk` into your project directory and import it:

```toka
import cli::{Cli}
```

Or add it as a dependency if using the Toka package manager.

## Quick Start

```toka
import cli::{Cli}
import std/string::String
import std/io::println
import std/env::args

fn main() -> i32 {
    // Create a new CLI app
    auto app# = Cli::new(
        String::from("myapp"),
        String::from("0.1.0"),
        String::from("A great CLI tool")
    )

    // Add a flag (boolean switch)
    app#.add_flag(
        String::from("verbose"),   // name
        'v',                       // short flag
        String::from("verbose"),   // long flag
        String::from("Enable verbose output")  // help text
    )

    // Add an option (takes a value)
    app#.add_option(
        String::from("output"),    // name
        'o',                       // short flag
        String::from("output"),    // long flag
        String::from("Output file path"),
        String::from("out.txt"),   // default value
        false                      // not required
    )

    // Add a positional argument
    app#.add_positional(
        String::from("file"),
        String::from("Input file to process")
    )

    // Parse command-line arguments
    auto raw = args()
    if !app.parse(raw) {
        return 0   // --help or --version was called
    }

    // Query parsed values
    if app.has(String::from("verbose")) {
        println("Verbose mode enabled")
    }

    auto out = app.get(String::from("output"))
    println("Output: {}", out.as_str())

    auto file = app.get_positional(0)
    if file.len() > 0:usize {
        println("File: {}", file.as_str())
    }

    return 0
}
```

## API Reference

### `Cli::new(name, version, about)`

Creates a new CLI application.

| Parameter | Type | Description |
|-----------|------|-------------|
| `name` | `String` | Application name |
| `version` | `String` | Version string (e.g. "0.1.0") |
| `about` | `String` | Short description |

### `add_flag(name, short, long, help)`

Registers a boolean flag (no value required).

| Parameter | Type | Description |
|-----------|------|-------------|
| `name` | `String` | Identifier for querying |
| `short` | `char` | Short flag character (e.g. `'v'`) |
| `long` | `String` | Long flag name (e.g. "verbose") |
| `help` | `String` | Help text description |

### `add_option(name, short, long, help, default, required)`

Registers an option that takes a value.

| Parameter | Type | Description |
|-----------|------|-------------|
| `name` | `String` | Identifier for querying |
| `short` | `char` | Short flag character |
| `long` | `String` | Long flag name |
| `help` | `String` | Help text description |
| `default` | `String` | Default value |
| `required` | `bool` | Whether this option is mandatory |

### `add_positional(name, help)`

Registers a positional argument.

| Parameter | Type | Description |
|-----------|------|-------------|
| `name` | `String` | Identifier for querying |
| `help` | `String` | Help text description |

### `parse(raw)`

Parses command-line arguments. Returns `true` on success, `false` on error or `--help`/`--version`.

### `has(name)`

Returns `true` if a flag was explicitly set.

### `get(name)`

Returns the value of an option or positional argument as `String`.

### `get_positional(index)`

Returns the positional argument at the given index (0-based).

### `positionals()`

Returns all positional arguments as `Vec<String>`.

### `print_help()`

Prints the auto-generated help message.

## Running the Demo

```bash
cd demo
tokac -I /path/to/toka/lib -I .. demo.tk -o demo -O3
./demo --help
./demo -v -o output.txt input.txt
```

## License

MIT
