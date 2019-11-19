Contributing to InfluxDB v2
========================

Bug reports
---------------
Before you file an issue, please search existing issues in case it has already been filed, or perhaps even fixed. If you file an issue, please include the following.
* Full details of your operating system (or distribution) e.g. 64-bit Ubuntu 14.04.
* The version of InfluxDB you are running
* Whether you installed it using a pre-built package, or built it from source.
* If applicable, a small test case or method to reproduce the issue described.

Remember the golden rule of bug reports: **The easier you make it for us to reproduce the problem, the faster it will get fixed.**
If you have never written a bug report before, or if you want to brush up on your bug reporting skills, we recommend reading [Simon Tatham's essay "How to Report Bugs Effectively."](http://www.chiark.greenend.org.uk/~sgtatham/bugs.html)

TEST CASES SECTION NEEDS REVISION based on v2 API

Test cases should be in the form of `curl` commands. For example:
```bash
# write data
curl -XPOST "http://localhost:9999/api/v2/write?org=YOUR_ORG&bucket=YOUR_BUCKET&precision=s" \
  --header "Authorization: Token YOURAUTHTOKEN" \
  --data-raw "mem,host=host1 used_percent=23.43234543 1556896326"

# query data
# Bug: expected it to return no data, but data comes back.
curl http://localhost:9999/api/v2/query?org=my-org -XPOST -sS \
  -H 'Authorization: Token YOURAUTHTOKEN' \
  -H 'Accept: application/csv' \
  -H 'Content-type: application/vnd.flux' \
  -d 'from(bucket:"example-bucket")
        |> range(start:-1000h)
        |> group(columns:["_measurement"], mode:"by")
        |> sum()'
```
**If you don't include a clear test case like this, your issue may not be investigated, and may even be closed**. If writing the data is too difficult, please zip up your data directory and include a link to it in your bug report.

Please note that issues are *not the place to file general questions* such as "how do I use collectd with InfluxDB?" Questions of this nature should be sent to the [InfluxData Community](https://community.influxdata.com/), not filed as issues. Issues like this will be closed.

Feature requests
---------------
We really like to receive feature requests as it helps us prioritize our work. Please be clear about your requirements, as incomplete feature requests may simply be closed if we don't understand what you would like to see added to InfluxDB.  If you find your feature request already exists as a Github issue please indicate your support for that feature by using the "thumbs up" reaction.

Contributing to the source code
---------------

InfluxDB follows standard Go project structure. This means that all your Go development is done in `$GOPATH/src`. GOPATH can be any directory under which InfluxDB and all its dependencies will be cloned. For full details on the project structure, follow along below.

You should also read our [coding guide](https://github.com/influxdata/influxdb/blob/master/CODING_GUIDELINES.md), to understand better how to write code for InfluxDB.

Submitting a pull request
------------
To submit a pull request you should fork the InfluxDB repository, and make your change on a feature branch of your fork. Then generate a pull request from your branch against *master* of the InfluxDB repository. Include in your pull request details of your change -- the why *and* the how -- as well as the testing your performed. Also, be sure to run the test suite with your change in place. Changes that cause tests to fail cannot be merged.

There will usually be some back and forth as we finalize the change, but once that completes it may be merged.

To assist in review for the PR, please add the following to your pull request comment:

```md
- [ ] CHANGELOG.md updated
- [ ] Rebased/mergable
- [ ] Tests pass
- [ ] Sign [CLA](https://influxdata.com/community/cla/) (if not already signed)
```

Security Vulnerability Reporting
---------------
InfluxData takes security and our users' trust very seriously. If you believe you have found a security issue in any of our
open source projects, please responsibly disclose it by contacting security@influxdata.com. More details about 
security vulnerability reporting, 
including our GPG key, [can be found here](https://www.influxdata.com/how-to-report-security-vulnerabilities/).


Signing the CLA
---------------

If you are going to be contributing back to InfluxDB please take a
second to sign our CLA, which can be found
[on our website](https://influxdata.com/community/cla/).

Installing Go
-------------
InfluxDB requires Go 1.12.

At InfluxData we find gvm, a Go version manager, useful for installing Go. For instructions
on how to install it see [the gvm page on github](https://github.com/moovweb/gvm).

After installing gvm you can install and set the default go version by
running the following:

    gvm install go1.11
    gvm use go1.12 --default

Revision Control Systems
-------------
Go has the ability to import remote packages via revision control systems with the `go get` command.  To ensure that you can retrieve any remote package, be sure to install the following rcs software to your system.
Currently the project only depends on `git` and `mercurial`.

* [Install Git](http://git-scm.com/book/en/Getting-Started-Installing-Git)
* [Install Mercurial](http://mercurial.selenic.com/wiki/Download)

Getting the source
------
Setup the project structure and fetch the repo like so:

```bash
    cd $GOPATH/src
    git clone git@github.com:influxdata/influxdb.git
    cd influxdb
    export  GO111MODULE=on
    go mod vendor
    go install ./...
    go test ./...
```

You can add the line `export GOPATH=$HOME/go` to your bash/zsh file to be set for every shell instead of having to manually run it everytime.

Cloning a fork
-------------
If you wish to work with fork of InfluxDB, your own fork for example, you must still follow the directory structure above. But instead of cloning the main repo, instead clone your fork. Follow the steps below to work with a fork:

```bash
    export GOPATH=$HOME/go
    mkdir -p $GOPATH/src/github.com/influxdata
    cd $GOPATH/src/github.com/influxdata
    git clone git@github.com:<username>/influxdb
```

Retaining the directory structure `$GOPATH/src/github.com/influxdata` is necessary so that Go imports work correctly.

Build and Test
-----

Make sure you have Go installed and the project structure as shown above. To then get the dependencies for the project, execute the following commands:

```bash
cd $GOPATH/src/github.com/influxdata/influxdb
make
```
The binaries will be located in `$GOPATH/bin`. Please note that the InfluxDB binary is named `influxd`, not `influxdb`.

To run the tests, execute the following command:

```bash
cd $GOPATH/src/github.com/influxdata/influxdb
go test -v ./...

# run tests that match some pattern
go test -run=TestDatabase . -v

# run tests and show coverage
go test -coverprofile /tmp/cover . && go tool cover -html /tmp/cover
```

To install go cover, run the following command:
```
go get golang.org/x/tools/cmd/cover
```

Generated Google Protobuf code
-----------------
Most changes to the source do not require that the generated protocol buffer code be changed. But if you need to modify the protocol buffer code, you'll first need to install the protocol buffers toolchain.

First install the [protocol buffer compiler](https://developers.google.com/protocol-buffers/
) 2.6.1 or later for your OS:

Then install the go plugins:

```bash
go get github.com/gogo/protobuf/proto
go get github.com/gogo/protobuf/protoc-gen-gogo
go get github.com/gogo/protobuf/gogoproto
```

Finally run, `go generate` after updating any `*.proto` file:

```bash
go generate ./...
```
**Troubleshooting**

If generating the protobuf code is failing for you, check each of the following:
* Ensure the protobuf library can be found. Make sure that `LD_LIBRARY_PATH` includes the directory in which the library `libprotoc.so` has been installed.
* Ensure the command `protoc-gen-gogo`, found in `GOPATH/bin`, is on your path. This can be done by adding `GOPATH/bin` to `PATH`.


Generated Go Templates
----------------------

The query engine requires optimized data structures for each data type so
instead of writing each implementation several times we use templates. _Do not
change code that ends in a `.gen.go` extension!_ Instead you must edit the
`.gen.go.tmpl` file that was used to generate it.

Once you've edited the template file, you'll need the [`tmpl`][tmpl] utility
to generate the code:

```sh
$ go get github.com/benbjohnson/tmpl
```

Then you can regenerate all templates in the project:

```sh
$ go generate ./...
```

[tmpl]: https://github.com/benbjohnson/tmpl

Profiling
-----
When troubleshooting problems with CPU or memory the Go toolchain can be helpful. You can start InfluxDB with CPU and memory profiling turned on. For example:

```sh
# start influx with profiling
./influxd -cpuprofile influxdcpu.prof -memprof influxdmem.prof
# run queries, writes, whatever you're testing
# Quit out of influxd and influxd.prof will then be written.
# open up pprof to examine the profiling data.
go tool pprof ./influxd influxd.prof
# once inside run "web", opens up browser with the CPU graph
# can also run "web <function name>" to zoom in. Or "list <function name>" to see specific lines
```
Note that when you pass the binary to `go tool pprof` *you must specify the path to the binary*.

If you are profiling benchmarks built with the `testing` package, you may wish
to use the [`github.com/pkg/profile`](github.com/pkg/profile) package to limit
the code being profiled:

```go
func BenchmarkSomething(b *testing.B) {
  // do something intensive like fill database with data...
  defer profile.Start(profile.ProfilePath("/tmp"), profile.MemProfile).Stop()
  // do something that you want to profile...
}
```