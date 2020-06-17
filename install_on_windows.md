Step by step instructions to install TensorFlow 2.2 from sources on Windows 10 64 bits.

I particularly prefer to use TensorFlow on a linux platform like Ubuntu 20.04 myself. But if you do insist to use Windows, here are some dissected instructions to install TensorFlow on this platform.

# Notes about system requirements

In my tests I used a Windows 10 pro 64 bits running on virtualbox with 6GB or 8GB de RAM and 100 GB of disk space. The installation process consumed around 12 GB.
Bazel, the Google build system used by TensorFlow build takes huge amounts of memory and CPU time. In adddition, during the build, bazel download gigabytes of artifacts from the internet. 

It is always convenient to update windows with windows update before carry out the TensorFlow build.

# Download and install python

Tensorflow documents recommeds to use Python 3.5 or 3.6. But in this link https://www.tensorflow.org/install/source_windows#tested_build_configurations it is report version 2.2 working with Python 3.8 as well.

Here you can find all downloads available to windows:
https://www.python.org/downloads/windows/

I decided to go by using the lastet version:

https://www.python.org/ftp/python/3.8.3/python-3.8.3-amd64.exe

# Download and install git for windows

Git for windows versions can be find here:

https://git-scm.com/download/win

And this was the link I ended up with:

https://github.com/git-for-windows/git/releases/download/v2.27.0.windows.1/Git-2.27.0-64-bit.exe

# Install python packages with pip

```
pip3 install six numpy wheel
pip3 install keras_applications==1.0.6 --no-deps
pip3 install keras_preprocessing==1.0.5 --no-deps
```

# Install bazel/bazelisk

https://github.com/bazelbuild/bazelisk/releases/download/v1.5.0/bazelisk-windows-amd64.exe

Rename bazelisk-windows-amd64.exe to bazel.exe and add the folder where you saved bazel.exe to your %PATH% environment variable.

# Install MSYS2 

Follow the MSYS2 installation instructions: https://www.msys2.org/

The MSYS2 installation is basically a sequence of next next next and running some commands to update packages inside MSYS2. Everything is plain described in MSYS2 website.

Do note forget to add C:\msys64\usr\bin to your %PATH% environment variable.

Then, execute:

```
pacman -S git patch unzip
```

# Install Windows Visual C++ stuff

Microsoft Visual C++ 2019 Redistributable:
Go to https://visualstudio.microsoft.com/downloads/ and search for "Microsoft Visual C++ 2019 Redistributable"

Microsoft Build Tools 2019
go to https://visualstudio.microsoft.com/visual-cpp-build-tools/ and download & install

# Clone tensorflow

open a command prompty and type

```
mkdir tf_build
cd tf_build
git clone https://github.com/tensorflow/tensorflow.git
cd tensorflow
git checkout v2.2.0
```
PS.: check out this link if the cloning is too slow: https://stackoverflow.com/questions/45768893/git-clone-in-windows-much-slower-than-in-linux

# Build tensorflow

execute:

```
python ./configure.py
```
and then

```
bazel build //tensorflow/tools/pip_package:build_pip_package
```
The command above will demand hours to finish. In my experiments this took around 4,5 hours long to finish.

Note that bazel will download several packages during the build so timeouts and network errors can occur which breaks the build process. If something like this happens to you, just restart the build process by re-run the build command.

Another common issue is memory starvation. Bazel eats tons of memory during the build and if the system runs out of memory the build will fail with a strange internal compiler error. Just re-run the build command and the build will restart from some intermediary save point. Anyway, consider to increase the system memory.

Once finished, run:
```
bazel-bin\tensorflow\tools\pip_package\build_pip_package C:/tmp/tensorflow_pkg/tensorflow-2.2.0-cp36-cp36m-win_amd64.whl
```
This command took a couple of minutes to finish, generating a `tensorflow-*.whl` python wheel file created into folder `C:/tmp/tensorflow_pkg` . In my case the file is named tensorflow-2.2.0-cp36-cp36m-win_amd64.whl .

Now you know the file name, install it by running:

```
pip3 install C:/tmp/tensorflow_pkg/tensorflow-version-cp36-cp36m-win_amd64.whl
```

In this point TensorFlow 2.2.0 should be properly installed on the system. Let's check out it.

# Validating the installation

In command prompt, move to another folder and run python:

```
cd ..
python
```
then execute the following python instructions:
```python
import tensorflow as tf;
print(tf__version__)
```
DISCLAIMER: Note that move to outside the folder where you build tensorflow is required. Missing this will end up raising `ImportError("Could not import tensorflow. Do not import tensorflow "` error.

If everything is good, python will print out the TensorFlow version:
```
2.2.0
```

You can also use python to train a model:

call:

```
python test.py
```

Note that the test.py program is available on this repository. Once ran, this program should output something as:

```
Downloading data from https://storage.googleapis.com/tensorflow/tf-keras-datasets/mnist.npz
11493376/11490434 [==============================] - 1s 0us/step
Epoch 1/5
1875/1875 [==============================] - 2s 904us/step - loss: 0.2911 - accuracy: 0.9141
Epoch 2/5
1875/1875 [==============================] - 2s 945us/step - loss: 0.1419 - accuracy: 0.9582
Epoch 3/5
1875/1875 [==============================] - 2s 898us/step - loss: 0.1053 - accuracy: 0.9680
Epoch 4/5
1875/1875 [==============================] - 2s 960us/step - loss: 0.0852 - accuracy: 0.9737
Epoch 5/5
1875/1875 [==============================] - 2s 1ms/step - loss: 0.0734 - accuracy: 0.9765
313/313 - 0s - loss: 0.0745 - accuracy: 0.9774
```

That's it. Tensorflow is running on your machine.
