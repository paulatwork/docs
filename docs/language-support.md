The Nitric SDKs communicate with the Nitric server using gRPC. The choice of gRPC and Protocol Buffers ensures the Nitric framework can support many languages without rewriting the entire framework from scratch.

The Nitric team provides idiomatic languages SDKs, designed to feel familiar to developers of each language and make the best use of unique language features. In addition, a set of base SDKs are auto-generated from the Protobuf contracts and published in the [APIs](https://github.com/nitrictech/apis) project.

## Supported Languages

The following runtimes are currently supported or are in development:

| Language Runtime                                   | Support        |
| -------------------------------------------------- | -------------- |
| [Node.js](https://github.com/nitrictech/node-sdk)  | Full Support   |
| [Go](https://github.com/nitrictech/go-sdk)         | In Preview     |
| [JVM](https://github.com/nitrictech/jvm-sdk)       | Experimental   |
| [Python](https://github.com/nitrictech/python-sdk) | Experimental   |
| [C#](https://github.com/nitrictech/dotnet-sdk)     | In Development |

If a language you want isn't listed, come chat to us on [Discord](https://discord.gg/Webemece5C).

Alternatively, feel free to develop your own SDKs. Nitric is completely [open source](https://github.com/nitrictech) so you can use any of the other SDKs linked above as a reference.

## Adding language support

To start adding support for a language, check out the [protobuf contracts](https://github.com/nitrictech/nitric/tree/main/contracts), and look at how to compile the base SDKs for your specific language on the [protobuf docs](https://developers.google.com/protocol-buffers). Compiling the protobuf definition to a base SDK will provide gRPC stubs for connecting with the Nitric server, as well as language specific types for constructing request and response objects.
