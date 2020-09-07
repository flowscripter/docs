### Scripting

A Javascript API exposes access to the Flowscripter runtime.

A core part of this API is a flow based processing model.

Plugins extend the API with additional processing operators, data models, datastores, payload types and item attribute keys.      

### CLI

The core processing is executed within a NodeJS runtime where usage is via a single binary executable CLI. 
The CLI provides a REPL scripting environment.

### Flow Based Runtime

The core runtime provides a generic flow based processing framework (as well as datastores, registry etc.) 

It supports passing items of data of any payload type (e.g. packet, frame) between operators (e.g. codecs) over any kind of 
link (e.g. in process, shared memory, UDP).

As well as 'essence' data, the flows between operators support concepts of stream control items (e.g. EOS) and semantic 
items (e.g. machine vision results).

The links between operators support blocking, leaky or back-pressure semantics. 

The processing graph supports callbacks to allow for flexible dynamic behaviour of processing as the data flows change 
(e.g. new flow added).
 
At the core, all items are accessing using readable and writable streams, but with the usage of stream attributes
(e.g. seekable), it is possible to expose efficient access if the underlying storage allows.

Useful 3rd-party tools (e.g. ffmpeg) can be utilised via component plugins which explicitly wrap specific functionality
 as operators.

Logic can be performed:
- *in loop*: i.e. within the execution of the processing graph via internal operator logic or via operator exposed callbacks.
- *out of loop*: i.e. outside the execution of the processing graph via graph callbacks, dynamic changes to operator parameters and pre- or post-processing 
access to model data (which is accessible to operators via datastores).

A processing session can consist of either a:

- *stream based approach*: defining a graph for performing *in-loop* processing to read, modify and write out streams.
- *model based approach*: defining a graph for reading from streams into a model, modifying the model *out of loop* and then 
defining a graph to write out to streams.

### Session

A session is a handle to maintain state for the core runtime. 

It allows graphs to be defined and models to be stored.

Session parameters, implementations and decorators provide the ability to use different datastore backends, capture 
telemetry and manage resources.

A session allows partitioning of graph operators for assignment to particular resources.

### Graph

A graph consists of a number of operators connected together via links.

When a graph is added to a session, there are restrictions applied as to what changes can then be made to the graph.

A graph provides for callbacks when changes to the graph structure or operators occur (e.g. EOS, new pin).

A sub-graph encapsulates a number of linked operators as a single operator allowing hierarchical definition of graphs. 

### Link

Links are used to connect output pins to input pins.

Links are defined to handle items of a specific payload type.

Links can operate either as:

- fixed length queues which are:
    - blocking 
    - leaky (leaky consumer or leaky producer) 
- non-blocking, back-pressure links if both the producer and consumer support reactive stream semantics.  

Link parameters, implementations, interceptors and decorators provide functionality such as: 

- wiretaps
- automatic payload type conversion (e.g. pixel format)
- item history (stored in item attributes)
- telemetry instrumentation 

It is possible to globally configure interceptors for all links.

### Item

Items are the units of data passed from operators over links.

An item consists of:

- *attributes*: a key->object map
- *payload type*: negotiated between operators
- *payload*: a chunk of data of the specified payload type

Defined payload types are registered in the registry by components.

Defined item attribute keys are registered in the registry by components. The registration includes whether the attributes are 
readonly, writable and their operator propagation rules.
 
Punctuation attribute keys e.g. EOS are added by the core framework to the registry.

### Plugin Framework 

A plugin to the core system is either a component or a command.

The plugin framework for Flowscripter provides the ability to implement plugins as JavaScript modules.
 
Flowscripter plugins can be discovered and installed using the CLI. 

### Commands

A command wraps Javascript functionality in a function so that it can be simply executed as a CLI command line argument. 

A command consists of a name, a version, dependencies on other plugins, Javascript and a definition of command line arguments and 
how these are mapped to arguments in the Javascript logic.

### Components

Defined component types are:

- *operator*
- *data model*
- *datastore*
- *registry*
- *payload type*
- *attribute key*
  
A component consists of a name, a version, dependencies on other plugins, Javascript and optionally native code libraries.

Payload types and data models can extend the Javascript API.

Most of the core Flowscripter functionality is implemented via components which extend the core runtime.

##### Operators

An operator is implemented in native code and exposed via a Javascript binding.

An operator is defined by:

- *properties*: typed values which modify operator behaviour and can only be set before the operator is added to a graph 
- *parameters*: typed values which modify operator behaviour and can be set while processing is occurring
- *pins*:
	- *label*: identifier for the pin
	- *type* (producer, consumer): whether the pin produces or consumes items 
	- *supportedPayloadTypes*: supported item payload types for this pin
	- *assignedPayloadType*: item payload type assigned to the link connected to this pin
	- *mandatory*: the pin must be linked
	- *static* (1, n): a fixed number of these pins
	- *dynamic* (added, removed, 0..*): an unknown, variable number of these pins
	- *requested* (add, remove, 0..n): a variable, but fixed maximum number of these pins
    - *constraints*: constraints defined between pins
    
Example core operators:

- *produce*: produce items 
- *consume*: consume items
- *transform*: modify or consume and produce new items e.g. Attribute Filter, Attribute Enricher
- *route*: e.g. Payload Type Router, Attribute Router, Scripted Router
- *filter*: only pass on certain items e.g. a scripted filter:
    ```
    Filter(function (item) {
        return item.attributes["discard"] > 30;
    })
    ```
- *map*: for each input item, map to a specific output value e.g. a scripted map operator:
    ```
    Map(quote => quote.price)
    ```
- *batch*: batch individual items into an array of items
- *sequence*: split arrays of items into individual items
- *data accessor*: read or write to a datastore
- *scatter*: input items on a single input pin are scattered (according to implemented logic) across multiple output pins
- *gather*: input items on multiple input pins are gathered (according to implemented logic) and output on a single output pin
- *parallel* (with scatter and gather): allows parallel processing - each input item is scattered across a number of 
parallel paths and the resulting items of each processing path are gathered together in correct order   
- *tee*: output copies of the same input items onto multiple separate output pins
- *split*: split input items into smaller output items on separate pins e.g. demultiplexer
- *aggregate*: combine multiple input items on separate pins into a single larger output item e.g. multiplexer
- *adapt*: producer or consumer which can link with external sources and sinks of items e.g. webrtc, websockets  

##### Data Models

Data models define explicit typed structure access to:

- *item data* e.g. packet structures for each specific payload type
- *physical and logical file data* e.g. file formats and file content relationships 
- *metadata* e.g. extracted semantic data  

##### Datastores

Datastores provide the ability to persist information for *out of loop* access.

Datastores can be used to store:

- model instances
- explicitly saved item attributes and payloads (a data model defines these as a model entry)

Implementations of datastores determine if the data is:

- maintained in memory
- persisted intermittently
- remotely stored
  
##### Registry

The registry is updated by components when they provide implementations of:

- payload types
- item attribute keys
- operators
- data models
- datastores

### Javascript Modules

As the runtime uses ES6 modules, command plugins can make use of 3rd party Javascript modules. Support for importing these
in the NodeJS runtime is transparent to the plugin implementer. 

The CLI is used to manage and use Flowscripter plugins using Flowscripter commands without any knowledge of NodeJS or npm.

It is also used to scaffold, build and deploy Flowscripter plugins.

`npm` is used to manage 3rd party Javascript modules packages.

### Native Code

Component plugins can include native code that can be written using Rust or C/C++ SDK bindings. 

The core Flowscripter library is developed with the same methodology.
 
### Resource

The processing framework provides resource concepts for IO, memory and compute.

Allocation of resources to operators in graphs is handled via a session.

### Logging and Telemetry

Telemetry is recorded at various levels of granularity for three purposes:

- internal dynamic optimisation of flow processing
- analysis and diagnosis of session processing by users
- anonymous summary usage data to understand usage and inform future roadmap decisions

Raw logging and telemetry data can be written to various adaptors e.g. standard out, remote datastore, websockets.
