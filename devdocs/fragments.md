![DREAMM](dreamm-logo.png)

# .dreamm File Specification
As of DREAMM 4.0x

This page is an attempt to document DREAMM’s internal text-based game description format.
It is a work in progress.


## Contents

There’s a lot to go over, so let’s break things up like this:

* [Introduction](#introduction)
   * [Hacking DREAMM’s existing descriptions](#hacking-dreamms-existing-descriptions)
   * [Creating new descriptions](#creating-new-descriptions)
   * [Making changes stick](#making-changes-stick)
   * [Launching CD-based games](#launching-cd-based-games)
   * [Adjusting common parameters](#adjusting-common-parameters)

* [Installable DREAMMs](#installable-dreamms)
   * [Making an installable .dreamm](#making-an-installable-dreamm)
   * [Enumerating game files](#enumerating-game-files)
   * [Picking an anchorfile](#picking-an-anchorfile)
   * [Fixing up the mounts](#fixing-up-the-mounts)
   * [Creating a DREAMMPack](#creating-a-dreammpack)



 ## Introduction

DREAMM’s game description files are what power its ability to install, configure, and launch games in a user-friendly manner.
The format has evolved greatly over DREAMM’s four releases, as new features have been added or modified to be more powerful.

Game descriptions are stored in text files with a `.dreamm` extension (earlier releases used a `.info` extension).
Typically, a single file is used to describe one game and all its variants, though it’s also possible to use one file to describe multiple games, or to split one game out into multiple files.


### Hacking DREAMM’s existing descriptions

DREAMM’s built-in game support is stored in an internal ZIP file, which you can extract via:

```
dreamm -extractall ifsdreamm :ifs
```

This command will extract the contents of the internal ZIP file to a directory called `ifsdreamm`.
In that directory you will find a bunch of stuff, but this document is concerned with the contents of the `dreammpack/dreamms` subdirectory, which houses all the `.dreamm` files.

If DREAMM sees a directory named `ifsdreamm` next to the executable, it will load those files in preference over its internal files.
This allows you to easily fiddle with existing definitions.
To test whether this is working, go ahead and modify the name at the top of a file representing a game you have installed.
Then run DREAMM to see if the change stuck.


### Creating new descriptions

If you want to try out new games in DREAMM, you can do so via the `-launch` command:

```
dreamm -launch <path/to/exe>
```

DREAMM will load the executable file at `<path/to/exe>` and determine if it is a Windows EXE program, a DOS EXE program, a DOS COM program, or a DOS batch file.
Based on that information, DREAMM will internally create a mini .dreamm file to try and run the game.

If you’d like to capture a starting point for running that game in the future, you can use the `-makedreamm` option.
When used together with `-launch`, it causes DREAMM to write the internally-created .dreamm file to the location you specify:

```
dreamm -makedreamm <path/to/dreammfile> -launch <path/to/exe>
```

Once you have the .dreamm file, you can begin to modify it as described in this document.

> [!NOTE]
> The `-makedreamm` command must appear _before_ the `-launch` command, since the launch command will treat anything after it as part of the command line to pass to the game.

After making changes, you can pass the .dreamm file to the `-launch` command directly:

```
dreamm -launch <path/to/dreammfile>
```

and test out the changes without having to recreate the original command line parameters.

> [!NOTE]
> The .dreamm files created this way are only useful as starting points or experiments.
> If you are interested in creating DREAMMPacks to support games, including easy installation and configuration within DREAMM, see the section on [Installable DREAMMs](#installable-dreamms) once you have your game up and running.


### Making changes stick

By default, when using the `-launch` command, any changes made by the program are lost when DREAMM exits.
This is because DREAMM just uses an internal RAM disk to hold modified files.

If you want the program to be able to make changes that persist, you can use the powerful `-mount` option in conjunction with `-launch`.

For example, if you want to persist any changes the game makes to the C: drive, you just specify a mount like this:

```
dreamm -mount rw:C=path/on/host -launch path/to/game
```

This causes all newly-created or modified files on the C: drive to be written to the specified _path/on/host_ on your host system, rather than to the temporary RAM disk.

> [!NOTE]
> The `-mount` command must appear _before_ the `-launch` command, since the launch command will treat anything after it as part of the command line to pass to the game.

You can specify multiple `-mount` options at once, and they can map any part of the virtual drive hierarchy.
For example:

```
dreamm -mount rw:C:\DREAMM=hostpath1 -mount rw:C=hostpath2 -launch path/to/game
```

would write all changes that were made in the `C:\DREAMM` subtree to _hostpath1_, while any other changes to the C drive would be written to _hostpath2_.


### Launching CD-based games

While the basic `-launch` command works fine for games that install completely to the hard disk and don’t require a CD.
However, games that do require a CD will require you to tell DREAMM where the CD-ROM files live.
This is also accomplished via the `-mount` option.

Since DREAMM assumes that your virtual D: drive is the CD-ROM drive, you need to mount the contents of the CD-ROM to the virtual D: drive like so:

```
dreamm -mount D=path/to/cd -launch path/to/game
```

DREAMM supports both directories and CD images in the _path/to/cd_ field.
CD images are preferred and more likely to succeed because they contain the raw data tracks, CD audio tracks, and metadata such as the volume name, which is frequently checked by games.
But if the game doesn’t check for that stuff, a loose collection of files in a directory will work just as well.

> [!NOTE]
> The `-mount` command must appear _before_ the `-launch` command, since the launch command will treat anything after it as part of the command line to pass to the game.

If you have a CD-based game that can be run directly from the CD, unfortunately you will need to extract the executable to use with the `-launch` command, since DREAMM currently requires direct access to the executable to determine its characteristics.


### Adjusting common parameters

The default .dreamm file that is created when using `-launch` is configured as follows:

* DOS: Pentium 100 (approximately), DOS 5.0, 16MB RAM with EMS/XMS, Super VGA graphics, SoundBlaster 16 audio, files in `C:\DREAMM`
* Windows: Windows 98, 1024x768 video in 16-bit color, files in `C:\Program Files\DREAMM\Generic`

If you want to change these pararameters, you can use `-makedreamm` to output the .dreamm file and modify things there, or you can use the `-property` option to specify properties directly from the command-line.

Here are the most common properties you may want to adjust.
For more detailed information, plus additional options, see the [System Properties](#system-properties) documentation:

* __rootpath__ &mdash; controls the location where the files appear in the virtual system
* __osenv__ &mdash; lets you specify the version of DOS/Windows that programs will see
* __mips__ &mdash; (mostly DOS only) controls the speed of the emulated CPU
* __ramkb__ &mdash; (DOS only) controls the amount of RAM the system sees
* __videohw__ &mdash; (DOS only) controls which video hardware is installed
* __audiohw__ &mdash; (mostly DOS only) controls which audio harwdare is installed
* __winres__ &mdash; (Windows only) lets you specify the screen resolution in Windows
* __locale__ &mdash; (Windows only) lets you specify the locale in Windows

For example, you can launch with:

```
dreamm -prop videohw=cga -prop ramkb=640 -prop mips=1.2 -launch path/to/game
```

to launch a DOS game with CGA hardware, 640KB of RAM, and running closer to a 10MHz 286 speed.

> [!NOTE]
> Any `-prop` commands must appear _before_ the `-launch` command, since the launch command will treat anything after it as part of the command line to pass to the game.


## Installable DREAMMs

To make a game installable within DREAMM like the built-in games, you will need to go beyond the minimal .dreamm files created by the `-launch` command.
Those files have several characteristics that make them unsuitable for the general case:

* They presume that the game has already been installed and has all the files laid out where they need to be.
* They don’t list all the files required by the game, so DREAMM doesn’t know how to search for them.
* The default set of mounts reference absolute locations on your hard disk, and thus won’t work outside of your system.

DREAMM’s philosophy is that it should be easy to install games from their original media, as well as from online stores like Steam and GOG.
For some games, this is relatively easy to achieve.
For others, it will take some hard work!

Below you’ll find some steps that outline what needs to be done to take a .dreamm file from its minimal state into something that can be shared and used more broadly.


### Enumerating game files

The first and most important step toward making a proper .dreamm file is to add to it the full list of files needed by the game.

By default, when DREAMM creates a .dreamm file for use with the `-launch` command, it just creates a single dummy [file](#file-objects) object within the game variant.
This works because the file objects are only relevant at installation time, and when you `-launch` a game you’re running something that’s already effectively installed.

To support proper installation, DREAMM needs to know the complete list of files required, and where they should live in the installation directory.

The easiest way to generate a list of files is to use DREAMM itself to output the list.
From the command line, you can run
```
   dreamm -dirinfo path/to/game
```
and DREAMM will output a full list of all the files in the directory hierarchy in [file object format](#file-objects).
These entries can be copied and pasted into your .dreamm file, replacing the dummy file object that DREAMM put there.
Note that `path/to/game` can be a directory on your hard drive, or any other image format that DREAMM supports.

However, this is just a starting point!
You will want to curate this list a bit, marking optional files with `optional = true`, and removing the CRC/size data for unimportant files that might change between releases, like text files.
You’ll also want to delete any game-generated files that wouldn’t be present in a fresh install.

If the game has an associated CD-ROM, you will also need to document the files that are on that.
You can use the `-dirinfo` command here as well, just passing the CD image file, to get the list of files.
This list should definitely be scrutinized for unnecessary junk as well, like demos, extra programs, etc. that aren’t needed for running the game.

If you have file lists from multiple locations (for example, the game install directory plus a CD), then you will probably want to segregate those games in the install folder.
You can do this by modifying the `name` property on the file objects to place them in specific subdirectories.
For example, you could prepend `INSTALL\` to the name of all the files that are installed, and `CD\` to all the files that were on the CD.
If a game has multiple CDs, you will almost certainly want to place each CD’s files into separate directories.


### Picking an anchorfile

Once you have your file lists and install directory layout defined, you’ll want to pick an anchorfile.

The __anchorfile__ is a required property set in your variant that is used to optimize DREAMM’s scanning process.
It should be the name of a file that is as unique as possible to the particular game, such that if DREAMM sees a file with that name, it knows that it will be worth scanning the directory tree around that file for this game.
A bad example would be `SETUP.EXE`, since that’s a super common and generic name.
A better example would be something with the game’s name, like `DOOM.WAD`.

Anchorfiles can live anywhere within the game’s directory hierarchy.
However, by default, DREAMM only searches the directory tree starting where the anchorfile lives.
If you want DREAMM to start its search higher up in the tree, specify the __anchordepth__ property, and set it to the number of levels above the anchorfile location where you want the search to begin.

For example, if you had this directory tree, say on the game CD:

```
\
   GAME\
      SUBDIR\
         ANCHORFILE
```
then you would want to specify an __anchordepth__ of 2, so that DREAMM will start scanning two levels above the anchorfile, at the __GAME__ directory.
If the game also needed some files at the root, you could bump the anchordepth up to 3.


### Fixing up the mounts

The next thing to fix up are the mounts.
By default, DREAMM creates two mounts:

1. a read-write mount mapping the root of the C drive to a RAM disk
2. a read-only mount mapping the rootpath (either `C:\DREAMM` for DOS games or `C:\Program Files\DREAMM\Generic` for Windows games) to the host-side directory where the launched executable lives

If you launched the games with one or more additional `-mount` options, those are included as well ahead of the two default items so that the ones you specified take precedence.

The thing that needs to be fixed are the references to locations on your physical hard disk.
Instead, these mounts should be redirected to locations controlled by DREAMM.

For the read/write mount that covers all of the C drive, this is easy: just change the `srcpath = ram` to `srcpath = $(ifsdeltapath)`.
The __ifsdeltapath__ variable is set by DREAMM when starting a game, and points to the location on your host machine where the game-specific deltas are stored.
For the most part, there’s only a need for this single read-write mount.

For the read-only mount mapping the rootpath, change the __srcpath__ from the hard-coded location on your system to `$(ifsinstallpath)`.
You may optionally append additional paths to __$(ifsinstallpath)__ if you located the installation files somewhere else within the install hierarchy.

For any other mounts that are present, you’ll need to fix those up to point to directories relative to __$(ifsinstallpath)__.
For example, if your game is CD-based, and you specified that the CD’s directory tree get installed to the `CD` directory, then you would change the __srcpath__ to point to `$(ifsinstallpath)/CD`.

In general, you should not leave any mounts that refer to CD image files, and all mounts should be relative to either __$(ifsdeltapath)__ or __$(ifsinstallpath)__.

For more detailed information on the __mount__ objects, see [the reference documentation](#mount-objects) below.


### Creating a DREAMMPack

In order for DREAMM to load your .dreamm file automatically and include it as part of the available games, you need to wrap it in a DREAMMPack.
DREAMMPacks can be installed via the __Manage DREAMMPacks__ menu option in post-4.0 experimental builds.

A DREAMMPack is just a well-defined directory structure containing one or more .dreamm files along with a special `pack.dreamm` file that describes the pack as a whole.
The directory structure looks like this:
```
pack.dreamm
dreamms/
   __(one or more .dreamm files)__
icons/
   (zero or more 256x256 ARGB PNG files used for icons)
customs/
   (zero or more custom PNG files used for overlays)
```
When releasing DREAMMPacks it is recommended that this file structure be ZIPped and then renamed to have a .dreammpack extension.
But during development, a folder of loose files is fine as long as it has this structure.

The `pack.dreamm` file is pretty minimal and looks like this:

```
pack[]:
   name = (friendly name of pack)
   author = (optional; your name here)
   link = (optional; link to where you distribute your packs)
end
```

Once your DREAMMPack is installed, DREAMM will load it at startup and treat the games described therein just as it treats its built-in games.
To make changes to your .dreamm files, you can either directly edit the installed pack (assuming it was a loose collection of files), or you can edit a working version and then use the __Manage DREAMMPacks__ menu to install that version as an upgrade to your existing pack.
