---
title: "Getting the AWS X-Ray Linux deamon to work with Alpine Linux"
layout: post
tags: [devops,aws]
---

[X-Ray](https://aws.amazon.com/xray/) is a managed distributed tracing service by Amazon Web Services. Your application (whether it's a single API or made up of multiple distributed microservices) collects data (such as timing and metrics for business logic, SQL queries and calls to AWS services) whilst processing incoming requests and then sends this data to a local deamon over UDP which in turn forwards the data onto the X-Ray service.

I've been experimenting with bundling the [X-Ray deamon](http://docs.aws.amazon.com/xray/latest/devguide/xray-daemon.html) into an [Alpine Linux](https://alpinelinux.org/) container alongside some other binaries however out of the box it didn't work. Attempting to execute the `xray` binary would result in a confusing error message like this:

> `sh: /xray: not found`

After some Googling I discovered the `ldd` command which is used to find the shared libraries that a binary calls.

Executing `ldd xray` I get the following output:

```
/ # ldd xray
/lib64/ld-linux-x86-64.so.2 (0x55a247c9b000)
libpthread.so.0 => /lib64/ld-linux-x86-64.so.2 (0x55a247c9b000)
libc.so.6 => /lib64/ld-linux-x86-64.so.2 (0x55a247c9b000)
```

This first library that the X-Ray binary attempts to call is `/lib64/ld-linux-x86-64.so.2`.

If I list the contents of `/` I can see that the `/lib64` directory isn't present out of the box with Alpine:

```
/ # ls
bin    dev    etc    home   lib    media  mnt    proc   root   run    sbin   srv    sys    tmp    usr    var
```

My understanding is that this is because Alpine Linux uses _musl libc_ instead of _GNU libc_ and the `xray` binary is linked against _GNU libc_ libraries.

Alpine provides a package called [libc6-compat](https://pkgs.alpinelinux.org/package/edge/main/x86/libc6-compat) which provides compatibility libraries for glibc as well as symlinks `/lib64` to `/lib`. This can be installed with the command `apk add libc6-compat`.

After installing `libc6-compat` the X-Ray Daemon now runs successfully.
