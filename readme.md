# CruftFlake

[![Build Status](https://api.travis-ci.org/lucasvscn/cruftflake.png)](https://travis-ci.org/lucasvscn/cruftflake)
[![ProjectStatus](http://stillmaintained.com/lucasvscn/cruftflake.png)](http://stillmaintained.com/lucasvscn/cruftflake)
[![FOSSA Status](https://app.fossa.io/api/projects/git%2Bgithub.com%2FHartmarken%2Fcruftflake.svg?type=shield)](https://app.fossa.io/projects/git%2Bgithub.com%2FHartmarken%2Fcruftflake?ref=badge_shield)

A stab at a version of [Twitter Snowflake](https://github.com/twitter/snowflake)
but in PHP with a simple ZeroMQ interface (rather than Thrift).

## Implementation

This project was motivated by personal curiosity and also my [inability to
get Twitter's project to build](https://github.com/twitter/snowflake/issues/8).

The implementation copies Twitter - generating 64 bit IDs.

  - time - 41 bits
  - configured machine ID - 10 bits
  - sequence number - 12 bits

Has a custom epoch that means it can generate IDs until 2081-09-06 (not the
same epoch as Snowflake).

### ZooKeeper for config coordination (optional)

Each machine **must** have a unique 10 bit machine ID. ZooKeeper can be used
to keep a list of all registered machine IDs, allowing new machines to
self-assign a new unique machine ID.

When using ZooKeeper, when a new node starts up for the first time it
**must** be able to contact the ZooKeeper cluster and create a new node.
It will look at all the existing nodes and then (if it can't find its
own Mac Address) attempt to claim a free one.

I was using Ephemeral nodes for this - similar(ish) to a lock pattern but this
had the issue that the node needed to remain connected to ZK throughout its
lifetime -- this way it doesn't.

The downside is that potentially the 1024 possible machine IDs will "fill up"
and need to be manually pruned.

## Running

Git clone and then execute `composer update` to install the dependencies. You should run the tests
to verify things are OK:

    vendor/bin/phpunit

There are two scripts provided for playing about with.

1. The generator (the server)

    ./examples/cruftflake.php

Or to specify a manually configured machine ID:

    ./examples/cruftflake.php -m 1

2. A client that will generate N IDs and dump to STDOUT

    ./examples/client.php -n 100


## License
[![FOSSA Status](https://app.fossa.io/api/projects/git%2Bgithub.com%2FHartmarken%2Fcruftflake.svg?type=large)](https://app.fossa.io/projects/git%2Bgithub.com%2FHartmarken%2Fcruftflake?ref=badge_large)