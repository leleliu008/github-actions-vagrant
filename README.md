# Run GitHub CI in VirtualBox via Vagrant

Use this action to run your CI in `FreeBSD`, `OpenBSD`, `NetBSD`, etc.

# Sample workflow

```yml
name: Build
on: [push]
jobs:
  build-on-openbsd:
    runs-on: macos-latest
    steps:
      - uses: actions/checkout@v2

      - uses: leleliu008/github-actions-vagrant@v1
        with:
          mem: 2048
          box: generic/openbsd6
          run: |
            export AUTOCONF_VERSION=2.69
            export AUTOMAKE_VERSION=1.16
              
            export CFLAGS='-I/usr/local/include -L/usr/local/lib'
              
            if [ ! -f /usr/local/lib/libiconv.so ] ; then
                sudo ln -s /usr/local/lib/libiconv.so.* /usr/local/lib/libiconv.so
            fi
              
            run() {
                printf "\033[0;35m==>\033[0m \033[0;32m%b\n\033[0m" "$*"
                $@
            }
              
            run sudo pkg_add automake-1.16.2
            
            run ./autogen.sh
            run ./configure --prefix=/usr
            run make
            run sudo make install
            run file /usr/bin/ctags
            run ctags --version
```

|option|required|overview|
|-|-|-|
|`runs-on`|✔︎|must be `macos-*`|
|`run`|✔︎|the commands you want to run in vm|
|`box`|✔︎|the vagrant box name, the most commonly used are `generic/freebsd11` `generic/freebsd12` `generic/netbsd8` `generic/netbsd9` `generic/openbsd6`, you can find your appropriate box from [https://app.vagrantup.com/boxes](https://app.vagrantup.com/boxes)|
|`mem`|✗|the memory size of vm, default value is `2048` MB|


The source code and directory are all synchronized into the VM.

All the `GITHUB_*` as well as `CI=true` environment variables are passed into the VM.

# Under the hood

`GitHub Actions` only supports `Ubuntu`, `Windows` and `macOS` out of the box. 

However, the `macOS` support virtualization. It has `VirtualBox` and `Vagrant` installed.

So, we can run the `FreeBSD`, `NetBSD`, `OpenBSD` OS in `VirbualBox` on `macOS`.












