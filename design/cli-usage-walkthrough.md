### Install

Download and unzip from [https://t9r.org/download](https://t9r.org/download)

### First Run

From a terminal:

`t9r` 

An initial prompt to share usage data will be displayed and a settings file will be created at:

`~/.t9r/settings.toml`

with the following contents:

```
[cli]
submit_usage_stats = true # anonymous submission of CLI usage to help improve Tessellator

[packages] # configuration for Tessellator wrapping of node npm functionality
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
    self-update     Update Tessellator (including core commands and modules).
    install         Install commands and modules.
    search          Search commands and modules.
    remove          Remove installed commands and modules.
    versions        Display version information for installed commands and modules.
    update          Update installed commands and modules.
    script          Execute a script using Tessellator and display the result.
    repl            Start Tessellator in REPL interactive mode.
    agent           Start Tessellator in REST API agent mode.
    workbench       Start Tessellator in REST API agent mode and launch the workbench UI in a browser. 

Core modules:

    file

Installed commands:

    None!
	
Installed modules:

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

`t9r self-update`

which returns:

```
Tessellator v1.1.0 

Updated Tessellator to v1.2.1 
```

#### Installing a Command

`t9r install mxf-metadata-extract-command`

which returns:

```
@t9r/mxf-metadata-extract@1.10.1
@t9r/bmx@1.10.1
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

Now when you run `t9r --help` the return includes:

```
Installed commands:

    mxf-metadata-extract    Display metadata extracted from an MXF file.
	
Installed modules:

    bmx                     MXF file functionality courtesy of BMXLib
    mxf-model               MXF file model
    bmx-extractor           MXF metadata extract operator using BMXLib
	
```

The `mxf-metadata-extract-command` package is a t9r command plugin and makes use of the `bmx` and `mxf-model` modules.


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

#### Script Example

A pre-existing script can be executed using:

`t9r script -file=script.js`

#### SDK Support

To develop commands and modules or to manage 3rd party NodeJS packages, you need to install the Tessellator SDK:

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

    scaffold      Create a new SDK project for a Tessellator command or module.
    build         Build a Tessellator module which includes native code.
    packages      Manage 3rd party NodeJS packages.
    deploy        Publish a Tessellator command or module.

```

The `packages` command lets you install, update and delete 3rd party NodeJS packages. They will be downloaded to 
sub-folders in `~/.t9r/node_modules/`.

Note that `packages` and `deploy` are wrappers around `npm` functionality. If you have `npm` installed and you configure 
it (using `prefix`) and the Tessellator `settings.toml` file to use the same modules prefix, you can use `npm` instead
for equivalent functionality.

Private npmjs.com organisations can be used when using `npm` to manage Tessellator and 3rd party NodeJS packages.

If the current working directory contains a `packages.json` file, this will be used with the `packages` command
similar to using `npm`.

When a local `packages.json` file exists in the current folder, this package can be required within the REPL or a
Tessellator script when running Tessellator from that folder.
