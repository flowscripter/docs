#### Documentation
- Website homepage demo of terminal based CLI install and usage
- User guide: CLI, REPL
- Scripting guide
- Developer guide: 
    - embedding (library API)
    - extension via commands, operators and models (extension API)
- Plugin documentation e.g. operators, models
- Lib API generated documentation and sample code
- Extension API generated documentation and sample code
- Codebase generated documentation
- Build instructions and design overview

#### CLI
- Cross platform, command line executable
- Simple download and run
- Pretty coloured text
- Initial prompt to opt-in for usage reporting
- Modules (command, model and operator) discovery and installation at runtime
- Core built-in commands:
	- update and self-update
	- agent mode
	- REPL
	- module management
- Core built-in operators: parallel, scriptable router

#### REPL
- Launched from CLI
- Provides scripted access to lib API

#### Configuration
- Configurable settings for CLI and core library
- TOML configuration file
- Library configured via API
- CLI reads configuration file and configures itself and library via API

#### Embeddable
- Cross platform library
- Javascript API
- Modules can be installed and loaded

#### Extensible
- Extensions for Commands, operators, payload types, scripting objects, events and models
- Project scaffolding via CLI
- Extension management via CLI e.g. scaffold, build, install, update, deploy
- Support for local/remote extension repositories
- Module SDK for Rust and C++
- Dynamic discovery, install and loading
- Cross platform
- Commands are scripts with argument declarations
- Configuration options can be added by modules
- CLI arguments can be added by commands

#### Example Plugins

- OpenEXR
- MXF
- IMF
- MPEG
- MP4
- YouTube 
- S3
- wavtotext
- bmxlib
- ffmpeg
- Editor similar to MoviePy
- Generator similar to MoviePy

#### Example Use Cases

- Extract info e.g. JSON output to local or remote sink
- Extract media e.g. media output to local or remote sink
- Validate e.g. bitstream validate, metadata value validate
- Duplicate e.g. local source to remote sink
- Update metadata e.g. updating parsed model when writing out, updating length and offsets as necessary
- Create/update media e.g. write out new media file with source media and metadata added
- Fix e.g. stream based replacement of values, parsing, handling bitstream errors and fixing, updating length and offsets as necessary
- Transcode e.g. decode and re-encode streamed media data
- Render e.g. rendered media data

