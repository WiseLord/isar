# Install Avocado

The framework could be installed by using standard HOWTO:

  https://github.com/avocado-framework/avocado#installing-with-standard-python-tools

## For Debian (tested on Debian 11.x)

```
$ pip install avocado-framework==99.0
```

# Run test

## Quick developers test

```
$ avocado run ../testsuite/citest.py -t dev --nrunner-max-parallel-tasks=1
```

## Single target test

```
$ avocado run ../testsuite/citest.py -t single --nrunner-max-parallel-tasks=1 -p machine=qemuamd64 -p distro=bullseye
```

## Fast build test

```
$ avocado run ../testsuite/citest.py -t fast --nrunner-max-parallel-tasks=1 -p quiet=1
```

## Full build test

```
$ avocado run ../testsuite/citest.py -t full --nrunner-max-parallel-tasks=1 -p quiet=1
```

## Fast boot test

```
$ avocado run ../testsuite/citest.py -t startvm,fast -p time_to_wait=300
```

## Full boot test

```
$ avocado run ../testsuite/citest.py -t startvm,full -p time_to_wait=300
```

# Running qemu images

## Manual running

There is a tool `start_vm.py` which is the replacement for the bash script in
`isar/scripts` directory. It can be used to run image previously built:

```
./start_vm.py -a amd64 -b /build -d bullseye -i isar-image-base
```

# Tests for running commands under qemu images

Package `isar-image-ci` configures `ci` user with non-interactive SSH access
to the machine. Image `isar-image-ci` preinstalls this package.

Example of test that runs qemu image and executes remote command under it:

```
    def test_getty_target(self):
        self.init()
        self.vm_start('amd64','bookworm', \
            image='isar-image-ci',
            cmd='systemctl is-active getty.target')
```

To run something more complex than simple command, custom test script
can be executed instead of command:

```
    def test_getty_target(self):
        self.init()
        self.vm_start('amd64','bookworm', \
            image='isar-image-ci',
            script='test_getty_target.sh')
```

The default location of custom scripts is `isar/testsuite/`. It can be changed
by passing `-p test_script_dir="custom_path"` to `avocado run`
arguments.
