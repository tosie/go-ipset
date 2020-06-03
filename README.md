# go-ipset #

This library is a simple GoLang wrapper to the IPtables ipset userspace utility.
It provides an interface to allow Go programs to easily manipulate ipsets.
It is currently limited to sets of `type hash`.

This repository is based off https://github.com/aporeto-inc/go-ipset (go-mod branch).

For ipset command documentation: http://ipset.netfilter.org/ipset.man.html

go-ipset requires ipset kernel module and userspace utility version 6.0 or greater.

## Installation ##

Install go-ipset using the "go get" command:

    go get github.com/tosie/go-ipset/ipset

Install dependencies:

    go get github.com/sirupsen/logrus
    go get github.com/coreos/go-semver/semver

## API Reference ##

[![GoDoc](https://godoc.org/github.com/google/go-github/github?status.svg)](https://godoc.org/github.com/janeczku/go-ipset/ipset)

## Usage ##

```go
import "github.com/tosie/go-ipset/ipset"
```

#### Create a New Set

Construct a new IPset instance (creating the set on the fly), then use the various methods to manipulate the IPset.
For example, to create a new ipset "customers" of type `hash:ip` for storing plain IPv4 addresses:

```go
customers := ipset.New("customers", "hash:ip", &ipset.Params{})
```

To create a new ipset to store different sized IPv4 network addresses (with /mask).

```go
trustedNetworks := ipset.New("trusted-networks", "hash:net", &ipset.Params{})
```

#### Add a Single Entry to the Set

```go
customers.Add("8.8.2.2")
```

#### Populate the Set With IPv4 Addresses (Overwriting the Previous Content)

```go
ips := []string{"8.8.8.8", "8.8.4.4"}
customers.Refresh(ips)
```

#### Remove a Single Entry From that Set:

```go
customers.Del("8.8.8.8")
```

#### Configure Advanced Set Options

You can configure advanced options when creating a new set by supplying the parameters in the `ipset.Params` struct.

```go
type Params struct {
  HashFamily string
  HashSize   int
  MaxElem    int
  Timeout    int
}
```
See http://ipset.netfilter.org/ipset.man.html for their meaning.

For example, to create a set whose entries will expire after 60 seconds, lets say for temporarily limiting abusive clients:

```go
abusers := ipset.New("ratelimited", "hash:ip", &ipset.Params{Timeout: 60})
```

#### List Entries of a Set
```go
// list is []string
list := customers.List("customers")
```

#### List all Sets

```go
// list is []IPSet
list := ipset.ListAll()
```
