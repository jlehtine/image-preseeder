Image Preseeder Examples
========================

The examples provided in the `examples` directory show how the Image Preseeder
can be used to generate images suitable for non-interactive automated
installations. They also demonstrate how the resulting VirtualBox virtual
machines can be controlled and updated using external scripts. For example, to
run a specific automated test suite in the virtual machine.

The examples have been presented as a Makefile which downloads the original
installation image, converts it into a preseeded one using Image Preseeder,
installs a new virtual machine based on the preseeded image and updates or
controls it with external scripting. See `Makefile` in specific example
directory to see the actual steps taken.


Running examples
----------------

The examples require network connectivity and download large installation
images from the network. GNU make and VirtualBox must be installed to run the
examples. Install packages *make* and *virtualbox* (or *virtualbox-ose* on
Debian 6.0) to ensure that pre-requisites are met.

If you have an installed version of Image Preseeder, you have to first make
personal copy of the examples directory. Then you can just run `make` in the
examples directory to run all examples or in one of the example directories to
run the specific example. If you have just built Image Preseeder, you can run
examples by running `make examples` in the *TOP* build directory.

Files downloaded and produced by the examples are stored by default in the
examples directory. The examples also create and register VirtualBox virtual
machines with name *image-preseeder-examples-EXAMPLE* where *EXAMPLE* is the
name of the example.

The examples use Makefile rules to run the example incrementally. This means
that the successfully performed steps are not repeated on re-runs. To rerun the
steps you have to first run `make clean-examples`. The downloaded original
installation images are cached and are not re-downloaded unless you manually
remove them.


Options
-------

You can set and export some environment variables to configure the behavior of
examples.

### IMAGE_PRESEEDER ###

Specifies the `image-preseeder` script to be used. By default uses the one
installed in the path. The Makefile in top build directory sets this to the
newly built script by default.

### IMAGE_PRESEEDER_STORAGE ###

Specifies where the large files, i.e. downloaded image files, preseeded image
files and virtual machines, are stored. By default these are stored in
`examples/storage`.

### IMAGE_PRESEEDER_VBEXECUTOR ###

Specifies the command used to synchronously execute VirtualBox virtual machines
when running the examples. Defaults to `VirtualBox` which shows the console
window to illustrate the progress within the virtual machine. If you are
executing the examples in a non-desktop environment or wish to hide the console
for more realistic example of how automated test environment would behave, you
should set this to `VBoxHeadless`.

### IMAGE_PRESEEDER_VBMANAGE ###

Specifies the command used to manage VirtualBox virtual machines. Defaults to
`VBoxManage`.


Example cases
-------------

### ubuntu-server-install ###

Shows how to convert Ubuntu server image into a preseeded image and how to
non-interactively create and install a new VirtualBox virtual machine using the
preseeded image. Finally installs Apache web server to the virtual machine as a
separate externally controlled non-interactive update.
