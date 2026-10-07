![DREAMM](dreamm-logo.png)

# .dreamm File Specification

This document is an attempt to describe DREAMM’s internal text-based game description format.
It is a work in progress.


## Contents

There’s a lot to go over, so let’s break things up like this:

* [Core Concepts](#core-concepts)
   * [Properties](#properties)
   * [Variable Substitution](#variable-substitution)
   * [Multi-Line Values](#multi-line-values)
   * [Objects](#objects)
   * [Conditional Objects](#conditional-objects)

* [File Structure](#file-structure)
   * [Object Hierarchy](#object-hierarchy)
   * [Built-In Variables](#built-in-variables)
   * [System Properties](#system-properties)

* [Object Descriptions](#object-descriptions)
   * [desc objects](#desc-objects)
   * [game objects](#variant-objects)
   * [demo objects](#variant-objects)
   * [installer objects](#variant-objects)
   * [upgrader objects](#variant-objects)
   * [addon objects](#variant-objects)
   * [fragment objects](#variant-objects)
   * [mount objects](#mount-objects)
   * [config objects](#config-objects)
   * [option objects](#option-objects)
   * [configutil objects](#configutil-objects)
   * [warning objects](#warning-objects)
   * [script objects](#script-objects)
   * [charset objects](#charset-objects)
   * [file objects](#file-objects)


## Core Concepts

A .dreamm file is a UTF-8 text file.

It is parsed one line at a time, with all leading and trailing whitespace stripped.
Thus, line endings are significant, but whitespace on either end is not.

Comments begin with `//` and continue to the end of the line.
Multi-line comments are not supported.

There is no escaping mechanism; instead, built-in variables are provided to cover embedding special characters and whitespace.

Game description files are fundamentally collections of [properties](#properties) and [objects](#objects), which are described in more detail below.


### Properties

Properties are just simple name/value pairs and are defined as:

```
<property> = <value>
```

where `<property>` is the name of the property to set and `<value>` is what you want to set it to.

Property names can be any sequence of letters, numbers, and underscores.

> [!WARNING]
> By convention, properties defined by DREAMM are all-lowercase.
> If you specify an all-lowercase property that is not recognized, DREAMM will issue a warning.
> Game-specific properties should be written in UPPERCASE; these will be fully ignored by DREAMM except for performing substitutions.


### Variable Substitution

The values of properties can be substituted in most situations throughout the file.
To insert the value of a property, use `$(<propertyname>)`.
For example:

```
// set a user-defined property VIDEO to be 'vga'
VIDEO = vga

// set the DREAMM-recognized videohw property to be the value of the VIDEO property
videohw = $(VIDEO)
```

The same syntax for variable substitution also allows you to insert arbitrary Unicode characters.
Use the form `$(:u<xxxx>)` where `<xxxx>` is the hexadecimal representation of the character you want to insert.

Variable substitution is performed &ldquo;just in time&rdquo; when needed at runtime.
This means that modifications to properties can happen at a later time and still be effective.
This will be an important feature later when it comes to options and conditional behaviors.
For example:

```
// set the VIDEO property to be 'vga'
VIDEO = vga

// indicate that videohw should be set to the value of VIDEO
videohw = $(VIDEO)

// change the VIDEO property to 'mcga'
VIDEO = mcga

// when DREAMM queries the videohw property, it will get back $(VIDEO),
// and then substitute the final value of VIDEO which is 'mcga'
```

Sometimes variables need to be substituted within quoted strings (e.g., within scripts).
In this case, you probably want the property value to be escaped, so there is an alternate format for this: `$$(<propertyname>)`.
Example:

```
 // FILEPATH has backslashes, which generally need to be escaped when used in C-like languages
 FILEPATH = C:\foo\bar\file.txt

 // hey look, our scripting engine is a C-like language; use $$() to escape the backslashes
 script = if (file_exists("$$(FILEPATH)")) echo("Found")
```


### Multi-Line Values

There are several situations where supporting longer property values is highly useful.
To make this work, DREAMM supports a heredoc-like mechanism that begins with the sequence `[[[` and ends with the opposite sequence `]]]` on its own line.

```
 <property> = [[[<delimiter>
   <line-of-text>
   <line-of-text>
   ...
 ]]]
```

Any text that is included between the opening `[[[` sequence and the end of that first line will be used as the `<delimiter>` between each subsequent line of text.
(This differs from a traditional heredoc where the text following the opening delimiter becomes the ending delimiter.)

For each `<line-of-text>` processed, any comments are trimmed, and then all leading/trailing whitepace is trimmed.
If the resulting line is not empty, it is appended to the result, with the `<delimiter>` inserted between lines.

Careful use of the `<delimiter>` can produce several useful effects, as in the examples below:

```
  // this example produces a string 'ABC' with no spaces
  // in this case, the delimiter is empty
  // note that leading/trailing whitespace is trimmed for each line
  nodelim = [[[
    A
    B
    C
  ]]]

  // here we set the delimiter to be a carriage return plus linefeed
  // this produces a string with a newline between each line
  // note, however, tha the last line will not end with a $(CRLF), so if
  // that is required, you will need to manually include it
  linedelim = [[[$(CRLF)
    line1
    line2
  ]]]

  // in this example we use the string " and " as a delimiter
  // note the use of the $(SP) substitution to insert explicit spaces that might otherwise be trimmed
  // we also make use of comments within the string
  // this produces the string: "Paul and John and Ringo"
  anddelim = [[[$(SP)and$(SP)
    Paul  // this comment gets trimmed, along with the whitespace before it
    John
    // George -- this commmented out line ends up empty and thus discarded
    Ringo
  ]]]
```


### Objects

Objects are structured collections of properties and possibly other objects.
Objects are introduced by a keyword, followed by either an empty pair of brackets `[]` or a bracketed id `[<id>]`, followed by a colon.

> [!IMPORTANT]
> By convention, the bracketed id, if provided, must contain only lowercase characters, the digits 0-9, or hyphens.
> DREAMM enforces this convention when parsing .dreamm files.

If the colon is followed by a line ending, then the object is a multi-line object.
Multi-line objects require each property or inner object to be specified on a separate line.
A final `end` marker is used to indicate the end of the object.

Simple objects can also be specified more compactly in a single line form.
In single-line objects, multiple properties can be specified, each separated by a semicolon, immediately following the object introduction.
Note that single-line objects cannot have inner objects, so if you need those, you must define them in a multi-line object.

Here are some example objects to show the basic format:

```
  // this is a simple single-line object, with no id
  // properties are separated by semicolons
  script[init]: condition = is_game(); code = drive_set_label("D", "My Drive Label")

  // here is the same object as a multi-line object
  script[init]:
    condition = is_game()
    code = drive_set_label("D", "My Drive Label")
  end

  // this a config object with an id of 'sound'
  // note that there is a single 'title' property plus
  // two inner 'option' objects, each defined as a single-line object
  config[sound]:
    title = Sound Configuration
    option[speaker]: name = $(SPEAKER_NAME); audiohw = speaker
    option[adlib]:   name = $(ADLIB_NAME);   audiohw = adlib; priority = 1
  end
```


### Conditional Objects

Many objects support a __condition__ property that allows you to control whether or not that object is manifested.
The condition is a simple C-syntax expression that is evaluated as true if the result of evaulating the expression is non-zero.

Typical conditions are a boolean combination of one or more properties.
In addition, there are several functions can be used to query the environment that the system will be running:

| Function | Description |
|----------|-------------|
| `file_exists(<path>)` | Returns true if the file specified by `<path>` exists in the virtual filesystem. This is useful for enabling options based on the presence of certain installed files. |
| `file_size(<path>)` | Returns the size of the file specified by `<path>` in the virtual filesystem, or 0 if not found. |
| `is_game()` | Returns true if the base variant being run is a full game. |
| `is_demo()` | Returns true if the base variant being run is a demo. |
| `is_installer()` | Returns true if the base variant being run is an installer. |
| `is_upgrader()` | Returns true if the base variant being run is an upgrader. |
| `is_dos()` | Returns true if the base variant being run has specified a DOS environment. |
| `is_windows()` | Returns true if the base variant being run has specified a Windows environment. |
| `is_fmtowns()` | Returns true if the base variant being run has specified an FM-Towns environment. |

Below is an example of how conditions can be used in practice:

```
    // define some user properties
    HAS_VIDEO = true
    VIDEO_ENABLED = false
    DRIVERPATH = C:\driver

    config[video]:
        // combine properties here using C-style expressions
        condition = $(HAS_VIDEO) && $(VIDEO_ENABLED)
        option[vga]:
            name = $(VGA_NAME)
            videohw = vga
            // test for the presence of a file; note the $$() syntax
            // so that the DRIVERPATH is escaped for use within the quoted string
            condition = file_exists("$$(DRIVERPATH)\\vga.drv")
        end
    end
```


## File Structure

A `.dreamm` file consists of one or more __desc__ objects, each of which is its own little world.
Each desc object contains properties and objects, some of which may contain additional object types.


### Object Hierarchy

Here is a reference for the hierarchy of supported objects:

* [desc[id]](desc-objects) -- base descriptor
   * [mount[]](#mouse) -- one or more mounted directories/images in the virtual filesystem
   * [config[id]](#config-objects) -- zero or more configuration options
      * [option[id]](#option-objects) -- one or more options within a configuration
   * [configutil[id]](#configutil-objects) -- zero or more configuration utilities
   * [warning[id]](#warning-objects)   -- zero or more warning messages
   * [script[id]](#script-objects) -- zero or more script contexts
   * [charset[id]](#charset-objects)   -- zero or more character set definitions
   * [game|demo|addon|installer|upgrader|fragment[id]](#variant-objects) -- one or more variants
      * [file[]](#file-objects)   -- zero or more files


### Built-In Variables

DREAMM has a collection of standard variables available to all files.

#### Core Strings

| Variable | Meaning |
|----------|---------|
|`$(NULL)`|An empty string|
|`$(SP)`|A single space character (ASCII 0x20)|
|`$(SPACE)`|A single space character (ASCII 0x20)|
|`$(CR)`|Carriage Return character (ASCII 0x0D)|
|`$(LF)`|Newline character (ASCII 0x0A)|
|`$(CRLF)`|Carriage Return plus Newline|
|`$(TAB)`|Tab character (ASCII 0x09)|
|`$(SEMI)`|Semicolon character (ASCII 0x3B)|
|`$(QUOT)`|Double quote character (ASCII 0x22)|
|`$(DSLASH)`|Two forward slashes|
|`$(NBSP)`|A non-breaking space character (Unicode 0xA0)|
|`$(NBHY)`|A non-breaking hyphen character (Unicode 0x2011)|

#### Common User Interface Strings

| Variable | Meaning |
|----------|---------|
|`$(HERCULES_NAME)`|"Hercules (2 colors)"|
|`$(CGA_NAME)`|"CGA (4 colors)"|
|`$(TVIDEO_NAME)`|"Tandy/PCjr (16 colors)"|
|`$(EGA_NAME)`|"EGA (16 colors, planar)"|
|`$(VGA_NAME)`|"VGA (16/256 colors)"|
|`$(MCGA_NAME)`|"MCGA (256 colors)"|
|`$(SVGA_NAME)`|"Super VGA"|
|`$(SPEAKER_NAME)`|"PC speaker (1-bit DAC)"|
|`$(TAUDIO_NAME)`|"Tandy/PCjr (SN76496)"|
|`$(CMS_NAME)`|"CMS/GameBlaster (2×SAA1099)"|
|`$(ADLIB_NAME)`|"AdLib (OPL)"|
|`$(SB16_NAME)`|"SoundBlaster 16 (OPL3 + PCM)"|
|`$(MT32_NAME)`|"Roland MT-32"|
|`$(GMIDI_NAME)`|"General MIDI"|

#### CPU Speed Strings

| Variable | Meaning |
|----------|---------|
|`$(MIPS_FMTOWNS)`|"1.5"|
|`$(MIPS_FMTOWNS_LATER)`|"2.5"|
|`$(MIPS_286_10)`|"1.2"|
|`$(MIPS_386SX_20)`|"3.1"|
|`$(MIPS_386DX_33)`|"6.0"|
|`$(MIPS_486SX_25)`|"9.1"|
|`$(MIPS_486DX_33)`|"12.0"|
|`$(MIPS_486DX2_50)`|"18.0"|
|`$(MIPS_486DX2_66)`|"23.8"|
|`$(MIPS_486DX4_100)`|"33.4"|
|`$(MIPS_P75)`|"43.5"|
|`$(MIPS_P100)`|"60"|
|`$(MIPS_MAX)`|"1000"|


### System Properties

When starting a game in DREAMM, all the properties are assembled into a final property set in the following manner:

* Properties from the root of the __[desc](desc-objects)__ object become the starting point.
* Properties from the selected base __[variant](#variant-objects)__ object are applied on top.
* Properties from any __[addon](#variant-objects)__ objects that are part of the current game are applied on top of that.
* Properties from any __[mount](#mount-objects)__ objects that are valid and whose __condition__ is true are applied.
* Properties from any __[config](#config-objects)__ objects that are valid and whose __condition__ is true are applied.
* Properties from the selected __[option](#option-objects)__ objects in each __config__ are applied.

This final set of properties is then queried by the game to control certain parameters of execution.
The list below describes each property, its potential values, and what it controls.

|Property|Meaning|
|:--------:|-------|
|__altkeys__|Optional for all systems. Specifies which Alt keys are used by the game, to prevent DREAMM from using them.  Use `*` to indicate the `F12` key. For example, `altkeys=at1*` tells DREAMM to disable its internal `Alt-A`, `Alt-T`, and `Alt-1` shortcuts, as well as the `F12` key. |
|__drivetypes__|Optional for all systems. Specifies what type of drive is associated with each drive letter. See the [drivetypes](#drivetypes) full description below for details. |
|__fastmovs__|Optional for all systems. Specifies whether to optimize `REP MOVS` operations. This is useful for reducing CPU requirements on some games, but can produce compatibility issues.|
|__fixdivover__|Optional for all systems.  Specifies whether to detect and attempt to fix divide overflow errors. It is not uncommon for older DOS games to hit these when running on systems much faster than they were designed for. |
|__launch__|Required for all systems. Specifies the full command line to execute, including any parameters. Typically this uses the `$(rootpath)` variable as the path. Windows-based games should always use quotes around the path to the executable.|
|__machine__|Required for all systems. Specifies the type of machine to emulate. Supported values are __pc__, __tandy__, or __fmtowns__.|
|__mips__|Required for all systems. Specifies the emulated CPU speed in millions of instructions per second. This value can be fractional.     Rather than putting raw values here, you should use one of the [built-in](#cpu-speed-strings) variables, as these line up with the options presented in the Advanced Configuration menu. Any values of 60 or below automatically put DREAMM into &ldquo;fixed execution mode&rdquo;, which creates more consistent timing. Values above 60 will run in &ldquo;timed execution mode&rdquo;, which has more variability but can run at higher speeds.|
|__nothrottle__|Optional for all systems. Specifies the number of emulated seconds to run without throttling (synchronizing to real time) at startup. This can be used with games that have a long startup time to help reduce the wait. |
|__osenv__|Required for all systems. Specifies the operating system to emulate. Supported values are __dos__, __windows__, or __fmtowns__. The name can be followed by a space and then a version number. For __dos__, the version should be in the form `<major>`.`<minor>`. For __windows__, the version should be `<major>`.`<minor>`.`<build>`; or you can use one of the following shortcuts: __95__, __98__, __98SE__, __Me__, __NT3.1__, __NT3.5__, __NT3.51__, __NT4__, __2000__, __XP__.|
|__rootpath__|Required for all systems. Specifies the path within the virtual filesystem that will be the &ldquo;current directory&rdquo; when launching the game.|

 These properties only apply to __dos__ systems:

|Property|Meaning|
|:--------:|-------|
|__audiohw__|Optional for __dos__ systems. Specifies the audio hardware installed on the system. Supported values are: __speaker__, __cms__, __adlib__, __sb16__, __mt32__, or __gmidi__. Multiple systems can be installed at once by combining with a `+` separator. For example `mt32+sb16`. To enable Tandy/PCjr audio use `machine=tandy`.|
|__dosfiles__|Optional for __dos__ systems. This value is the equivalent of the `CONFIG.SYS` __FILES__ option, and specifies the maximum number of simultaneous open files each DOS program can have. The default value is 20.|
|__dosmem__|Optional for __dos__ systems. Specifies one or more comma-separated memory configuration options for DOS machines. Possible values include: __noems__ to disable the EMS driver, even if more than 1MB of RAM is configured; or __noxms__ to disable the XMS driver, even if more than 1MB of RAM is configured.|
|__gameport__|Optional for __dos__ systems. Specifies one or more comma-separated option overrides for the emulated gameport. Possible values include: __timed__ to force emulated timing (this is the default when running in &ldquo;fixed execution mode&rdquo;; see __mips__ above); __counted__ to just return values based on the number of times the gameport is read; __swapped__ to swap joystick 1 and 2, for games that only talk to joystick 2; __extended__ to replace joystick 2 with additional axes and buttons from joystick 1.|
|__ramkb__|Required for __dos__ and __fmtowns__ systems. Specifies the amount of RAM the emulated system has, in kilobytes. If this value is 1024 or less, DREAMM disables EMS/XMS in DOS programs.|
|__videohw__|Required for __dos__ systems. Specifies the video hardware installed on the system. Supported values are: __hercules__, __cga__, __ega__, __vga__, or __svga__. To represent Tandy/PCjr video modes, use `machine=tandy` and specify __cga__ here.|

These properties only apply to __windows__ systems:

|Property|Meaning|
|:--------:|-------|
|__audiohw__|Optional for __windows__ systems. If present, the only supported option on Windows is __gmidi__, which enables the Windows MIDI system.|
|__locale__|Optional for __windows__ systems. Specifies the system locale when launching a game. Only these locales are currently supported: __en-US__, __de-DE__, __fr-FR__, __es-ES_tradnl__, __it-IT__, __ja-JP__, __zh-CN__, __zh-TW__, __ko-KR__.|
|__maxfps__|Optional for __windows__ systems. Specifies the maximum frame rate for emulated video modes. By default, video modes run at typical refresh rates; however, for some games it may be beneficial to limit this to improve compatibility or reduce CPU load.
|__safedisc__|Optional for __windows__ systems. Specifies the SafeDisc encryption key needed for the given game. To make SafeDisc games work, use an external utility to determine the key, and specify it as a property. Then be sure to include the .EXE and .ICD files in the file manifest.|
|__wincrop__|Optional for __windows__ systems. Specifies whether or not to crop the desktop to the size of the topmost window by default. This can be used to achieve a more focused effect on games which do not take up the full screen.|
|__winres__|Optional for __windows__ systems. Specifies the desktop resolution at startup, in form of `<width>`x`<height>`x`<depth>`. Valid depths are: __8__, __16__, or __32__. If `<depth>` is not specified, it is assumed to be 32. The dimensions specified must match one of the supported video modes: __640x480__, __800x600__, __1024x768__, __1280x960__, __1280x1024__, or __1600x1200__.|


### drivetypes

The __drivetypes__ property specifies one letter per drive indicating the drive type, either __F__ for a floppy drive, __H__ for a hard disk, __C__ for a CD-ROM drive, or any other character to indicate unmapped.

The default value is __FFHC__, which maps A: and B: to floppy disks, C: to a hard disk, and D: to a CD-ROM drive.

After each drive letter, you can optionally include further details in brackets:

* You can specify the reported __capacity__ of the drive for both floppy and hard disks by just including a number followed by either `k` (kilobytes), `m` (megabytes), or `g` (gigabytes). For example, `F[1.2M]` describes a 1.2MB floppy disk, while `H[1GB]` specifies a 1GB hard drive.
* Alternatively, you can specify the exact reported __geometry__ of the drive in `C:H:S` format. For example, `F[40:2:9]` specifies a floppy drive with 40 tracks, 2 sides, and 9 sectors/track (typical 360k floppy). Or `H[1023:5:17]` describes a 42MB hard disk with 1023 cylinders, 5 heads, and 17 sectors/track.
* You can specify the amount of __free space__ on the disk by following the capacity or geometry with a `/` and a number, specified either as a size just like capacity above, or as a percentange. For example `H[500MB/80%]` describes a 500MB hard disk with 80% (400MB) free space remaining, while `F[80:2:18/100k]` describes a 1.44MB floppy with 100k free space.
  </li>
 </ul>


## Object Descriptions

### desc Objects

The __desc__ object is the outermost object and serves to contain all global properties and shared objects.

All __desc__ objects must have a bracketed id.
This id is used as a unique identifier of the desc, and thus must be unique among all files processed.
It is used as the directory name for installs and game deltas, so it should only contain characters that are commonly permitted in filenames.
By convention the id is of the form `<companyid>`-`<gamename>`.

The following `<companyid>` values have been defined:

|id|Company|
|:---:|---|
|has|Hasbro Interactive|
|lec|Lucasfilm Games/LucasArts Entertainment Company|
|lll|Lucas Learning Ltd|
|msft|Microsoft|
|mind|Mindscape|
|pai|Parroty Interactive|
|sol|Sierra Online|
|swt|The Software Toolworks|

In addition to the properties described below, __desc__ objects can hold a number of different objects:

* [__variant__](#variant-objects) objects describe individual game versions, including required files
* [__mount__](#mount-objects) objects describe the virtual filesystem presented to the game
* [__config__](#config-objects) objects describe a set of configuration options for the user to choose from
* [__configutil__](#configutil-objects) objects describe programs the user can launch to perform configuration
* [__warning__](#warning-objects) objects describe warning messages that are displayed to the user
* [__script__](#script-objects) objects describe scripts that can be run during execution
* [__charset__](#charset-objects) objects describe character bitmap patterns for screen scraping

 __desc__ objects recognize the following unique properties:

|Property|Meaning|
|:--------:|-------|
|__filter__|Optional. Specifies a comma-separated set of categories to which the game belongs. This is used for filtering on the main game selection screen.|
|__icon__|Optional. Specifies the filename of the icon to associate with this game. Icons are loaded from the `icon` directory of the DREAMM’s internal file system, and should be 256&times;256 pixel PNG files. If the icon is not found or not specified, a generic icon is substituted. Note that DREAMM groups games on its main selection screen by the icon name provided here, not by desc id.|
|__name__|Required. Specifies the display name of the game. This is reflected in the final [System Properties](#system-properties) as __descname__.|
|_other properties_|Optional. Any other user-specified properties here are valid, and are stored. These can be [System Properties](#system-properties) or user-defined properties (in ALL_CAPS).|


### Variant Objects

Variant objects are actually not called &ldquo;variant&rdquo; in the file.
Rather, __variant__ is an umbrella definition for the following object types: __game__, __demo__, __installer__, __upgrader__, __addon__, and __fragment__.
Below is a description of each type:

* __game__ &mdash; this is the most common variant type, designed to represent a full game. __game__ variants can be paired with one or more __addon__ variants, and can be upgraded with __upgrader__ variants.
* __demo__ &mdash; demos are essentially equivalent to __game__s, but are labelled separately to help DREAMM sort out more &ldquo;important&rdquo; items from a user interface perspective.
* __installer__ &mdash; installers are a mechanism to install __game__s or __demo__s. These are called out separately because DREAMM wants to search for them, but does not expect them to be long-term residents in its game list. Installers should have an __installid__ property to describe the id of the variant it will install.
* __upgrader__ &mdash; upgraders are similar to installers, except that they are applied to an existing installation to install a fix or add new features (like an add-on pack). Instead of an __installid__ property, upgraders must have an __upgradeids__ property that describes which variants can be upgraded.
* __addon__ &mdash; addons are collections of files applied on top of a __game__ or __demo__. Games/demos can have multiple addons applied. If an addon just consists of loose files (for example, if a patch is shipped as a replacement EXE file), then addons can be applied directly. Otherwise, it is typical for an __upgrader__ to apply the patch and then for it to be detected as an addon when the final results are examined.
* __fragment__ &mdash; fragments are just collections of files/properties that are imported via the __imports__ property to assemble another variant type. They are not useful on their own.

All variant objects must have a bracketed id.

Variant objects can contain both [System Properties](#system-properties) and user-defined properties, which take precedence over properties in the __[desc](desc-objects)__ object.
If a __game__ or __demo__ variant has one or more __addon__ variants applied, then the properties in the addons take precedence over properties in the base variant.

Apart from properties, variants must contain one or more __[file](#file-objects)__ objects.
These describe the set of files needed to run the game, and how they should be laid out in the installation directory.

The following properties are defined for variants:

|Property|Meaning|
|:--------:|-------|
|__anchordepth__|Optional. Specifies the number of directory levels above the location of the __anchorfile__ that should be included when searching for a game’s files. If not specified, then DREAMM will start the search in the directory where the __anchorfile__ was found.|
|__anchorfile__|Required. Specifies a single filename that can be used as a quick check to decide whether to do a more thorough scan when installing a game. Typically this should be the most uniquely-named file specified for this variant.|
|__imports__|Optional. Specifies a comma-separated list of other variant ids whose properties and objects you wish to import. Objects defined in the current variant take precedence over imported items. Imports can be used to describe variants that differ from other variants by just a few files. Multiple imports can also be used to assemble a variant from __fragment__s and/or other variants.|
|__installid__|Required for __installer__ variants only. Specifies the id of the variant that will be installed.|
|__name__|Required for all non-__fragment__ variants. Specifies the display name of the variant. This is reflected in the final [System Properties](#system-properties) as __varname__.|
|__upgradeids__|Required for __upgrader__ and __addon__ objects only. Specifies a comma-separated list of variant ids that can be targeted by the upgrade. Each id can be a simple variant id or a combination of multiple variants connected by a `+` separator (e.g., `game+required-addon`). ids can also start with a `!` to indicate that the upgrader cannot target any game with that variant (e.g., `!incompatible-addon`).|
|_other properties_|Optional. Any other user-specified properties here are valid, and are stored. These can be [System Properties](#system-properties) or user-defined properties (in ALL_CAPS), and will override any such properties defined in the containing __[desc](desc-objects)__ object.|


### mount Objects

Mount objects describe the virtual filesystem that is presented to the game.
By default, the virtual filesystem is empty, so at least one __mount__ is required.

Mount objects may have a bracketed id, but it is not required or used.

The virtual filesystem is super flexible, and thus so are __mount__s.
The easiest way to think about it is that for each file access, take the directory where the file lives and work from the first mount toward the last until a mount is found whose virtual path (aka __vpath__) includes the file’s directory.

Note that multiple mounts can cover the same directory.
For example, you could have one mount whose __vpath__ is `C:\`, and a second mount whose __vpath__ is `C:\gamedir`.
If a game queries all the files in `C:\gamedir` then it will include all the files in the first mount under the `gamedir` subdirectory (if it exists), combined with all the files in the root of the second mount.

For read operations &mdash; enumerating files, opening read-only files, querying attributes &mdash; DREAMM will scan all mounts, top-to-bottom, until it finds a match.
For the specific case of enumerating files, DREAMM will continue scanning over all mounts that match; however, in general DREAMM stops at the first match.

For write operations &mdash; creating a new file, creating directories, renaming files, deleting files &mdash; DREAMM will also scan top-to-bottom, but will only consider writable mounts.
For the specific case of opening an existing file in read-write mode, if that file is first found on a non-writable mount, DREAMM will perform a copy-on-write operation to the first matching writable mount.

In general, writable mounts should be listed at the top. Consider these to be &ldquo;catch-all&rdquo; locations for any modifications, allowing the other mounts below it to maintain their integrity.

Here is an example set of mounts:

```
 // the first mount catches all writes to the C: drive in the ~/hd path on the host system
 mount[]: vpath = C:\;         srcpath = ~/hd; writable = true

 // the second mount is a read-only mount that points to the game's installation directory
 // and maps to the directory described by the 'rootpath' property
 mount[]: vpath = $(rootpath); srcpath = /path/to/game/install

 // the third mount is a read-only mount that maps the contents of the cdrom.cue image to
 // the root of the D: drive
 mount[]: vpath = D:\;         srcpath = /path/to/game/cdrom.cue
```

Regardless of operating system, DREAMM always treats the A: and B: drives as floppy discs, the C: drive as a hard disk, and the D: drive as a CD-ROM, unless overridden by the __[drivetypes](#drivetypes)__ property.

 The following properties are defined for mounts:

|Property|Meaning|
|:--------:|-------|
|__condition__|Optional. Specifies the condition under which to process this mount. See [Conditional Objects](#conditional-objects) for more detail.
|__disc__|Optional. Specifies the name of the virtual disc associated with this mount. By default, mounts are visible no matter which disc is selected. However, if you are emulating a multi-disc game, and the game really needs it to appear as multiple discs, you can organize your installation files and mark certain paths as being from certain discs with this property. For an example, see the `lec-grimm.dreamm` file from DREAMM’s internal file system.
|__optional__|Optional. Specifies a Boolean true/false to indicate whether this mount is optional. Normally, if the __srcpath__ cannot be found, DREAMM will fail to launch. Marking a mount as optional means that DREAMM will silently fail and move on to the next mount.
|__srcpath__|Required. Specifies the location (either directory or image file) in your host file system that will map to the __vpath__ in the  al filesystem. If you prefix the name with `ifs:` then the path references DREAMM’s internal file system. If you set this value to __ram__ then DREAMM will create a virtual RAM disk and store all changes there; the contents will be lost when DREAMM exits.
|__subpath__|Optional. Specifies the path within __srcpath__ that will be mapped. This is useful if __srcpath__ points to a disk image, and you want to map a subdirectory within that image.
|__vpath__|Required. Specifies the path in the virtual filesystem where the mount will populate. d></tr>
|__writable__|Optional. Specifies a Boolean true/false to indicate whether this mount is writable. By default, mounts are read-only unless explicitly specified to be otherwise with this property.
|_other properties_|Optional. Any other user-specified properties here are valid, and are stored. These can be [System Properties](#system-properties) or user-defined properties (in ALL_CAPS), and will override any such properties defined in the containing __[desc](desc-objects)__ object and any active __[variant](#variant-objects)__ objects. Note that these properties will not be applied if the mount’s __condition__ was evaluated to be false, or if the mount doesn’t exist and the __optional__ property is set to true.


### config Objects

Config objects allow you to specify user-facing options for a game.
Each config object contains one or more mutually-exclusive __[option](#option-objects)__ objects that provide the specific options for the user to choose from.

All config objects must have a unique (within the containing __[desc](#desc-objects)__) bracketed id.
This identifier is used as the name of the entry that stores the currently selected option in DREAMM’s `config.json` file.

If a config object has only one viable __[option](#option-objects)__ (after evaluating attached conditions), then DREAMM will auto-select that option and hide the configuration option in the user interface.

Below is an example of a config object:

```
 // shows a sound configuration with 3 options: speaker, AdLib, or SoundBlaster 16
 // - the AdLib option is only offered if the ADLIB.DRV file exists
 // - the SoundBlaster 16 option is only offered if the SB.DRV file exists
 // - the default value (with highest priority) is SoundBlaster 16, unless the SB.DRV is missing,
 //    in which case the next highest priority item (AdLib) becomes the default
 // - if both SB.DRV and ADLIB.DRV are missing, only one option remains viable,
 //    and this configuration option will not be shown
 config[sound]:
    title = Sound Configuration
    option[speaker]: name = $(SPEAKER_NAME); audiohw = speaker
    option[adlib]:
       name = $(ADLIB_NAME)
       audiohw = adlib
       priority = 1
       condition = file_exists("$$(rootpath)\\ADLIB.DRV")
    end
    option[sb16]:
       name = $(SB16_NAME)
       audiohw = sb16
       priority = 2
       condition = file_exists("$$(rootpath)\\SB.DRV")
    end
 end
```

The following properties are defined for config objects:

|Property|Meaning|
|:--------:|-------|
|__condition__|Optional. Specifies the condition under which to this config is to be shown. See [Conditional Objects](#conditional-objects) for more detail.
|__title__|Required. The user-friendly name of the configuration option. This is used as the title of the option when displayed to the user in the frontend (though if the title ends with &ldquo;Configuration&rdquo;, that word is automatically removed in some cases for brevity).
|__script__|Optional (and experimental!) If provided, specifies a set of [DREAMMScript](dreammscript.md) functions that populate and respond to selections in a more customizable fashion. The `populate` function is called to generate the set of options to display. See the `lec-xwa.dreamm` for the only existing example of how this works. Since this is experimental, it may change in the future.


### option Objects

Option objects describe a single, mututally-exclusive option within a __[config](#config-objects)__ object.

All option objects must have a unique (within the containing __[config](#config-objects)__) bracketed id.
This identifier is the value stored in `config.json` file for the containing config when this option is selected.

The following properties are defined for option objects:

|Property|Meaning|
|:--------:|-------|
|__condition__|Optional. Specifies the condition under which this option is to be shown. See [Conditional Objects](#conditional-objects) for more detail.
|__name__|Required. The user-friendly description of the option. This is used as the name of the option when displayed to the user in the frontend. In some circumstances, the name may be truncated by removing any parenthetical phrases for brevity.
|__priority__|Optional. If provided, specifies the priority for this option, as an integer. When no valid option is selected, DREAMM will by default choose the option with the highest priority. If multiple valid options are available with the same priority, the one is selected as the default is undefined. The default priority is 0.
|_other properties_|Optional. Any other user-specified properties here are valid, and are stored. These can be [System Properties](#system-properties) or user-defined properties (in ALL_CAPS), and will override any such properties defined in the containing __[desc](desc-objects)__ object, any active __[variant](#variant-objects)__ objects, and any active __[mount](#mount-objects)__ objects. Note that these properties will only be applied if this option is selected as the active one for the containing __[config](#config-objects)__.


### configutil Objects

Configutil objects are used to offer the user a means of configuring the game via an external utility program.
This is useful when a game provides options that don’t easily translate into DREAMM’s limited user interface.

Configutil objects may have a bracketed id, but it is not required or used.

> [!IMPORTANT]
> Because executing a configutil involves executing another program under emulation in the same context as the game, configutil options are only available in the frontend, when the game is not running.

The following properties are defined for configutil objects:

|Property|Meaning|
|:--------:|-------|
|__condition__|Optional. Specifies the condition under which to this configutil is to be shown. See [Conditional Objects](#conditional-objects) for more detail.
|__launch__|Required. The command to execute when the user selects this option.
|__message__|Optional. An optional message to display to the user when launching the configuration utility program.
|__title__|Required. The user-friendly name of the configuration option. This is used as the title of the option when displayed to the user in the frontend (though if the title ends with &ldquo;Configuration&rdquo;, that word is automatically removed in some cases for brevity).
|_other properties_|Optional. Any other user-specified properties here are valid, and are stored. These can be [System Properties](#system-properties) or user-defined properties (in ALL_CAPS), and will override any such properties defined in the containing __[desc](desc-objects)__ object, any active __[variant](#variant-objects)__ objects, and any active __[mount](#mount-objects)__ objects.


### warning Objects

Warning objects provide warning messages to the user that are shown in the frontend.

All warning objects must have a unique (within the containing __[desc](#desc-objects)__) bracketed id.
This identifier is used internally to uniquely identify the warning.

Here is an example warning with an embedded link:

```
warning[update-available]:
    condition = is_game() && $(UPDATE_AVAILABLE)
    text = [[[$(SPACE)
        An update for this game has been released.
        You can *[download it here~900$(SEMI)https://link.to/updater]*.
        To apply the patch, drag the downloaded EXE onto this window.
    ]]]
end
```

The following properties are defined for warning objects:

|Property|Meaning|
|:--------:|-------|
|__condition__|Optional. Specifies the condition under which to this warning is to be shown. See [Conditional Objects](#conditional-objects) for more detail.
|__text__|Required. This is the message to display. Links can be embedded via the (admittedly awkward) syntax of: `*[<text>~900$(SEMI)<link>]*`.


### script Objects

Script objects define scripts that can be executed while the game or installer is running.
These scripts can perform a number of operations, from automating installers to supporting mouse integration to identifying copy protection and much more.

All script objects must have a unique (within the containing __[desc](#desc-objects)__) bracketed id.
This identifier is used internally to uniquely identify the script context.

```
script[autoinstall]:
    condition = is_upgrader()
    code = [[[$(CRLF)
        if (!prop("noautoinstall"))
        {
            input_lockout(true);
            wait("wintitle", "~LucasArts");   // info dialog
            sleep(250); wincontrol_click(1);  // click ok
            input_lockout(false);
        }
    ]]]
```

The following properties are defined for script objects:

|Property|Meaning|
|:--------:|-------|
|__condition__|Optional. Specifies the condition under which to this warning is to be shown. See [Conditional Objects](#conditional-objects) for more detail.
|__code__|Required. This is the code of the script. For a complete description of DREAMM’s scripting language, please see the [DREAMMScript Reference](dreammscript.md). Because scripts are usually longer than a single line, it is common to use the [multi-line](#multi-line-values) mechanism when specifying the code, as seen above.


 <a name='file'></a>
### file Objects

File objects describe the details of all the files required to run a game.
DREAMM uses the list of file objects when scanning for and installing new games.

> [!IMPORTANT]
> Any path specified in the file object’s __name__ field is relative to the installation path.
> This means that the set of file objects should describe the final layout of the files as they will appear the install directory.
> This layout does not necessarily correspond to the layout of the files on the source discs; DREAMM’s search algorithm is responsible for shuffling things to match your desired final layout.

To match a folder or disc image against the current list of supported game variants, DREAMM will scan all the files present looking for a filename that matches the __anchorfile__ property associated with the particular variant.
If this is found, then it will scan that directory and all subdirectories looking for a complete set of files that match the list of file objects.
If the variant specifies an __anchordepth__ greater than 0, then DREAMM starts the scan that many levels up the hierarchy; this allows for using a more unique __anchorfile__ name that exists deeper in the directory structure.

The process of matching files against the list of file objects is fairly involved, but basically boils down to looking for, in order of preference:

1. A file with the same name and matching CRC/size.
2. Any file with a matching CRC/size.
3. A file with the same name but different CRC/size (this is assumed to be a modified or unknown variant).

If no file can be found that satisfies the above requirements, and the file is required, then the match fails.

Below are some examples to show how files are used:

```
 game[test1]:
   // a normal file entry; it will be copied to the install path under the FOO\BAR directory
   file[]: crc = f812ee1e; size =     27688; name = FOO\BAR\FILE1.DAT

   // an optional file; it will be copied if present, but will not fail if not found
   file[]: crc = 6a2882cb; size =    446494; name = FOO\BAR\FILE2.DAT; optional = true

   // a file that must exist but can have any size or any CRC
   file[]: name = FOO\BAR\FILE3.DAT

   // a file that has a bunch of garbage after the first 313536 bytes
   file[]: crc = d00ebfb1; size =    313536; name = FOO\BAR\FILE4.DAT; truncate = true

   // a CDDA description for a disc that has 9 audio tracks
   file[]: name = CDDA.SOU; cdda = 146/143/178/148/145/146/59/79/39
 end

 game[test2]:
   // import properties and file objects from test1 above
   imports = test1

   // this version has a different FILE1.DAT
   file[]: crc = ea0d8d23; size =     30452; name = FOO\BAR\FILE1.DAT

   // this version has FILE2.DAT but it was renamed to FOOBAR2.DAT
   file[]: crc = 6a2882cb; size =    446494; name = FOO\BAR\FOOBAR2.DAT; replace = FOO\BAR\FILE2.DAT

   // this version doesn't need FILE3.DAT
   file[]: remove = FOO\BAR\FILE3.DAT
 end
```

The following properties are defined for file objects:

|Property|Meaning|
|:--------:|-------|
|__cdda__|Optional. Specifies a slash-separated list of track lengths, starting with track 2. This field should only be used with files named `CDDA.SOU`, which represent raw ripped audio tracks. Lengths are specified as seconds, and DREAMM has a fairly generous fuzzy match against the lengths.|
|__crc__|Optional. Specifies the CRC of the file contents, as an 8-digit hex string. If not specified, then any file matching the filename portion of the __name__ property will be considered a potential match.|
|__exactonly__|Optional. Specifies a Boolean true/false value indicating whether DREAMM should consider a file with a matching __name__ only if its CRC and size match exactly the provided __crc__ and __size__ fields. This is for the unusual case where there are multiple files with matching names (for example, two files with the same name but different contents on two separate discs), and provides some guardrails against DREAMM getting these files mixed up during its matching process.|
|__name__|Required, unless the __remove__ property is specified. Specifies the path where the file will be copied to in the final installation folder.|
|__optional__|Optional. Specifies a Boolean true/false value indicating whether the file is required. If an optional file is found, it is copied to the install folder; however, if it is missing, DREAMM will just ignore the file.|
|__remove__|Optional. Specifies a filename that should be removed from the current list of file objects. This is useful when used in conjunction with the __import__ property, when you want to modify the file list of the imported entries to remove a particular file. This property is generally used on its own, as the only property for its file object.|
|__replace__|Optional. Specifies a filename that should be replaced with the current file entry. This works much like __remove__ except that it still expects a __name__ property to specify the replacement filename, along with other properties describing the replacement file.|
|__size__|Optional. Specifies the size of the file, in bytes. If not specified, then any file matching the filename portion of the __name__ property will be considered a potential match.|
|__truncate__|Optional. Specifies a Boolean true/false value indicating whether a truncated version of the file should be considered. This flag is only rarely used, and is specifically for the case where some copy protection mechanisms create fake extra-long files. When present, if DREAMM finds a file whose __name__ matches, and whose size is larger than the specified __size__, it will re-compute the file CRC with only the first __size__ bytes included to see if that produces a match. If it does match, then only the first __size__ bytes are copied.|


## Generic Game Templates

### Generic DOS Template

```
 //
 // Generic DOS launch template
 // search for ### and modify to suit
 //
 desc[generic]:

 //
 // configure these variables
 //
 INSTALL_PATH = C:\GAME    ### the virtual path where the game files should appear to be installed
 EXE_NAME = GAME.EXE       ### the name of the game's executable (can be a .COM or .BAT file as well)
 HOST_INSTALL_PATH = .     ### the path on your host system where the files really live
 HOST_DELTA_PATH = ram     ### the path on your host system where changes should go, or 'ram' to ignore
 HOST_CDROM_PATH = $(NULL) ### the path on your host system to the CD-ROM image, or $(NULL) if none

 //
 // system properties
 //

 // friendly name for game
 name = ### My Game's Name

 // regular PC running DOS
 machine = pc
 osenv = dos

 // rootpath is the "current directory" when launching
 rootpath = $(INSTALL_PATH)

 // we presume that the EXE lives in the rootpath
 launch = $(rootpath)\$(EXE_NAME)

 // automatically fix divide overflow errors
 fixdivover = true

 // set the CPU speed; see the list of options above
 mips = $(MIPS_P100)

 // RAM size; default is 16MB
 ramkb = 16384

 // audio hardware; specify speaker, cms, adlib, sb16, mt32, or gmidi,
 // or a combination like sb16+mt32
 audiohw = sb16

 // video hardware; specify hercules, cga, mcga, ega, vga, or svga
 videohw = svga

 //
 // environment configuration
 //
 script[env]:
     code = [[[$(CRLF)
         // use this to configure any environment variables
         // env_set("VARIABLE", "VALUE");
     ]]]
 end

 //
 // filesystem
 //
 mount[]: vpath = C:\; srcpath = $(HOST_DELTA_PATH); writable = true
 mount[]: vpath = $(rootpath); srcpath = $(HOST_INSTALL_PATH)
 mount[]: vpath = D:\; srcpath = $(HOST_CDROM_PATH); condition = "$(HOST_CDROM_PATH)" != ""

 //
 // dummy game description
 //
 game[generic]:
     name = generic

     // the anchorfile and file objects don't matter unless you're setting
     // it up for DREAMM to formally install the game
     anchorfile = DUMMY.TXT
     file[]: name = DUMMY.TXT
 end

 end
```
