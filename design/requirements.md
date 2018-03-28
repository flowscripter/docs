#### Documentation
- Website homepage demo of terminal based CLI install and usage
- User guide: CLI, REPL, REST API
- Scripting guide
- Developer guide: 
    - embedding (library API)
    - extension via commands, operators and models (extension API)
- Plugin documentation e.g. operators, models
- Lib API generated documentation and sample code
- Extension API generated documentation and sample code
- REST API generated documentation 
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

#### Experimentation
- Browser workbench
- Access to scripting, model view and telemetry 
- Launched from CLI in agent mode or connect to agent after load
- Access to configuration
- Manage extensions
- Eventually create re-usable UI widgets

#### Embeddable
- Cross platform library
- Scripting API bindings:
	- Rust 
	- Java
	- Python
	- C++
- Modules can be installed and loaded

#### Integration
- CLI exposes lib API over REST
- CLI provides web hook callbacks for streaming state and telemetry

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
- Analyse e.g. higher level processing of streamed data such as audio level analysis, then output as a streamed analysis model
- Transcode e.g. decode and re-encode streamed media data
- Render e.g. rendered media data
- Stream in e.g. real-time/live processing of streamed media data
- Stream out e.g. real-time/live processing of streamed media data or streamed analysis data
- Expose VFS e.g. parse file, expose as constituent files which can be streamed out directly

