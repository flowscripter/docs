### Scripting

A NodeJS based runtime provides Javascript based access to the t9r framework library.

A core part of this API is a flow based processing model.

Modules extend the API with additional processing operators, data models, data stores, payload types and item attributes.      

### Runtime

The core runtime is exposed via a Javascript API executed within a NodeJS process.

Usage is via a single binary executable CLI.

The CLI also provides a REPL scripting environment and running in agent mode to expose a REST API. 

### Flow Based Processing

The core runtime provides a generic flow based processing framework (as well as data stores, registries etc.) 

It supports passing items of data of any payload type e.g. packet, frame between operators e.g. codecs over any kind of 
link e.g. in process, shared memory, UDP.
 
The flows of data between operators support concepts of live/real-time data, stream control items e.g. EOS and semantic 
items e.g. machine vision results.

The links between operators support operating with blocking, leaky or back-pressure semantics. 

Processing graph supports callbacks to allow for flexible dynamic behaviour of processing as the data flows change 
e.g. new flows added.
 
At the core all media is read and written using streams, but with the usage of stream attributes e.g. seekable, it is 
possible to expose efficient access if the underlying storage allows.

Useful 3rd-party tools e.g. ffmpeg can be utilised via modules which explicitly wrap specific functionality as operators.

Flow based processing logic can be performed:
    - 'in loop': via internal operator logic or via operator exposed callbacks
    - 'out of loop': via processing graph callbacks, dynamic changes to operator parameters and pre- or post-processing 
    access to model data accessible to operators via data stores

A processing session might consist of:

- a stream based approach of defining a graph for performing in-loop processing to read, modify and write out streams.
- a model based approach of defining a graph for reading from streams into a model, modifying the model and then 
defining a graph to write out to streams.

### Session

A session is a handle to maintain state for the core runtime. 

It allows graphs to be defined and models to be stored.

Session parameters, implementations and decorators provide the ability to use different data store backends, capture 
telemetry, manage resources and connect to remote agent instances.

A session allows partitioning of graph operators for assignment to particular resources.

### Graph

A graph consists of a number of operator instances connected together via links.

When a graph is added to a session, there are restrictions applied as to what graph changes can be made.

A graph provides for callbacks when changes to the graph structure or operators occur e.g. EOS, new pin

A sub-graph encapsulates a number of linked operators as a single operator allowing hierarchical definition of graphs. 

### Link

Links are used to connect output pins to input pins.

Links are defined to handle items of a specific payload type.

Links can operate either as:
- fixed length queues which are:
    - blocking 
    - leaky (leaky consumer or leaky producer) 
- non-blocking back-pressure links if both the producer and consumer support reactive stream semantics.  

- links have fixed size queues
- can be blocking, leaky consumer, leaky producer, back-pressure

Link parameters, implementations, interceptors and decorators provide functionality such as: 
- wiretaps
- automatic payload type conversion e.g. pixel format
- item history (stored in item attributes)
- telemetry instrumentation 

It is possible to globally configure interceptors for all links.

### Item

Items are the units of data passed from operators over links.

An item consists of:
- attributes: a key->object map
- payload type: negotiated between operators
- payload: a chunk of data of the specified payload type

Defined payload types are registered in the registry by modules.

Defined attribute keys are registered in the registry by modules. The registration includes whether the attributes are 
readonly, writable and whether their operator propagation rules.
 
Punctuation attribute keys e.g. end of stream are dded by the core framework to the registry.

### Extensions

An extension to the core system is either a module or a command.

Most of the core t9r functionality is implemented via extensions to the core runtime.

t9r extensions can be discovered and installed using the CLI. 

### Modules

Defined module types are:

- operator
- model
- registry
- data store
  
A module consists of a name, a version, dependencies, Javascript and optionally native code libraries.

### Commands

A command wraps Javascript functionality in a function so that it can be simply executed as a CLI command line argument. 

A commands consists of a name, a version, dependencies, Javascript and a definition of command line arguments and how these are mapped to arguments in 
the Javascript logic.

### Operators

An operator is implemented in native code and exposed via a Javascript binding.

An operator is defined by:

- properties: typed values which modify operator behaviour and can only be set before the operator is added to a graph 
- parameters: typed values which modify operator behaviour and can be set while processing is occurring
- pins:
	- label: identifier for the pin
	- type (producer, consumer): whether the pin produces or consumes items 
	- supportedPayloadTypes: supported item payload types for this pin
	- assignedPayloadType: item payload type assigned to the link connected to this pin
	- mandatory: the pin must be linked
	- static (1, n): a fixed number of these pins
	- dynamic (added, removed, 0..*): an unknown, variable number of these pins
	- requested (add, remove, 0..n): a variable, but fixed maximum number of these pins
    - constraints: constraints defined between pins
    
Example core operators:

- producer: produce items 
- consumer: consume items
- transform: modify or consume and produce new items e.g. Attribute Filter, Attribute Enricher
- route e.g. Payload Type Router, Attribute Router, Scripted Router
- filter: only pass on certain items e.g. a scripted filter:
    ```
    Filter(function (item) {
        return item.attributes["discard"] > 30;
    })
    ```
- map: for each input item, map to a specific output value e.g. a scripted map operator:
    ```
    Map(quote => quote.price)
    ```
- batch: batch individual items into an array of items
- sequence: split arrays of items into individual items
- data store: read or write to a data store
- adaptor: producer or consumer which can link with external sources and sinks of items e.g. webrtc, websockets  
- scatter: input items on a single input pin are scattered (according to implemented logic) across multiple output pins
- gather: input items on multiple input pins are gathered (according to implemented logic) and output on a single output pin
- parallel (with scatter and gather): allows parallel processing - each input item is scattered across a number of 
parallel paths and the resulting items of each processing path are gathered together in correct order   
- tee: output copies of the same input items onto multiple separate output pins
- split: split input items into smaller output items on separate pins e.g. demultiplexer
- aggregate: combine multiple input items on separate pins into a single larger output item e.g. multiplexer

### Models

Models define explicit typed structure access to:

- item data e.g. packet structures for each specific payload type
- physical and logical file data e.g. file formats and file content relationships 
- metadata e.g. extracted semantic data  
  
### Registry

The registry is updated by modules when they provide implementations of:

- payload types
- item attribute keys

### Data Stores

Data stores provide the ability to persist information for 'out of band' access.

Data stores can be used to store:

- model instances
- explicitly saved item attributes and payloads

Implementations of data stores determine if the data:

- is maintained in memory
- persisted intermittently
- remotely stored

### Packages

As the runtime is based on NodeJS, the underlying plugin and module system is based on npm support for packages. 

The CLI can be used to:

- manage and use t9r modules using t9r commands without any knowledge of NodeJS or npm.
- manage t9r modules and 3rd party NodeJS packages with knowledge of npm.

For NodeJS developers and t9r plugin framework users, standard NodeJS and npm can also be used to manage and use t9r 
modules and 3rd party NodeJS packages.

### Plugin Framework 

Plugin framework for t9r modules defines extension points to implement:

- commands
- operators
- models
- payload types
- data stores
- attribute keys

Payload types and models can extend the Javascript API.

### Resource

The processing framework provides resource concepts for IO, memory and compute.

Allocation of resources to operators in graphs is handled via a session.

### Logging and Telemetry

Telemetry is recorded at various levels of granularity for three purposes:

- internal dynamic optimisation of flow processing
- analysis and diagnosis of session processing by users
- anonymous summary usage data to understand usage and inform future roadmap decisions

Raw logging and telemetry data can be written to various adaptors e.g. standard out, remote data store, websockets.

### Browser Integration

When running the CLI in agent mode, the workbench browser UI can be used for integration.

The interaction between the agent process and the browser is performed via:

- the agent REST API
- redirection of the REPL standard input and output over HTTP
- sending logging and telemetry over websockets
- intercepting data store updates and sending over websockets

The workbench can be launched either by:

- running the CLI with the workbench command which will launch a browser, load the workbench UI and connect to the local 
instance
- loading the workbench UI in a browser and specifying an existing agent instance to connect to 

The workbench UI includes components for:

- rendering graphs of telemetry data
- rendering filtered tables for logging
- presenting operator parameters as UI forms
- presenting runtime configuration as UI forms
- structured rendering of data store contents
