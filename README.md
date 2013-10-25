## node-eiscp

This is a node module to control and interact with Onkyo receivers over the network.

Basically it is a merge of my previous module which was really simple and https://github.com/miracle2k/onkyo-eiscp translated to javascript, which means a lot more features but some stuff still missing like handling multiple receivers and special commands (ranges are supported though).

### How to use it?

#### See the examples
https://github.com/tillbaks/node-eiscp/master/examples

### Command syntax

Same syntax as https://github.com/miracle2k/onkyo-eiscp thanks dude!

See example 3.js to retreive an array of all available commands

A command consists of three parts: The zone, the command, and the arguments.
Here are some examples::

    system-power=on
    zone2.power=on
    main.balance=3

As you can see, the basic format is::

    zone.command=argument

If you do not specify a zone, then ``main`` is assumed.

There are some variations on this syntax that are possible, for example the
following are all equivalent::

    power on
    power:on
    main.power on
    main power on

In other words, instead of the ``.`` and ``=`` separators, whitespace may
be used, and the colon ``:`` is an alternative to ``=``. However, it's best
to use the suggested syntax above.

### API

#### connect ([ options ])

If you only have one receiver on your network there is no need to provide any options, it will be discovered automatically.

**options:**

- `host` _(default: undefined)_ - Hostname or IP of receiver
- `port` _(default: 60128)_ - Port of receiver
- `reconnect` _(default: true)_ - Reconnect to receiver if connection is lost
- `reconnect_sleep` _(default: 5)_ - Time in seconds between reconnection attempts

#### discover (options, callback)

Sends a broadcast packet and waits for response

**options:**

- `address` _(default: "255.255.255.255")_ - Hostname or IP of receiver
- `port` _(default: 60128)_ - Listening port **(NOTE: try changing this if you have trouble connecting)**
- `timeout` _(default: 2)_ - Time in seconds to wait for respoonses after broadcast is sent

**callback:**

You will receive one argument cointaining an array of devices

- `devices` _(array)_ - An array of objects
  - `host` - Receiver IP
  - `port` - Receiver Port
  - `model` - Receiver Model
  - `areacode` - Area Code?
  - `message` - Raw message that was received


#### raw (data [, callback])

Sends a low-level command like _PWR01_ or _MVL1A_

**data:** _(string)_ - Command to send

**callback:**

NOTE: Callback does not tell you if the command was successfull only that it was sent to the receiver.

- `result` _(object)_
  - `result` - `true` or `false` if command was sent to receiver
  - `msg` - If there is a message attached to the result


#### command (command [, callback])

Sends a high-level command like **system-power=query** or **zone2.volume=1A**
more info about command syntax can be found above the API

**command:** _(string)_ - Command to send

**callback:**

NOTE: Callback does not tell you if the command was successfull only that it was sent to the receiver.

- `result` _(object)_
  - `result` - _true_ or _false_ if command was sent to receiver
  - `msg` - If there is a message attached to the result


#### get_commands (zone, callback)

Retreives an array of all commands in the provided zone

**zone:** _(string)_ - zone can be "main", "zone2" etc..

**callback:**

- `commands` _(array)_ - Array of all commands


#### get_command (command, callback)

Retreives an array of all arguments in the provided command

**command:** _(string)_ - command can be "system-power", "zone2.volume" etc.. (NODE: If you don't specify zone "main" will be assumed)

**callback:**

- `arguments` _(array)_ - Array of all arguments


### Info about yaml/json commands stuff

All commands are in a json file "eiscp-commands.json" and are converted from a YAML file from https://github.com/miracle2k/onkyo-eiscp thanks dude!

###### Download YAML file:
```
wget https://raw.github.com/miracle2k/onkyo-eiscp/master/eiscp-commands.yaml
```

###### Convert YAML file to json:
Run in node-eiscp directory. This will create "eiscp-commands.json". Warning this script might be badly coded. [:
```
node eiscp-commands-convert.js
```