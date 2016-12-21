# (open)SUSE installation images

Scripts and tools to generate the disk images used during installation
(inst-sys) and the rescue disk images for openSUSE and SUSE.

## Generating the images locally

To build a new image, you have to:

* be root
* put the installation-images directory on a *local* file system (ie. not NFS)
* have a valid .oscrc

Then you must run ```make``` once to build all parts.

The images will be stored in the 'image' directory, the contents of the images
will be in the 'tmp' directory.

```make install``` will gather the images and put them into the 'instsys' dir.

For testing purposes there is a make target 'cd1' that builds a complete
tree as it's used on our media.

`make iso` will directly create the .iso file for cd1

## Configuring the generation of images

The exact behavior of ```make``` can be influenced by several environment
variables. For a full description of these variables and how they affect each
image, check the [configoptions.md](configoptions.md) file.

## Committing changes to (open)SUSE

**NOTE: we discussed here something about a hack to exclude branding in some cases**

Every time a new commit is integrated into the master branch of the repository,
a new submit request is created in the openSUSE Build Service for the
[corresponding package](https://build.opensuse.org/package/show/openSUSE:Factory/installation-images-openSUSE).

A similar procedure is set in other branches of this repository, making possible
to submit changes to other products and versions as well.

* Branch ```sl_11.1``` SUSE Enterprise 11 SP4.
* Branch ```sle12``` SUSE Enterprise 12.
* Branch ```master``` Factory and SUSE Enterprise 12 SP1.

You can find more information about this workflow in the [linuxrc-devtools
documentation](https://github.com/openSUSE/linuxrc-devtools#opensuse-development).

## Anatomy of the images

After a successful execution of ```make``` the directory ```images/``` will
contain several subdirectories and files. Follows a short description of the images:

* ```images/base``` is the build environment used for the other images.
* ```images/boot``` is the image that contains the bootloader.
* ```images/cd1``` is a media used for testing purposes only; it's optional.
* ```images/initrd``` contains all the files used at the initrd stage ([check out our initrd implementation](https://github.com/openSUSE/linuxrc)).
* ```images/rescue``` contains the data for the rescue system.
* ```images/root``` is mounted to provide the necessary files for YaST installation.


## FAQ

* How to make sure a driver is available in the installation/rescue system?
  Check the [modules.md](modules.md) file.

* How to add a package or one of its file to the image?
  Check the [files.md](files.md) file.

* How the branding works?
  Check the [branding.md](branding.md) file

## Troubleshooting and Hacks

* How to remove a specific file from the initrd image?

  If you can rebuild the image, the recommended way is to exclude the file from the package that contains it. To do so, you need to modify the .file_list of the image (see [files.md](files.md) ).

  If you're looking for a quick hack with `mksusecd` please keep in mind that symlinks won't work; instead you'll need to overwrite the files with other, real files (even empty) thanks to the --initrd flag.

* After building an image from the master branch and installing it I get an error regarding "Wrong media".

  This is because for some reason, your images have mismatching build IDs. To check a build id, you want to look at the content of the .config files. ` find . -name "*.config"` will show a few of them, in general you'll be interested in .instsys.config and linuxrc.config.

* What happens if a package is included in two subsequent stages (e.g. initrd, root)?

  Installation-images is smart enough to recognise this duplication and eliminate it. Please do not abuse of this feature because the .file_list files serve as a list of files included in the image (read: documentation) so polluting the files might cause confusion.

* What is the control.xml used for?

  This is a very important file! It gives YaST the instructions on how to (the sequence of steps) used to install a system.

* How do I mount the .iso files?

  The images are generally compressed so you'll need to extract them
  before mounting.

**TODO FAQ: compression algorithm, linemode, fonts**