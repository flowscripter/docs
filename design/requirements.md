#### Documentation

**Status: Planned**

- Website homepage demo of terminal based CLI install and usage
- User guide: CLI, REPL
- Scripting guide
- Developer guide: 
    - embedding (e.g. using the Lib API)
    - feature development via commands and components (e.g. using the Component API)
- Lib API generated documentation and sample code
- Component API generated documentation and sample code
- Component documentation e.g. implemented operators, models etc.
- Codebase generated documentation
- Build instructions and design overview

#### CLI

**Status: Planned**

- Cross platform, command line executable
- Simple download and run
- Pretty coloured text
- Initial prompt to opt-in for usage reporting
- Plugin (command, component) discovery and installation at runtime
- Core built-in commands:
	- update and self-update
	- agent mode
	- REPL
	- plugin management
- No core built-in components

#### REPL

**Status: Planned**

- Launched from CLI
- Provides scripted access to lib API

#### Configuration

**Status: Planned**

- Configurable settings for CLI and core library
- YAML configuration file
- Library configured via API
- CLI reads configuration file and configures itself and library via API

#### Embeddable Library

**Status: Planned**

- Cross platform library
- Javascript API
- Component plugins can be installed and loaded

#### Extensible

**Status: Planned**

- Plugins for Commands and Components
- Component types such as operators, payload types, scripting objects, events and models
- Plugin lifecycle management via CLI e.g. scaffold, build, install, update, deploy
- Support for local/remote plugin repositories
- Plugin SDK for Rust and C++
- Dynamic discovery, install and loading of plugins
- Cross platform
- Configuration options can be added by plugins
- Commands are scripts with argument declarations
- CLI arguments can be added by command plugins

#### Example Plugins

**Status: Planned**

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

**Status: Planned**

- Extract info e.g. JSON output to local or remote sink
- Extract media e.g. media output to local or remote sink
- Validate e.g. bitstream validate, metadata value validate
- Duplicate e.g. local source to remote sink
- Update metadata e.g. updating parsed model when writing out, updating length and offsets as necessary
- Create/update media e.g. write out new media file with source media and metadata added
- Fix e.g. stream based replacement of values, parsing, handling bitstream errors and fixing, updating length and offsets as necessary
- Transcode e.g. decode and re-encode streamed media data
- Render e.g. rendered media data
- Analyse e.g. higher level processing of streamed data such as audio level analysis, then output as a streamed analysis model
- Stream in e.g. real-time/live processing of streamed media data
- Stream out e.g. real-time/live processing of streamed media data or streamed analysis data
- Expose VFS e.g. parse file, expose as constituent files which can be streamed out directly


#### Usage Scenarios

**Status: Planned**

- Try: immediate cross-platform binary download and immediate CLI usage
- Experiment: flexibility and automation using the CLI or REPL with settings and scripts
- Embed: embedding the library in JavaScript
- Extend: publish, discover, install and load plugins providing components and/or command extensions
- Use cloud storage: hybrid storage access, intelligent random access, install a module and configure access keys

```mermaid
flowchart LR
    User((User))
    subgraph Try
        T1[CLI] --> T2[bin + lib] -.-> T3[/Media/]
    end
    subgraph Experiment
        E1[CLI or REPL] --> E2[bin + lib]
        E2 -.-> E3[/Media/]
        E2 -.-> E4[/Settings/]
        E2 -.-> E5[/Script/]
    end
    subgraph Embed
        M1[Node App] --> M2[lib] -.-> M3[/Media/]
    end
    subgraph Extend
        X1[bin + lib] -->|REST| X2[flowscripter plugin repository on npmjs.com]
        X2 -.-> X3[/command/]
        X2 -.-> X4[/component/]
        X1 --- X5[component]
        X1 --- X6[command]
    end
    subgraph Cloud[Use Cloud Storage]
        C1[bin or embedding app + lib] --> C2[cloud storage] --- C3[S3, GCS, Azure Blob] -.-> C4[/Media/]
    end
    User --- T1
    User --- E1
    User --- M1
    User --- X1
    User --- C1
```

#### Use Cases

**Status: Planned**

```mermaid
flowchart LR
    User((User))
    Dev((Developer))
    subgraph CLI
        Install([Install])
        Configure([Configure])
        Update([Update])
        UseScript([Use Script])
        UseCLI([Use CLI])
        UseCommand([Use Command])
        UseREPL([Use REPL])
        ManageCLI([Manage Plugins])
    end
    subgraph Lib
        UseAPI([Use API])
        ConfigSettings([Configure Settings])
        UsageStats([Allow Usage Stats])
        UseComponent([Use Component])
        UseCore([Use Core])
    end
    subgraph npmjs[npmjs.com]
        ManageNpm([Manage Plugins])
        Deploy([Deploy Plugins])
        Search([Search Plugins])
    end
    subgraph SDK[SDK Dev Env]
        DevPlugin([Develop Plugin])
    end
    subgraph App[Custom Javascript App]
        Embed([Embed])
    end
    User --- Install
    User --- Configure
    User --- Update
    User --- UseCLI
    Dev --- ManageCLI
    Dev --- DevPlugin
    Dev --- Embed
    Install -.->|uses| UsageStats
    Configure -.->|extends| ConfigSettings
    Update -.->|extends| ManageCLI
    UseScript -.->|extends| UseCLI
    UseREPL -.->|extends| UseCLI
    UseCLI -.->|includes| UseCommand
    UseCLI -.->|uses| UseAPI
    UseAPI -.->|uses| ConfigSettings
    UseAPI -.->|uses| UseComponent
    ConfigSettings -.->|includes| UsageStats
    UseComponent -.->|uses| UseCore
    ManageCLI -.->|uses| ManageNpm
    ManageNpm -.->|includes| Deploy
    ManageNpm -.->|includes| Search
    DevPlugin -.->|extends| UseCLI
    Embed -.->|uses| UseAPI
```

#### User Journeys

**Status: Planned**

```mermaid
flowchart TD
    Start(( )) --> Home[flowscripter.org]
    Home -->|click news| News[flowscripter.org/news]
    Home -->|click source, issues| GH[github.org/flowscripter]
    Home -->|click docs| Docs[docs.flowscripter.org]
    Home -->|click forum| Forum[forum.flowscripter.org]
    Home -->|click plugins| Npm[npmjs.com]
    Home -->|click donate| Donate[Donation Popup]
    Home -->|click download| Download[flowscripter.org/download]

    subgraph Try
        DlBin[Download bin] --> Invoke[Invoke CLI]
        Invoke --> Share{Share Usage Set?}
        Share -->|No| Input[Get User Input and Set] --> Load[Load Commands]
        Share -->|Yes| Load
        Cmd[Command] --> Results[Processing / View Results]
    end
    Download --> DlBin

    subgraph Experiment
        Settings[Process Settings: write defaults if missing] --> Repl{REPL Command?}
        Repl -->|Yes| Interactive[Interactive Scripting]
        Interactive <--> ExecScript[Execute Script]
        Repl -->|No| ScriptCmd{Script Command?}
        ScriptCmd -->|Yes| ExecScript
        ScriptCmd -->|No| Cmd
    end
    Load --> Settings

    subgraph Embed
        SDK[Download SDK] --> Write[Write custom Javascript app] --> Launch[Launch custom Javascript app]
    end
    Download --> SDK

    subgraph Extend
        PluginCmd{Plugin command?}
        Scaffold{Scaffold command?}
        RepoCmd[Repository command: install, remove, versions, update, self-update, deploy, packages, search]
        Skeleton[Create skeleton project] --> DevPlugin[Develop plugin] --> Build[Build plugin]
        PluginCmd -->|Yes| Scaffold
        Scaffold -->|Yes| Skeleton
        Scaffold -->|No| RepoCmd
    end
    Settings --> PluginCmd
    PluginCmd -->|No| Repl
    RepoCmd -.-> Npm

    InvokeAPI[Invoke API]
    Results -.-> InvokeAPI
    Settings -.-> InvokeAPI
    Launch -.-> InvokeAPI
```
