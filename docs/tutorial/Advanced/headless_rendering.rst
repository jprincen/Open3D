.. _headless_rendering:

Headless rendering
-------------------------------------

This tutorial introduces how to render and save images from terminal without any display device.

.. Note:: This feature is experimental. Only tested with Ubuntu 16.04 environment.

Install OSMesa
````````````````````````

To generate a headless context, it is necessary to install `OSMesa <https://www.mesa3d.org/osmesa.html>`_.

.. code-block:: shell

    $ sudo apt-get install libosmesa6-dev

Otherwise, recent version of OSMesa can be built from source.

.. code-block:: shell

    # download OSMesa 2018 release
    $ cd
    $ wget ftp://ftp.freedesktop.org/pub/mesa/mesa-18.0.0.tar.gz
    $ tar xf mesa-18.0.0.tar.gz
    $ cd mesa-18.0.0/

    # configure compile option and build
    $ ./configure --enable-osmesa --disable-driglx-direct --disable-gbm --enable-dri --with-gallium-drivers=swrast
    $ make

    # add OSMesa to local path.
    $ export PATH=$PATH:~/mesa-18.0.0

.. _install_virtualenv:

Install virtualenv
````````````````````````

The next step is to make a virtual environment for Python.

.. code-block:: shell

    $ sudo apt-get install virtualenv python-pip
    $ virtualenv -p /usr/bin/python3 py3env
    $ source py3env/bin/activate
    (py3env) $ pip install numpy matplotlib

This script installs and activates ``py3env``. Necessary modules, ``numpy`` and ``matplotlib`` are installed on ``py3env``.

.. Error:: Anaconda users are recommended to use this configuration as ``conda install matplotlib`` installs additional modules that is not based on OSMesa. This will make **segmentation fault error** at the runtime.


Build Open3D with OSMesa
````````````````````````

Let's move to build folder.

.. code-block:: shell

    (py3env) $ cd ~/Open3D/
    (py3env) $ mkdir build && cd build

In the next step, there are two cmake flags need to be specified.

- ``-DOpen3D_HEADLESS_RENDERING=ON``: this flag informs glew and glfw should use **OSMesa**.
- ``-DOpen3D_USE_NATIVE_DEPENDENCY_BUILD=OFF``: note that headless rendering is only working with **glew 2.1** and **glfw 3.3-dev** version. In most of the case, these versions are not installed in vanilla Ubuntu systems. Instead of using naive build, this flag lets Open3D build glew 2.1 and glfw 3.3-dev from source.

As a result, the cmake command is the following

.. code-block:: shell

    (py3env) $ cmake -DOpen3D_HEADLESS_RENDERING=ON \
            -DOpen3D_USE_NATIVE_DEPENDENCY_BUILD=OFF \
            -DPYTHON_EXECUTABLE:FILEPATH=/usr/bin/python3 \
            ../src

Note that ``-DPYTHON_EXECUTABLE:FILEPATH=/usr/bin/python3`` is the same path what was used for :ref:`install_virtualenv`.

If cmake successfully generates makefiles, build Open3D.

.. code-block:: shell

    (py3env) $ make # or make -j in multi-core machine



Test headless rendering
````````````````````````

As a final step, test a python script that saves depth and surface normal sequences.

.. code-block:: shell

    (py3env) $ cd ~/Open3D/build/lib/Tutorial/Advanced/
    (py3env) $ python headless_rendering.py

This should print the following:

.. code-block:: shell

    Capture image 00000
    Capture image 00001
    Capture image 00002
    Capture image 00003
    Capture image 00004
    Capture image 00005
    :
    Capture image 00030

Rendered images are at ~/Open3D/build/lib/TestData/depth and image folder.

.. Note:: ``headless_rendering.py`` saves png files. This may take some time. Try tweak the script for your purpose.

.. Error:: If glew and glfw did not correctly linked with OSMesa, it may crash with following error. **GLFW Error: X11: The DISPLAY environment variable is missing. Failed to initialize GLFW**
