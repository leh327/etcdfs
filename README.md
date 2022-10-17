# etcdfs - A FUSE filesystem backed by etcd

[![Tests](https://github.com/polyrabbit/etcdfs/workflows/Tests/badge.svg)](https://github.com/polyrabbit/etcdfs/actions)
[![License: MIT](https://img.shields.io/badge/License-MIT-brightgreen.svg)](https://opensource.org/licenses/MIT)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](https://github.com/polyrabbit/my-token/pulls)
[![Go Report Card](https://goreportcard.com/badge/github.com/polyrabbit/etcdfs)](https://goreportcard.com/report/github.com/polyrabbit/etcdfs)

[blog: how to mount etcd as a filesystem](https://blog.betacat.io/post/2020/08/how-to-mount-etcd-as-a-filesystem/)

Tired of typing `etcdctl`? why not mount it to local filesystem and open in your favorite editors?

## Example

Wondering how Kubernetes organizes data in etcd? After mounting it locally, we can use [VS Code](https://code.visualstudio.com/) to get the whole picture:

![etcd-of-a-kubernetes](https://user-images.githubusercontent.com/2657334/89164446-b6b92e00-d5a9-11ea-93b9-218072ce73ff.png)

_Hint: steps to mount Kubernetes etcd locally:_

```bash
$ # scp etcd certificates to a local directory (keep them carefully)
$ scp -r <kubernetes-master-ip>:/etc/kubernetes/pki/etcd .
$ # mount to a local directory
$ etcdfs --endpoints=<kubernetes-master-ip>:2379 --cacert etcd/ca.crt --key etcd/server.key --cert etcd/server.crt mnt
$ # open it in VS code
$ code mnt
```

## Installation

#### Homebrew

```bash
# WIP
```

#### `curl | bash` style downloads to `/usr/local/bin`
```bash
$ curl -sfL https://raw.githubusercontent.com/polyrabbit/etcdfs/master/.godownloader.sh | bash -s -- -d -b /usr/local/bin
```

#### Using [Go](https://golang.org/)
```bash
snap install go (make sure the go version is 1.16 or later - 1.18 is the current stable version as of 2022/10/17)
$ go install github.com/polyrabbit/etcdfs@master
```

## Usage

```bash
$ etcdfs
Mount etcd to local file system - find help/update from https://github.com/leh327/etcdfs

Usage:
  etcdfs [mount-point] [flags]

Flags:
      --endpoints strings       etcd endpoints (default [127.0.0.1:2379])
      --dial-timeout duration   dial timeout for client connections (default 2s)
      --read-timeout duration   timeout for reading and writing to etcd (default 3s)
  -v, --verbose                 verbose output
      --enable-pprof            enable runtime profiling data via HTTP server. Address is at "http://localhost:9327/debug/pprof"
      --cert string             identify secure client using this TLS certificate file
      --key string              identify secure client using this TLS key file
      --cacert string           verify certificates of TLS-enabled secure servers using this CA bundle
      --mount-options strings   options are passed as -o string to fusermount (default [nonempty])
  -h, --help                    help for etcdfs
```

_Notice: `etcdfs` has a very similar CLI syntax to `etcdctl`._

## Limitations

* Etcdfs depends on the [FUSE](https://en.wikipedia.org/wiki/Filesystem_in_Userspace) kernel module which only supports Linux and macOS(?). It needs to be installed first:
    * Linux: `yum/apt-get install -y fuse`
    * macOS: install [OSXFUSE](https://osxfuse.github.io/)
* Keys in etcd should have a hierarchical structure to fit the filesystem tree model. And currently the only supported hierarchy separator is `/` (the same as *nix), more will be supported in the future. 
* Currently only etcd v3 is supported.

## Supported Operations

Most commonly used POSIX operations are supported:

* Readdir
* Lookup
* Getattr
* Open
* Read
* Write
* Create
* Flush
* Fsync
* Unlink
* Setattr

## TODO

- [x] ~~When building a directory, all keys belonging to that directory can be skipped~~
- [ ] Support hierarchy separators other than `/` in etcd
- [ ] Watch for file/directory changes

## Credits

 * Inspired by [rssfs](https://github.com/dertuxmalwieder/rssfs)
 * Based on [go-fuse](https://github.com/hanwen/go-fuse) binding

## License

The MIT License (MIT) - see [LICENSE.md](https://github.com/polyrabbit/etcdfs/blob/master/LICENSE) for more details
