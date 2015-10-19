# SwiftLogging

SwiftLogging

## How to access a logger

You can access the default global `Logger` instance via either the global
variable `SwiftLogging.log` or via the Logging class' `sharedInstance` property
`SwiftLogging.Logging.sharedInstance`.

## Logging

The simplest form of logging is to use the priority based convenence logging:


```swift
log.debug("Hello world")
log.error("This is just a test")
```

Anything that can be converted to a String can be logged:

```swift
log.debug(42)
log.debug(nil)
log.debug(UIApplication)
```

A slightly more verbose method allows you to pass the priority as a parameter:

```swift
log.log("Hello world", priority: .Info)
```

If you need more control over logging you can manually create an Event and log
that:

```swift
let event = Event(...)
log.log(event)
```

## Events

An `Event` captures the information about a single logging event. The following
properties are part of an `Event`:

* subject
* priority
* timestamp
* source
* tags
* userInfo

### Priority

* Debug (👷)
* Info (📰)
* Warning (🚧)
* Error (🚨)
* Critical (💣)

### Tags

A tag is a string used to provide extra metadata to a logging event. `Filters`,
`Destinations` and `Formatters` can all optionally use tags to decide how to
process the event.

You provide a set of tags to an event either via the `Event` init methods. The
convenience methods on `Logger` also take tags:

```
log.info("Hello", tags: ["example tag"])
```

The following tags are provided (and used by built-in formatters and filters):

* `preformattedTag`: Event should not be processed by destination's formatter
* `sensitiveTag`: Event may contain sensitive user information (password etc). Used by `sensitivityFilter`
* `verboseTag`: Event is considered verbose. Verbose events can be filtered out by the `verbosityFilter`
* `veryVerboseTag`: Event is considered very verbose. Verbose events can be filtered out by the `verbosityFilter`
* `flushTag`: Destinations that support flushing should perform a flush after processing this event.

### Source

A `Source` instance captures the file name, function name and line number of a
particularly invocation. The `Source` default init method will capture all three
properties based on the particularly line number it is called on. Generally it
is useful to pass use this as a default parameter to a function or method call.

## How to configure a logger

Loggers are not configured by default. You configure a logger by adding
`Destination` and `Filter` instances to it.

## `Destination` Instances

The following destinations are provided:

* `ConsoleDestination`: Log events to `stdout`
* `FileDestination`: Writes events to a file
* `MemoryDestination`: Stores the log event in an in-memory array.

## `Formatter` Instances

`Formatter` instances take an `Event` instance and transform it to a `String`.

The following formatters are provided:

* `preciseFormatter`: This formatter outputs all event properties.
* `terseFormatter`: This formatter outputs events like:

```
12:56:04.424 👷 [main.swift:20 main]: This is a tersely formatted event
```

## `Filter` instances

Each `Destination` instance and the blobal `Logger` instance can have zero or
more `Filter` instances added to them. `Filters` are simple closures that take
a single `Event` parameter and return an optional `Event`. `Filters` can modify
or throw away `Events` as needed.

The following filters are provided

* `nilFilter`: Drops all events passed into this filter.
* `passthroughFilter`: Returns all events passed into this filter without modification (identity filter)
* `tagFilterIn`: Returns only events that match provided tags.
* `tagFilterOut`: Returns only events that do not match provided tags.
* `priorityFilter`: Returns only events whose priority is in a provided set.
* `duplicatesFilter`: Filters out duplication events seen in the last user-defined time interval.
* `sensitivityFilter`: Filters out events marked as sensitive (see `sensitiveTag`).
* `verbosityFilter`: Filters out events marked as verbose (see `verbosityFilter`).
* `sourceFilter`:

### Using the `sensitivityFilter`:

TODO

### Using the `verbosityFilter`

TODO

### Using the `sourceFilter`

TODO

## Requirements

Swift 2.0 and higher.
The project provides iOS and OSX dynamic frameworks.

## Installation

Use [carthage](https://github.com/carthage/carthage).

## Logging Strategies.

Do not use the `Debug` priority in shipping code. If an event is important
enough to be logged in a shipping app the priority should be at least `Info`.

Logging at `Debug` level can be "banned" in the main release branches and either
flagged at code review time or automatically detected with something like a git
hook.

You generally want as little superflous data as possible logged to the main
developer console. You should create a `ConsoleDestination` and add the
`duplicatesFilter`, `verbosityFilter` and `sourceFilter` to it.

Marking log events as verbose and relying on the `verbosityFilter` to remove
them should be the primary method to "clean" the console log. The
`verbosityFilter` can be configured via command line flags (exposed as
`NSUserDefaults` settings).See the section on "Using the `verbosityFilter`".

If the `verbosityFilter` isn't working well enough you can use the
`sourceFilter` to limit logging to just events whose source matches a provided
regular expression. If your source file names or function names are well named
then this should provide a very effective filter. Like `verbosityFilter` this
filter can be configured via `NSUserDefaults`/command line switches.

## FAQ

### What about "modules" or "facilities"?

Syslog style "facilities" or the concept of logging "modules" used

### Threading

TODO

### Use in sub-Frameworks

TODO

## License

The MIT License
