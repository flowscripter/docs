### Install

Download and unzip from [https://tessellator.org/download](https://tessellator.org/download)

### First Run

From a terminal:

`t9r` 

An initial prompt to share usage data will be displayed and a settings file will be created at:

`~/.t9r/settings.toml`

with the following contents:

```
[cli]
submit_usage_stats = true # anonymous submission of CLI usage to help improve Tessellator

[packages] # advanced(!) configuration for Tessellator wrapping of node npm functionality 
prefix=~/.t9r/ # location of node_modules folder for packages downloaded by Tessellator
scope=t9r # scope to use for Tessellator module packages
```

### Basic Help

`t9r --help` or `t9r`

which returns:

```
Usage: t9r [<options>] <command> [<arguments>]

Options:

	--settings=<filepath>	Use settings from <filepath> (defaults to '~/.t9r/settings.toml').

Core commands:

    help            Display this help message. Use 'help <command>' for detailed help.
    version         Display Tessellator version.
    self-update     Update Tessellator (including core commands and extensions).
    install         Install command and extension plugins.
    search          Search command and extension plugins.
    uninstall       Remove installed command and extension plugins.
    versions        Display version information for installed command and extension plugins.
    update          Update installed command and extension plugins.
    script          Execute a script using Tessellator and display the result.
    repl            Start Tessellator in REPL interactive mode.
    agent           Start Tessellator in REST API agent mode.
    workbench       Start Tessellator in REST API agent mode and launch the workbench UI in a browser. 

Core extensions:

    file
    plugin
    
Installed commands:

    None!
	
Installed extensions:

    None!
```

#### Basic Commands

`t9r version`

which returns:

```
Tessellator v1.1.0

Your version of Tessellator is out of date! The latest version is 1.2.1 
You can update by downloading from t9r.org or on the command line via 't9r self-update'
```

Possibly followed by (for example):
```
The following plugins are not compatible with the current version and will need to be updated via 't9r update':

    mxf-model
```

`t9r self-update`

which returns:

```
Tessellator v1.1.0 

Updated Tessellator to v1.2.1
```

Possibly followed by (for example):
```
The following plugins are not compatible with the current version and will need to be updated via 't9r update':

    mxf-model
```

#### Installing a Command

`t9r install mxf-metadata-extract-command`

which returns:

```
@t9r/bmx@1.10.1
@t9r/bmx-model@1.10.1
@t9r/bmx-extractor@1.10.1
@t9r/mxf-metadata-extract-command@1.10.1
```

and results in a new folder and module sub-folders:

```
~/.t9r/node_modules/@t9r/bmx
~/.t9r/node_modules/@t9r/mxf-model
~/.t9r/node_modules/@t9r/bmx-extractor
~/.t9r/node_modules/@t9r/mxf-metadata-extract-command
```
and possibly other 3rd party packages which are transitive dependencies of the above packages in sub-folders of:

`~/.t9r/node_modules/`

Now when you run `t9r --help -plugins` the return includes:

```
Installed commands:

    @t9r/mxf-metadata-extract@1.10.1    Display metadata extracted from an MXF file.
	
Installed extensions:

    @t9r/bmx@1.10.1                     MXF file functionality courtesy of BMXLib
    @t9r/mxf-model@1.10.1               MXF file model
    @t9r/bmx-extractor@1.10.1           MXF metadata extract operator using BMXLib
	
```

The `mxf-metadata-extract-command` package is a Tessellator command plugin and makes use of the `bmx` and `mxf-model` 
extension plugins.


Running:

`t9r help mxf-metadata-extract`

returns:

```
Description: 

    Display metadata extracted from an MXF file.

Usage:

    t9r mxf-metadata-extract <arguments>

Arguments:

    -url=<mxf_file_url>        The URL of an MXF from which to extract metadata.
```

#### REPL Example

`t9r repl`

which returns:

```
> 
```

A user can then interactively enter Javascript:

```
import { BmxExtractor } from '@t9r/bmx-extractor';
```

Further Javascript logic will allow the user to achieve the same effect as using the wrapped command:

`t9r mxf-metadata-extract -url=~/test.mxf`

A plugin can also be installed dynamically within the REPL:

```
import { PluginManager } from '@t9r/plugin';

myTestPlugin = PluginManager.importPlugin('my-test-extension');

```

The above will perform the equivalent of:
 
`t9r install my-test-extension`

Now when you run `t9r --help` the return includes:

```
Installed extensions:

    my-test-extension          My test extension
```

#### Script Example

A pre-existing script can be executed using:

`t9r script -file=script.js`

#### SDK Support

To develop command and extension plugins, you need to install the Tessellator SDK:

`t9r install sdk`

which returns:

```
@t9r/sdk@1.10.1
```

and results in a new folder and module sub-folders:

```
~/.t9r/node_modules/@t9r/sdk
```
and possibly other 3rd party packages which are transitive dependencies of the above packages in sub-folders of:

`~/.t9r/node_modules/`

Now when you run `t9r --help` the return includes:

```
Installed commands:

    scaffold      Create a new SDK project for a Tessellator plugin.
    build         Build a Tessellator plugin.
    deploy        Publish a Tessellator plugin.

```

##### npm and 3rd Party Packages

To build a plugin you will need `npm` installed.

When you scaffold a new project it will include a `package.json` file and suitable build scripts to build
 the Javascript plugin for usage in the NodeJS and browser runtimes. It will also support building
 native code as a WASM module.

If you configure `npm` (using `prefix`) and the Tessellator `settings.toml` file to use 
the same modules prefix, you can use `npm` to manage 3rd party packages you wish to import within your plugin.
