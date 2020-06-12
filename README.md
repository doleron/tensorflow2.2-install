# tensorflow2.2-install
Set of resources showing how to install Tensorflow 2.2

## Install Tensorflow from sources on linux

This instructions are based on https://www.tensorflow.org/install/source

## Pre flight

First, update local machine

```bash
$ sudo apt update
$ sudo apt upgrade
```
Installing pre-requirements:

```bash
$ sudo apt install python-dev python-pip python3-dev python3-pip git curl
```
Installing python3 packages:

```bash
$ pip3 install -U --user pip six numpy wheel setuptools mock 'future>=0.17.1'
$ pip3 install -U --user keras_applications --no-deps
$ pip3 install -U --user keras_preprocessing --no-deps
```
If you planning to use legacy python, run the following commands 

```bash
$ pip install -U --user pip six numpy wheel setuptools mock 'future>=0.17.1'
$ pip install -U --user keras_applications --no-deps
$ pip install -U --user keras_preprocessing --no-deps
```

Donwload / set bazelisk

```bash
$ wget https://github.com/bazelbuild/bazelisk/releases/download/v1.5.0/bazelisk-linux-amd64
```
Once you get the file, move it to /usr/local/bin with sudo
```bash
$ sudo mv bazelisk-linux-amd64 /usr/local/bin/bazel
$ sudo chmod +x /usr/local/bin/bazel
```

## gettting and building tensorflow 2.2

Use git to clone tensorflow from github:
```bash
git clone https://github.com/tensorflow/tensorflow.git
cd tensorflow
``

Call `configure` script. Reply the questions according your needs.
If you are not sure about some question, try the default option
```bash
./configure
``

Once configuration is finished, it is time to build the builder program using bazel:
```bazel
bazel build //tensorflow/tools/pip_package:build_pip_package
```

The previous command will take longer to finish. Then call the build itself:
```bash
./bazel-bin/tensorflow/tools/pip_package/build_pip_package /tmp/tensorflow_pkg
```

Once the build is finish bazel creates a python wheel file /tmp/tensorflow_pkg/tensorflow-\*.whl.
During the writing of this tutorial, the file is /tmp/tensorflow_pkg/tensorflow-2.2.0-cp36-cp36m-linux_x86_64.whl
Note that this name can change, thus,  check the directory to be sure:

```bash
$ ls /tmp/tensorflow_pkg
tensorflow-2.2.0-cp36-cp36m-linux_x86_64.whl
```

Finally, install tensorflow using pip3
```bash
$ pip3 install --user /tmp/tensorflow_pkg/tensorflow-2.2.0-cp36-cp36m-linux_x86_64.whl
```
Or using pip if you are using legacy python instead
```bash
$ pip3 install --user /tmp/tensorflow_pkg/tensorflow-2.2.0-cp36-cp36m-linux_x86_64.whl
```

## Check out the instalation

If eveything is good, tensorflow 2.2 is available on the system.

If you prefer python3, run the following command
```bash
$ cd ~
$ python3 -c 'import tensorflow as tf; print(tf.__version__)'  # for Python 3
```

or for legacy python instead:
```bash
$ cd ~
$ python -c 'import tensorflow as tf; print(tf.__version__)'  # for Python 2
```

Any of the previous commands should print the current version. For me it outputs `2.2.0`.

Obs.: make sure you change the current folder like `cd ~` or so. If you try to run the previous commands in the same fodler as tensorflow was compiled you end up with a error such as: `ImportError: cannot import name 'function_pb2'`.

## Actually running tensorflow

If you are like me and prefer to see things actually running, I do recommend to call:

```bash
$ python3 test.py
```

The file test.py can be found here in the same repository.
