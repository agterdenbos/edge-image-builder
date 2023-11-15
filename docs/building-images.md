# Building Images

Two things are necessary to build an image using EIB:
1. A configuration file that describes the image to build
1. A directory that contains the base SLE Micro image to modify, along with any other custom files that
   will be included in the built image

## Image Configuration File

The image configuration file is a YAML document describing a single image to build. The file is specified using
the `-config-file` argument. Only a single image may be built at a time, however the same image configuration
directory may be used to build multiple images by creating multiple configuration files.

The following can be used as the minimum configuration required to create an image:
```yaml
apiVersion: 1.0
image:
  imageType: iso
  baseImage: SLE-Micro.x86_64-5.5.0-Default-SelfInstall-GM.install.iso
  outputImageName: eib-image.iso
```

* `apiVersion` - Indicates the version of the configuration file schema for EIB to expect 
* `imageType` - Must be either `iso` or `raw`.
* `baseImage` - Indicates the name of the image file used as the base for the built image. This file must be located
  under the `images` directory of the image configuration directory (see below for more information). This image will
  **not** directly be modified by EIB; a new image will be created each time EIB is run.
* `outputImageName` - Indicates the name of the image that EIB will build. This may only be a filename; the image will
  be written to the root of the image configuration directory.

### Operating System

The operating system configuration is entirely optional.

The following describes the possible options for the operating system section:
```yaml
operatingSystem:
  kernelArgs:
  - arg1
  - arg2
```

* `kernelArgs` - Optional; Provides a list of flags that should be passed to the kernel on boot.

## Image Configuration Directory

The image configuration directory contains all the files necessary for EIB to build an image. As the project matures,
the structure of this directory will be better fleshed out. For now, the required structure is described below:

```shell
.
├── eib-config-iso.yaml
├── eib-config-raw.yaml
└── images
    └── SLE-Micro.x86_64-5.5.0-Default-SelfInstall-GM.install.iso
    └── SLE-Micro.x86_64-5.5.0-Default-GM.raw
```

* `eib-config-iso.yaml`, `eib-config-raw.yaml` - All image configuration files should be in the root of the image
  configuration directory. Multiple configuration files may be included in a single configuration directory, with
  the specific configuration file specified as a CLI argument as described above.
* `images` - This directory must exist and contains the base images from which EIB will build customized images. There
  are no restrictions on the naming; the image configuration file will specify which image in this directory to use
  for a particular build.

There are a number of optional directories that may be included in the image configuration directory:

* `scripts` - If present, all the files in this directory will be included in the built image and automatically
  executed during the combustion phase.
* `rpms` - If present, all RPMs in this directory will be included in the built image and installed during the 
  combustion phase. These RPMs are installed directly (instead of using zypper), which means that there will be no
  automatic dependency resolution.