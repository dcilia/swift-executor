[![Build Status](https://travis-ci.org/dcilia/swift-executor.svg?branch=master)](https://travis-ci.org/dcilia/swift-executor)

# swift-executor
A lightweight simple implementation of ```Foundation``` framework ```Operation```

# Introduction
Leverages ``` Operation ``` & ``` OperationQueue ```.  ``` AsyncOperation ``` is a ```concurrent operation``` that generates the appropriate ```KVO``` notifications as required by a concurrent ```Operation``` subclass.

# Additional Reading:
[Apple Concurrency Programming Guide](https://developer.apple.com/library/content/documentation/General/Conceptual/ConcurrencyProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40008091-CH1-SW1)

[Apple Operation Class Reference](https://developer.apple.com/reference/foundation/operation)


# Supported Platforms:
```macOS``` ```iOS``` ```tvOS``` ```watchOS``` ```linux```

# Minimum OS Versions Supported:
``` macOS 10.10 ``` ``` iOS 9.0 ``` ``` tvOS 9.0 ``` ``` watchOS 2.0 ``` ``` linux ```

# Installation:

You can use ```Xcode 11``` and manage packages that way to use Swift SPM.

or...

***Swift Package Manager:***

add to your ```Package.swift```:

```
dependencies: [
   .Package(url: "https://github.com/silverfoxlabs
   /swift-executor", majorVersion: 1, minor: 1)
])
```
***Carthage:***

Add to your ```Cartfile```

```
github "silverfoxlabs/swift-executor"
```


***CocoaPods***

in your ```Podfile```:

```pod 'swift-executor', :git=> 'https://github.com/silverfoxlabs/swift-executor'```

# How to use:

The main class to use is ```AsyncOperation```:

``` Swift
class Foo : AsyncOperation {
    
    var bar: String = "Hello World"
    
    override func execute() {
        
        //Calling super is a no-op.
        
        //Some long running task (or not).
        bar = String(bar.characters.reversed())
        
        //Call finish when done
        finish()
    }
}

```

then ...

``` Swift
var p = Foo(identifier: "com.your.bundle.id")
p.completionBlock = {
    print("I am a completion block")
}
```

create an observer for your ```AsyncOperation``` ...

``` swift
struct FooObserver : ExecutorObserver {
    
    func did(start operation: AsyncOperation) {
        dump(self)
        print(#function)
    }
    
    func did(cancel operation: AsyncOperation) {
        dump(self)
        print(#function)
    }
    
    func did(finish operation: AsyncOperation) {
        dump(self)
        print(#function)
    }
    
    func did(becomeReady operation: AsyncOperation) {
        dump(self)
        print(#function)
    }
}
```

``` swift
let observer = FooObserver()
p.add(observer: observer)
```
```  swift
let first = FooObserver() //observer that is a class
let second = Barz() //observer that is a struct
p.add(observer: observer)
p.add(observer: two)
```
You can continue to use the normal properties of ```Operation``` to manage state:
```isReady``` ```isExecuting``` ```isCancelled```

you can also use the default properties of ```Operation```

Lastly, to finally execute your operation simply follow the accepted pattern of ```Operation``` & ```OperationQueue```

``` Swift
let queue = OperationQueue()
queue.name = "com.someQueue"
queue.addOperation(operation)
```

## New

Some changes and enhacements:

```AsyncOperation``` now has a ```run``` property.  This can be used as is or part of the custom subclass.  Here is an example:

``` swift
var op = AsyncOperation(identifier: "com.executorKit.bar",readyToExecute: true)

op.run = {
    print("Hello World")
}
op.add(observer: someObserver)

//Use the "start" or add to an OperationQueue
var q = OperationQueue()
q.name = "myqueue"
q.addOperation(op)

```

```AsyncOperation``` also has closures that can be used for state notification instead of using an observer:

``` swift

op.didStart = {
    print("did start")
}

op.didFinish = {

    //do something here
}

```

Be aware of memory management semantics when using these closures!
   
