# BlazorSignalR
This package is a compatibility library for [Microsoft ASP.NET Core SignalR](https://github.com/aspnet/SignalR) to allow it to run on [Microsoft ASP.NET Blazor](https://github.com/aspnet/Blazor).

The package is an addon for the existing .net client for SingalR, this is unlike the ```BlazorExtensions/SignalR``` package which emulates the c# api. This package instead works by replacing the transport mechanics, meaning the front facing SignalR api is still the standard .net one. The benefits of this setup is that as SignalR changes, this package takes little maintenance, as it only replaces the transport mechanisms, which are unlikely to change.

For more information about SignalR development, please check [SignalR documentation](https://docs.microsoft.com/en-us/aspnet/core/signalr/introduction?view=aspnetcore-2.1).

## Features

- Uses standard SignalR Core .NET client
- Allows multiple hub connections
- Supports all transports (Long polling, Side side events and websockets)
- Wide compatability (Automatic transport fallback ensure it works on all platforms)
- Supports authentication

## Install

Install the nuget package (or use the GUI in VS and search)
```
Install-Package BlazorSignalR
```

And then configure your connection creation like the following:

```
HubConnection connection = new HubConnectionBuilder().WithUrlBlazor("/chathub",
    options: opt => {
        opt.AccessTokenProvider = async () =>
        {
            return "some token for example";
        };
    }).Build();
```

Follow the [official docs](https://docs.microsoft.com/en-us/aspnet/core/signalr/dotnet-client?view=aspnetcore-2.1) for the .NET core client.

## Transports
**All transports work**

You will require browser support for [WebSocket](https://caniuse.com/#feat=websockets) and [EventSource](https://caniuse.com/#feat=eventsource) for those transports to be enabled. You can install polyfils if you wish, as otherwise signalr will fallback to long polling.

You can manually select what transports and the implementations to use via ```Transports``` & ```Implementations``` in the ```BlazorHttpConnectionOptions``` when configuring the builder.

- Long Polling (Implemented in C#)
- Server Side Events (Implemented in JS, C# implementation waiting on Blazor bug)
- Web Sockets (Implemented in JS, C# implementation waiting on mono support)

JS implemented means that the network requests are proxied to and from Javascript at a high level, whereas C# implemented means most of the processing occurs within the mono wasm runtime, with the low level networking being proxied back and forth. JS implementations should be faster as they use the underlying browser mechanisms.

## Issues

### JSON
Currently the default options in use by Blazor mean that [Json.NET](https://github.com/csnewman/BlazorSignalR) will not be able to encode/decode objects correctly. 

You need to use the example Linker config from the [Blazor Docs](https://blazor.net/docs/host-and-deploy/configure-linker.html) in the meantime.

## Alternatives

### [BlazorExtensions/SignalR](https://github.com/BlazorExtensions/SignalR)
Uses the typescript client, exposed to c# via a fake api that relays back to typescript. This has the benefit of being extreemly reliable and fast, however at the expense of each and every feature needing to be hand exposed, meaning the api does not perfectly reflect the .net api.

This package uses the test suite from the ```BlazorExtensions/SignalR``` package and was based on their work. Please do check it out!
