# EventHubFramework
![Last Version : 1.1.0](https://img.shields.io/badge/release-1.1.0-green) 
![Last Coomit : June 2023](https://img.shields.io/badge/last_commit-june_2023-blue) 
![License](https://img.shields.io/badge/License-MIT-green)
## Introduction
The EventBus repository is an implementation of an event bus for managing and distributing events within an application. It provides functionality for subscribing to and unsubscribing from events, as well as firing events and registering new event signals. The EventBus class is a static class, meaning it can be accessed globally without the need for an instance.
## Table of Contents
- [Installation](#installation)
    - [.NET CLI](#installcli)
    - [NuGet Package Referense](#installnugetpackage)
    - [PackageReference (MSBuild)](#installmsbuild)
- [Usage](#usage)
    - [Registering Event Signals](#registering)
    - [Subscribing to Events](#subscribing)
      - [Subcribing To Sync Events](#subscribingsync)
      - [Subscribing To Async Events](#subscribingasync)
    - [Unsubscribing from Events](#unsubscribing)
    - [Firing](#firing)
    - [Firing Events with Arguments](#firingwitharguments)
    - [Async Firing](#asyncfiring)
- [Examples](#examples)
- [Contributing](#contributing)

# <a id="installation"/> Installation
## <a id="installcli"/> Using .NET CLI
To install the EventHubFramework NuGet package using the .NET CLI, open the command-line interface and execute the following command:
```c#
dotnet add package EventHubFramework --version 1.0.0
```
This command will add the JsContainer package with the specified version to your project.
## <a id="installnugetpackage"/> Using NuGet Package Manager Console
If you prefer using the NuGet Package Manager Console, you can execute the following command:
```c#
Install-Package JsContainer -Version 1.0.0
```
Running this command will install the JsContainer package with the specified version into your project.
## <a id="installmsbuild"/> Using PackageReference (MSBuild)
For projects that use MSBuild and PackageReference format, you can add the following line to your project file:
```c#
<PackageReference Include="EventHubFramework" Version="1.0.0" />
```
This configuration will ensure that the JsContainer package with the specified version is installed in your project.

# <a id="usage"/> Usage
## <a id="registering"> Registering Event Signals
You can register new event signals dynamically using the `RegisterSignal<TSignal>` method. This method takes a type parameter `TSignal`, which represents the event signal to be registered.
```c#
EventBus.RegisterSignal<MyEventSignal>();
```
After registering an event signal, you can subscribe, unsubscribe, and fire events using that signal.
## <a id="subscribing"> Subscribing to Events
### <a id="subscribingsync"/> Subcribing To Synchronous Events
To subscribe to an event, you can use the `Subscribe<TSignal>` method. This method takes a type parameter `TSignal`, which represents the event signal you want to subscribe to. The event signal should be a class or interface that defines the structure of the event.
```c#
EventBus.Subscribe<MyEventSignal>(this, HandleMyEvent);
// Or
this.Subscribe<MyEventSignal>(HandleMyEvent);
```
The HandleMyEvent method is the event handler that will be called when the event is fired. It should have a compatible signature with the event signal.
### <a id="subscribingasync"/> Subscribing to Asynchronous Signals
To subscribe to an asynchronous signal, you can use the Subscribe<TSignal> method in the following format:
```c#
this.Subscribe<TestSignal>(TestSubscribe);
```
Here, TestSubscribe is an asynchronous method that returns a Task. It should have a compatible signature with the event signal TestSignal.

## <a id="unsubscribing"> Unsubscribing from Events
**Please note that unsubscribing from signals is crucial**. To do this, implement the `IDisposable` interface and unsubscribe from all the signals to which the object is subscribed in its `Dispose` method.
```c#
public class BarReceiver : IDisposable
{
    public BarReceiver()
    {
        this.Subscribe<TestSignal>(TestSubscribe);
    }

    public void TestSubscribe(TestSignal args)
    {
        Console.WriteLine(args.Float);
    }

    public void Dispose()
    {
        this.UnSubscribe<TestSignal>(TestSubscribe);
    }
}

// OR if Async Signal

public class MyClass : IDisposable
{
    public MyClass()
    {
        this.Subscribe<TestSignal>(TestSubscribe);
    }

    public void Dispose()
    {
        this.Unsubscribe<TestSignal>(TestSubscribe);
    }

    private async Task TestSubscribe(TestSignal signal)
    {
        // Handle the event asynchronously
        await Task.Delay(1000);
        Console.WriteLine("TestSignal handled asynchronously");
    }
}

```

To unsubscribe from an event, you can use the `UnSubscribe<TSignal>` method. This method takes the same type parameter `TSignal` as the Subscribe method.
```c#
EventBus.Unsubscribe<MyEventSignal>(this, HandleMyEvent);
// Or
this.UnSubscribe<MyEventSignal>(HandleMyEvent);
```
## <a id="firing"> Firing Events
To fire an event, you can use the Fire<TSignal> method. This method takes a type parameter TSignal, representing the event signal to be fired.
```c#
EventBus.Fire<MyEventSignal>();
// Or, use TryFire if you do not want to receive an exception in case it occurs.
// Please note that if an exception occurs and you are not in Debug Mode, you will not be able to know about it.
EventBus.TryFire<MyEventSignal>()
```
All the event handlers that have subscribed to the specified event signal will be called.
## <a id="firingwitharguments"/> Firing Events with Arguments
To fire an event with arguments, you can use the `Fire<TSignal>` or `TryFire<TSignal>` method variants that accept a `Func<TSignal>` parameter. This function should return an instance of the event signal with the desired argument values.
```c#
EventBus.Fire<MyEventSignal>(() => new MyEventSignal {Arg1 = 10, Arg2 = string.Empty});
```

## <a id="asyncfiring"/> Async Firing
To fire an asynchronous signal, you can use the FireAsync method. It can accept an instance of the event signal TSignal or no arguments. The method returns a Task that represents the asynchronous operation.
```c#
await EventBus.FireAsync<TestSignal>();
// Or
EventBus.FireAsync<TestSignal>();
// If you dont want awaiting signal
```
You can also pass an instance of the event signal as a parameter:
```c#
await EventBus.FireAsync(new TestSignal(arg1, arg2));
// Or
EventBus.FireAsync(new TestSignal(arg1, arg2));
// If you dont want awaiting signal
```

# <a id="examples"/> Examples
Here are some examples of how you can use the EventBus:
```c#
// Subscribe to an event
EventBus.Subscribe<MyEventSignal>(this, HandleMyEvent);
// Or
this.Subscribe<MyEventSignal>(HandleMyEvent);

// Unsubscribe from an event
EventBus.UnSubscribe<MyEventSignal>(this, HandleMyEvent);
// Or
this.UnSubscribe<MyEventSignal>(HandleMyEvent);

// Register a new event signal
EventBus.RegisterSignal<MyEventSignal>();

// Fire an event
EventBus.Fire<MyEventSignal>();
// Or
EventBus.TryFire<MyEventSignal>();

// Fire an event with arguments
EventBus.Fire<MyEventSignal>(() => new MyEventSignal {Arg1 = 10};
// Or
EventBus.TryFire<MyEventSignal>(() => new MyEventSignal {Arg1 = 10};
```
**Async Example**
```c#
public class MyClass : IDisposable
{
    public MyClass()
    {
        this.Subscribe<TestSignal>(TestSubscribe);
    }

    public void Dispose()
    {
        this.Unsubscribe<TestSignal>(TestSubscribe);
    }

    private async Task TestSubscribe(TestSignal signal)
    {
        // Handle the event asynchronously
        await Task.Delay(1000);
        Console.WriteLine("TestSignal handled asynchronously");
    }
}
```
In the above example, MyClass subscribes to the TestSignal event with the asynchronous method TestSubscribe. When the event is fired, TestSubscribe is invoked asynchronously to handle the event.
Remember to implement the IDisposable interface if you need to unsubscribe from signals when the object is disposed to avoid memory leaks.
That's it! You can now leverage the power of asynchronous signals in the EventBus repository to handle events asynchronously in your application.

# <a id="contributing"> Contributing
Contributions to the EventBus repository are welcome! If you find any issues or have suggestions for improvements, please open an issue or submit a pull request on the GitHub repository.
