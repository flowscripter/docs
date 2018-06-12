### Install

Download and unzip from [https://flowscripter.org/download](https://flowscripter.org/download)

### First Run

From a terminal:

`f10r` 

An initial prompt to share usage data will be displayed and a settings file will be created at:

`~/.f10r/settings.toml`

with the following contents:

```
[cli]
submit_usage_stats = true # anonymous submission of CLI usage to help improve Flowscripter

[packages] # advanced(!) configuration for Flowscripter wrapping of node npm functionality 
prefix=~/.f10r/ # location of node_modules folder for packages downloaded by Flowscripter
scope=f10r # scope to use for Flowscripter module packages
```

### Basic Help

`f10r --help` or `f10r`

which returns:

```
Usage: f10r [<options>] <command> [<arguments>]

Options:

	--settings=<filepath>	Use settings from <filepath> (defaults to '~/.f10r/settings.toml').

Core commands:

    help            Display this help message. Use 'help <command>' for detailed help.
    version         Display Flowscripter version.
    self-update     Update Flowscripter (including core commands and extensions).
    install         Install command and extension plugins.
    search          Search command and extension plugins.
    uninstall       Remove installed command and extension plugins.
    versions        Display version information for installed command and extension plugins.
    update          Update installed command and extension plugins.
    script          Execute a script using Flowscripter and display the result.
    repl            Start Flowscripter in REPL interactive mode.
    agent           Start Flowscripter in REST API agent mode.
    workbench       Start Flowscripter in REST API agent mode and launch the workbench UI in a browser. 

Core extensions:

    file
    plugin
    
Installed commands:

    None!
	
Installed extensions:

    None!
```

#### Basic Commands

`f10r version`

which returns:

```
Flowscripter v1.1.0

Your version of Flowscripter is out of date! The latest version is 1.2.1 
You can update by downloading from f10r.org or on the command line via 'f10r self-update'
```

Possibly followed by (for example):
```
The following plugins are not compatible with the current version and will need to be updated via 'f10r update':

    mxf-model
```

`f10r self-update`

which returns:

```
Flowscripter v1.1.0 

Updated Flowscripter to v1.2.1
```

Possibly followed by (for example):
```
The following plugins are not compatible with the current version and will need to be updated via 'f10r update':

    mxf-model
```

#### Installing a Command

`f10r install mxf-metadata-extract-command`

which returns:

```
@f10r/bmx@1.10.1
@f10r/bmx-model@1.10.1
@f10r/bmx-extractor@1.10.1
@f10r/mxf-metadata-extract-command@1.10.1
```

and results in a new folder and module sub-folders:

```
~/.f10r/node_modules/@f10r/bmx
~/.f10r/node_modules/@f10r/mxf-model
~/.f10r/node_modules/@f10r/bmx-extractor
~/.f10r/node_modules/@f10r/mxf-metadata-extract-command
```
and possibly other 3rd party packages which are transitive dependencies of the above packages in sub-folders of:

`~/.f10r/node_modules/`

Now when you run `f10r --help -plugins` the return includes:

```
Installed commands:

    @f10r/mxf-metadata-extract@1.10.1    Display metadata extracted from an MXF file.
	
Installed extensions:

    @f10r/bmx@1.10.1                     MXF file functionality courtesy of BMXLib
    @f10r/mxf-model@1.10.1               MXF file model
    @f10r/bmx-extractor@1.10.1           MXF metadata extract operator using BMXLib
	
```

The `mxf-metadata-extract-command` package is a Flowscripter command plugin and makes use of the `bmx` and `mxf-model` 
extension plugins.


Running:

`f10r help mxf-metadata-extract`

returns:

```
Description: 

    Display metadata extracted from an MXF file.

Usage:

    f10r mxf-metadata-extract <arguments>

Arguments:

    -url=<mxf_file_url>        The URL of an MXF from which to extract metadata.
```

#### REPL Example

`f10r repl`

which returns:

```
> 
```

A user can then interactively enter Javascript:

```
import { BmxExtractor } from '@f10r/bmx-extractor';
```

Further Javascript logic will allow the user to achieve the same effect as using the wrapped command:

`f10r mxf-metadata-extract -url=~/test.mxf`

A plugin can also be installed dynamically within the REPL:

```
import { PluginManager } from '@f10r/plugin';

myTestPlugin = PluginManager.importPlugin('my-test-extension');

```

The above will perform the equivalent of:
 
`f10r install my-test-extension`

Now when you run `f10r --help` the return includes:

```
Installed extensions:

    my-test-extension          My test extension
```

#### Script Example

A pre-existing script can be executed using:

`f10r script -file=script.js`

#### SDK Support

To develop command and extension plugins, you need to install the Flowscripter SDK:

`f10r install sdk`

which returns:

```
@f10r/sdk@1.10.1
```

and results in a new folder and module sub-folders:

```
~/.f10r/node_modules/@f10r/sdk
```
and possibly other 3rd party packages which are transitive dependencies of the above packages in sub-folders of:

`~/.f10r/node_modules/`

Now when you run `f10r --help` the return includes:

```
Installed commands:

    scaffold      Create a new SDK project for a Flowscripter plugin.
    build         Build a Flowscripter plugin.
    deploy        Publish a Flowscripter plugin.

```

##### npm and 3rd Party Packages

To build a plugin you will need `npm` installed.

When you scaffold a new project it will include a `package.json` file and suitable build scripts to build
 the Javascript plugin for usage in the NodeJS and browser runtimes. It will also support building
 native code as a WASM module.

If you configure `npm` (using `prefix`) and the Flowscripter `settings.toml` file to use 
the same modules prefix, you can use `npm` to manage 3rd party packages you wish to import within your plugin.
