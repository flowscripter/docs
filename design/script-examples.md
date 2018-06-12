#### Basic Graph Concepts

```
var payloadType1 = new BinaryDataChunkPayloadType();
var payloadType2 = new Mpeg2TransportStreamPacketPayloadType();
var payloadType3 = new Mpeg2ElementaryStreamPacketPayloadType
var payloadType4 = new JsonPayloadType();

var link1 = new Link(payloadType1)
var link2 = new Link(payloadType1)
var link3 = new Link(payloadType1)
var link4 = new Link(payloadType2)
var link5 = new Link(payloadType3)
var link6 = new Link(payloadType4)
var link7 = new Link(payloadType4)
var link8 = new Link(payloadType4)

var node1 = new Source(link1);
var node2 = new Fork(link1, {out1: link2, out2: link3});
var node3 = new X(link2, link4);
var node4 = new Y(link3, link5);
var node5 = new Dual({in1: link4, in2: link5}, {out1: link6, out2: link7});
var node6 = new Join({in1: link6, in2: link7}, link8)
var node7 = new Sink(link8);

var graph = new Graph([node1, node2, node3, node4, node5, node6, node7]);

var session = new Session();
session.add(graph);
session.resume(graph)

session.pause(graph)

session.resumeAndWaitForCompletion(graph);
session.destroy();
```

#### Chains

Automatic links with default negotiated payload types:

```
var graph = new Graph(new Chain(new Source(), new X(), new Y(), new Sink()));

var session = new Session();
session.add(graph);
session.resumeAndWaitForCompletion(graph);
session.destroy();
```

Simple scenarios for multiple paths are also supported:

```
var graph = new Graph(new Chain(new Source(), new Fork(), {new X(), new Y()}, new Dual(), new Join(), new Sink()));

var session = new Session();
session.add(graph);
session.resumeAndWaitForCompletion(graph);
session.destroy();
```

#### Multiple Graphs Per Session

Multiple graphs can be processed at the same time:
 
```
var graph1 = new Graph(new Chain(new Source(), new X(), new Y(), new Sink()));
var graph2 = new Graph(new Chain(new Source(), new Z(), new Sink()));

var session = new Session();
session.add(graph1);
session.resume(graph1);

session.add(graph2);
session.resumeAndWaitForCompletion(graph2);

session.resumeAndWaitForCompletion(graph1);
session.destroy();
```

#### Hierarchical Graphs

An incomplete chain used to construct a sub-graph (unconnected pins will be exposed on sub-graph):

```
var childGraph = new SubGraph(new Chain(new X(), new Y()));

// Use child graph as a node
var parentGraph = new Graph(new Chain(new Source(), childGraph, new Sink()));

var session = new Session();

session.add(parentGraph);
session.resumeAndWaitForCompletion(parentGraph);
session.destroy();
```

A `SubGraph` also exposes an API to explicitly expose pins and operator parameters and properties.

#### Parallel Execution 

Automatic and transparent parallel execution of paths by the framework can currently only be performed 
on single operators and for operators with only a single input and single output.

However explicit parallel execution can also be performed. 

You can setup parallel execution of an individual operator or a sub-graph:

```
var childGraph = new SubGraph(new Chain(new X(), new Y()));

var parentGraph = new Graph(new Chain(new Source(), new Parallel(new Scatter(), childGraph, new Gather(), 3), new Sink()));
```

Simple scenarios for multiple inputs or outputs can be supported:

```
var parentGraph = new Graph(new Chain(new DualOutputSource(), new Parallel(new Scatter(), { new X(), new Y() }, new Gather(), 3), new DualInputSink()));
```

Alternatively, you could use an `aggregate()` operator before the `scatter()` operator and a `split()` operator after the `scatter()` operator 
(and the same for before and after the `gather()` operator):


```
var parentGraph = new Graph(new Chain(new DualOutputSource(), new Aggregate(), new Parallel(new Scatter(), new Split(), { new X(), new Y() }, new Aggregate(), new Gather(), 3), new Split(), new DualInputSink()));
```

For multiple scatter without gather:

```
var graph = new Graph(new Chain(new Source(), new Parallel(new Scatter(), new Sink(), 3)));
```

For multiple gather without scatter:

```
var graph = new Graph(new Chain(new Parallel(new Source(), new Gather(), 3), new Sink()));
```

#### Use Case Example 1

To achieve this functionality:

```
S3 => bmxlib => metadata output | workbench render
```

use this:

```
import { S3Reader } from '@f10r/s3';
import { BmxExtractor } from '@f10r/bmx-extractor';
import { MxfModel } from '@f10r/mxf-model';
import { ModelStoreRecorder } from '@f10r/model';

var s3Reader = new S3Reader(url: 's3://bucketname/filename.mxf);
var bmxExtractor = new BmxExtractor();
var storeRecorder = new ModelStoreRecorder(new MxfModel());

var graph = new Graph(s3Reader, bmxExtractor, storeRecorder);

var session = new Session();
session.resumeAndWaitForCompletion(graph);

// To display in workbench, use a model datastore which pushes updates over websockets

```

#### Use Case Example 2

To achieve this functionality:

```
File/URL => mp3 to wav => wav to text => timecoded script data points | workbench render
```

use this:

```
import { HttpReader } from '@f10r/http';
import { Mp4Parser, Mp4Demuxer } from '@f10r/mp4';
import { Mp3Parser } from '@f10r/mp3';
import { AudioTranscoder } from '@f10r/audio';
import { Transcripter, TranscriptModel } from '@f10r/audio-tools';
import { ModelStoreRecorder } from '@f10r/model';

var httpReader = new HttpReader({url: 'http://partliament.org/media/test.mp4'});
var mp4Parser = new Mp4Parser();
var mp4Demuxer = new Mp4Demuxer();
var mp3Parser = new Mp3Parser();
var audioTranscoder = new AudioTranscoder();
var transcript = new Transcripter();
var storeRecorder = new ModelStoreRecorder(new TranscriptModel());

var chain1 = new Chain(httpReader, mp4Parser, mp4Demuxer);
var chain2 = new Chain(mp3Parser, audioTranscoder, transcript, storeRecorder);

var graph = new Graph(chain1);

graph.onEvent(Symbol.for("newPin"), function newPin(event, session, graph, operator, pin) {
        if (operator == mp4Demuxer) && (pin.payloadType == 'mp3') {
            session.pause(graph);
            graph.append(chain1, chain2);
            session.resume(graph);
        }
    })

var session = new Session();
session.resumeAndWaitForCompletion(graph);

// To display in workbench, use a model datastore which pushes updates over websockets
```
