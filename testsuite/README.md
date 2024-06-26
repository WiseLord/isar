# Install Avocado

The framework could be installed by using standard HOWTO:

  https://github.com/avocado-framework/avocado#installing-with-standard-python-tools

## For Debian (tested on Debian 11.x)

```
$ sudo apt-get update -qq
$ sudo apt-get install -y virtualenv
$ rm -rf /tmp/avocado_venv
$ virtualenv --python python3 /tmp/avocado_venv
$ source /tmp/avocado_venv/bin/activate
$ pip install avocado-framework==100.1
```

# Run test

## Quick developers test

```
$ avocado run ../testsuite/citest.py -t dev --max-parallel-tasks=1
```

## Single target test

```
$ avocado run ../testsuite/citest.py -t single --max-parallel-tasks=1 -p machine=qemuamd64 -p distro=bullseye
```

## Fast build test

```
$ avocado run ../testsuite/citest.py -t fast --max-parallel-tasks=1
```

## Full build test

```
$ avocado run ../testsuite/citest.py -t full --max-parallel-tasks=1
```

## Fast boot test

```
$ avocado run ../testsuite/citest.py -t startvm,fast
```

## Full boot test

```
$ avocado run ../testsuite/citest.py -t startvm,full
```

# Running qemu images

## Manual running

There is a tool start_vm which is the replacement for the bash script in
`isar/scripts` directory. It can be used to run image previously built:

```
start_vm -a amd64 -b /build -d bullseye -i isar-image-base
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
            script='test_systemd_unit.sh getty.target 10')
```

The default location of custom scripts is `isar/testsuite/`. It can be changed
by passing `-p test_script_dir="custom_path"` to `avocado run`
arguments.

# Custom test case creation

The minimal build test can be look like:

```
#!/usr/bin/env python3

from cibase import CIBaseTest

class SampleTest(CIBaseTest):
    def test_sample(self):
        self.init()
        self.perform_build_test("mc:qemuamd64-bullseye:isar-image-base")
```

To show the list of available tests you can run:

```
$ avocado list sample.py
avocado-instrumented sample.py:SampleTest.test_sample
```

And to execute this example:

```
$ avocado run sample.py:SampleTest.test_sample
```

## Using a different directory for custom testcases

Downstreams may want to keep their testcases in a different directory
(e.g. `./test/sample.py` as top-level with test description) but reuse
classes implemented in Isar testsuite (e.g. `./isar/testsuite/*.py`). This is
a common case for downstream that use `kas` to handle layers they use.

In this case it's important to adjust `PYTHONPATH` variable before running
avocado so that isar testsuite files could be found:

```
# TESTSUITEDIR="/work/isar/testsuite"
export PYTHONPATH=${PYTHONPATH}:${TESTSUITEDIR}
```

# Example of the downstream testcase

See `meta-isar/test` for an example of the testcase for kas-based downstream.
