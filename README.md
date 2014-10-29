beanstalk-multiplex
===================

multiplex a beanstalk tube into one or more other tubes

## Installation

    $ sudo gem install beaneater
    $ git clone https://github.com/martint17r/beanstalk-multiplex

Put beanstalk-multiplex into a directory in your path, for example /usr/local/bin.


## Usage

```
$ ./beanstalk-multiplex --help
Usage: beanstalk-multiplex [options]
    -h, --host HOST:PORT             beanstalk server to connect to (defaults to localhost:11300)
    -s, --sourcetube NAME            source tube name
    -c, --controltube NAME           control tube name (defaults to "mux-control")
    -e, --expiry seconds             time to live for target tubes (defaults to 3600)
    -d, --debug                      spit out debug infos
```

## Control Protocol

This ruby script listens to the beanstalk tubes specified with `sourcetube` and `controltube`.

If a job is received on the controltube, its body is interpreted as a tubename and all jobs received in the next `expiry` seconds are sent to it.

## Keeping client tubes alive

If you need to keep a tube alive, just use the provided script

    $ export EXPIRY=5
    $ while true; do ./beanstalk-mux-injector -b 'my-foo' mux-control; sleep $EXPIRY; done
    
Choose the EXPIRY Environment variable to be less than the expiry for beanstalk-multiplex

## Bugs & Caveats

   * `beanstalk-multiplex` currently accepts exactly one server to connect to
   * retries and release_delay are not configurable through the command line
   * there is no automatic installation script

## Testcases

### Test --saveonexit

```
./beanstalk-multiplex -s foo --saveonexit -d
./beanstalk-mux-injector mux-control -b foo_en;./beanstalk-mux-injector mux-control -b foo_de
```

interrupt beanstalk-multiplex and start it again - it should process 2 jobs from mux-control for fox_de & fox_en

