# Guido Configuration Files

## Guido Agent Operation
Guido Agents check configuration files for changes every 30 seconds

The configuration files contain rules that are used to match against classes that are found in the instrumented code.  

The default is action for all classes is to not log anything.  In order to log something you must explicitly specify logging __on__ in a rule that matches a class.  Specifying logging off for any class or interface will take priority over switching it on, regardless of the order it is specified in the configuration file.

If a class matches more than one rule, then logging is disabled if any of them is set to __off__.  Furthermore the threshold value is set to the value specified in the last rule found in the file.

The rules contain:

1. A __class matching__ or __interface matching__ rule
* An optional __host matching__ rule
* An __on__ or __off__ setting to turn on or off logging
* An optional __threshold__ setting to specify in milliseconds the duration of the method call above which to log information.  The default threshold if none is specified is 0.5 milliseconds.


## Configuration File Format

___configuration_line___ := _classes_to_instrument_ [ __@__ _ant_host_pattern_ ] __=__ ( __on__ | __off__ ) [ __threshold:__ _min_milliseconds_before_loging_ ]

___classes_to_instrument___ := ( _ant_class_pattern_ | __\[__ _interface_class_pattern_ __]__ )

___ant_class_pattern___ := ___interface_class_pattern___ := ( _part_of_class_name_ | __*__ | __**__ | __.__ ) [ _ant_class_pattern_ ] ... 

___ant_host_pattern___ := ( _part_of_host_name_ | __*__ | __**__ | __.__ ) [ _ant_host_pattern_ ] ... 


## Examples

__Log Everything__
To log everything that takes more than 10 seconds on any host
```
**=on,threshold:10000
```

__Log All calls to a package__
To log all calls to classes in the `com.example.mypackage`
```
com.example.mypackage.*=on,threshold:0
```

__Off takes precedence__
For all host names starting with __prod__ and the following rule
```
**@prod**=off
**@prod-bpm**=on,threshold:0
```
Logging will be disabled regardless of subsequent rules

__Order is important__
The order of rules is important for threshold values.  The rules are read in order and last threshold
value for any matching rule is set

For class `com.hazelcast.FooBar` and the following rules
```
**@sa-**=on,threshold:1000
**@sa-bpm**=on,threshold:0
**.hazelcast.**@sa-**=on,threshold:10
```
The threshold will be set to 10 milliseconds because of the last matching rule
