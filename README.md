![Logy logo](https://user-images.githubusercontent.com/5354910/224152840-2c913efa-f7c3-41ea-b0cc-a215a7ec02cf.png)

# Logy

The Logy package provides a fast and simple logger.

## Installation

```bash
go get -u github.com/procyon-projects/logy
```

## Logging Format

By default, Logy uses a pattern-based logging format.

You can customize the format for each log handler using a dedicated configuration property.
For the console handler, the property is `logy.console.format`.

The following table shows the logging format string symbols that you can use to configure the format of the log
messages.

Supported logging format symbols:

| Symbol              |              Summary               |                                                Description                                                 |
|:--------------------|:----------------------------------:|:----------------------------------------------------------------------------------------------------------:|
| `%%`                |                `%`                 |                                           A simple `%%`character                                           |
| `%c`                |            Logger name             |                                              The logger name                                               |
| `%C`                |            Package name            |                                              The package name                                              |
| `%d{layout}`        |                date                |                                     Date with the given layout string                                      |
| `%e`                |               Error                |                                           The error stack trace                                            |
| `%F`                |            Source file             |                                            The source file name                                            |
| `%i`                |             Process ID             |                                          The current process PID                                           |
| `%l`                |          Source location           |                          The source location(file name, line number, method name)                          |
| `%L`                |            Source line             |                                           The source line number                                           |
| `%m`                |            Full Message            |                                   The log message including error trace                                    |
| `%M`                |           Source method            |                                           The source method name                                           |
| `%n`                |              Newline               |                                         The line separator string                                          |
| `%p`                |               Level                |                                      The logging level of the message                                      |
| `%s`                |           Simple message           |                                    The log message without error trace                                     |
| `%X{property-name}` |        Mapped Context Value        |                        The value from Mapped Context  `property-key=property-value`                        |
| `%x{property-name}` |  Mapped Context Value without key  |                   The value without key from Mapped Context  in format `property-value`                    |
| `%X`                |       Mapped Context Values        | All the values from Mapped Context in format `property-key1=property-value1,property-key2=property-value2` |
| `%x`                | Mapped Context Values without keys |        All the values without keys from Mapped Context in format `property-value1,property-value2`         |

## Logging Levels

You can use logging levels to categorize logs by severity.

Supported logging levels:
* ERROR
* WARN
* INFO
* DEBUG
* TRACE

## Log Handlers

Logy comes with three different log handlers: **console**, **file** and **syslog**.

### Console Log Handler

The console log handler is enabled by default.

### File Log Handler

The file log handler is disabled by default.

### Syslog Log Handler

The syslog log handler is disabled by default.

## Colorize Logs

If your terminal supports ANSI, the color output will be used to aid readability.
You can set `logy.console.color` to `true`.


### Example logging yaml configuration
Here is an example of how you 
```yaml
logy:
  level: INFO
  include-caller: true
  handlers:
    - console

  console:
    enabled: true
    target: stderr
    format: "%d{2006-01-02 15:04:05.000} %l [%x{traceId},%x{spanId}] %p : %s%e%n"
    color: false
    level: DEBUG
    json:
      key-overrides:
        timestamp: "@timestamp"
      additional-fields:
        application-name:
          value: test-app
```

## Performance

Log a message without context fields

| Package                 |    Time     | Objects Allocated |
|:------------------------|:-----------:|:-----------------:|
| :star: logy             | 27.99 ns/op |    0 allocs/op    |
| :star: logy(formatting) | 883.8 ns/op |    7 allocs/op    |
| :zap: exp/slog          | 38.08 ns/op |    0 allocs/op    |
| zerolog                 | 37.49 ns/op |    0 allocs/op    |
| zerolog(formatting)     | 3030 ns/op  |   108 allocs/op   |
| zap                     | 98.30 ns/op |    0 allocs/op    |
| zap sugar               | 110.9 ns/op |    1 allocs/op    |
| zap sugar (formatting)  | 3369 ns/op  |   108 allocs/op   |
| go-kit                  | 248.5 ns/op |    9 allocs/op    |
| log15                   | 2490 ns/op  |   20 allocs/op    |
| apex/log                | 1139 ns/op  |    6 allocs/op    |
| logrus                  | 1831 ns/op  |   23 allocs/op    |

Log a message with a logger that already has 10 fields of context:

| Package                 |     Time     | Objects Allocated |
|:------------------------|:------------:|:-----------------:|
| :star: logy             | 61.43 ns/op  |    0 allocs/op    |
| :star: logy(formatting) | 1208.0 ns/op |    7 allocs/op    |
| :zap: exp/slog          | 266.3 ns/op  |    0 allocs/op    |
| zerolog                 | 44.84 ns/op  |    0 allocs/op    |
| zerolog(formatting)     | 3103.0 ns/op |   108 allocs/op   |
| zap                     | 92.50 ns/op  |    0 allocs/op    |
| zap sugar               | 113.7 ns/op  |    1 allocs/op    |
| zap sugar (formatting)  |  3355 ns/op  |   108 allocs/op   |
| go-kit                  |  3628 ns/op  |   66 allocs/op    |
| log15                   | 12532 ns/op  |   130 allocs/op   |
| apex/log                | 14494 ns/op  |   53 allocs/op    |
| logrus                  | 16246 ns/op  |   68 allocs/op    |
