<!doctype html>
<html lang='en-US' xmlns:og='http://opengraphprotocol.org/schema/' xmlns:fb='http://www.facebook.com/2008/fbml'>
<head>
<!--#set var="title" value="DREAMM Developer Documentation" -->
<!--#set var="description" value="Documentation page for developers to extend DREAMM." -->
<!--#set var="url" value="https://dreamm.aarongiles.com/docs/" -->
<!--#include virtual="/common-header.html" -->
<style>
body { line-height:140% }
ul+h3,ul+a+h3 { margin-top:30px }
h4 { border-top:2px dotted; padding-top:20px }
div.capimg img { border:none; }
div.subtopics { font-size:90%; line-height:120%; padding-left:20px; margin-bottom:6px }
div.subtopic { display:inline-block; word-break:keep-all; white-space:nowrap; hyphens:none }
div.subtopic::after { content:"\00a0\00a0•\00a0" }
div.subtopic:last-child::after { content:none }
div.subtopic a { text-decoration:none; font-style:italic }
h3 { padding-top:30px; margin-top:30px; margin-left:-20px; margin-right:-20px; padding-left:20px; padding-right:20px; border-top:3px solid #030742 }
table { margin:0 auto; border-collapse:collapse }
td,th { border:1px solid #a13223; padding:2px 10px }
th { color:#a13223 }
@media all and (max-width:600px)
{
div.subtopic { display:block; word-break:keep-all; white-space:normal; hyphens:none; padding-left:20px; margin-left:-20px }
div.subtopic::after { content:none }
}
ol li { margin-bottom:5px }
.fixed { hyphens:none }
.indented
{
    margin:20px 20px;
	font-family:monospace;
	font-size:80%;
    line-height:120%;
	background-color:#efefef;
	padding:5px 10px;
	border-radius:5px;
	border:1px solid #03063A;
    white-space:pre-wrap;
}
ol.spaced li,ul.spaced li { padding-bottom:15px }
ol.spaced li:last-child,ul.spaced li:last-child { padding-bottom:0 }
ol.spaced .indented,ul.spaced .indented { margin-top:5px }
ol.spaced ul,ol.spaced ol,ul.spaced ul,ul.spaced ul { margin-top:5px }
ol.spaced ul li,ol.spaced ol li,ul.spaced ul li,ul.spaced ol li { padding-bottom:0 }
</style>
</head>

<body lang='en' class='winbody'>
<div class='page'>
 <div class='logobg'>
  <div class='center'>
   <a href='/'><img src='/img/dreamm-logo.png'></a>
  </div>
 </div>

 <h2 class='center'>DREAMM Developer Documentation</h2>

 <p>
 This page is the starting point for learning about how DREAMM works and how to extend it by creating your own .dreamm files and DREAMMPacks.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>

 <h3>Contents</h3>

 <p>
 There’s a lot to go over, so let’s break things up like this:
 </p>

 <ul>
  <li>
   <a href='#intro'>Introduction</a>
   <div class='subtopics'>
    <div class='subtopic'><a href='#vision'>The DREAMM Vision</a></div>
    <div class='subtopic'><a href='#limits'>DREAMM Limitations</a></div>
    <div class='subtopic'><a href='#expanding'>Expanding Support</a></div>
   </div>
  </li>
  <li>
   <a href='#internals'>DREAMM Internals</a>
   <div class='subtopics'>
    <div class='subtopic'><a href='#ifs'>DREAMM’s internal filesystem</a></div>
    <div class='subtopic'><a href='#gameids'>How game IDs are used</a></div>
    <div class='subtopic'><a href='#dreammpacks'>How DREAMM finds DREAMMPacks</a></div>
    <div class='subtopic'><a href='#environment'>How a game’s environment is built</a></div>
    <div class='subtopic'><a href='#properties'>How properties are stacked</a></div>
    <div class='subtopic'><a href='#scanning'>How file scanning works</a></div>
    <div class='subtopic'><a href='#installers'>How game installers are run</a></div>
    <div class='subtopic'><a href='#rawdisk'>How raw disk access is handled</a></div>
    <div class='subtopic'><a href='#cdda'>How CD audio is handled</a></div>
    <div class='subtopic'><a href='#syscalls'>How Windows system calls are handled</a></div>
   </div>
  </li>
  <li>
   <a href='#debugging'>Debugging in DREAMM</a>
   <div class='subtopics'>
    <div class='subtopic'><a href='#info'>Live Information</a></div>
    <div class='subtopic'><a href='#logging'>Logging</a></div>
    <div class='subtopic'><a href='#debugger'>Interactive Debugger</a></div>
    <div class='subtopic'><a href='#debugtips'>Debugging Tips</a></div>
   </div>
  </li>
  <li>
   <a href='#tips'>Tips and Tricks</a>
   <div class='subtopics'>
    <div class='subtopic'><a href='#misctips'>Miscellaneous Tips</a></div>
    <div class='subtopic'><a href='#conventions'>.dreamm File Conventions</a></div>
   </div>
  </li>
  <li>
   <a href='#fileformats'>File Formats</a>
   <div class='subtopics'>
    <div class='subtopic'><a href='#floppy-track'>Floppy track files</a></div>
    <div class='subtopic'><a href='#cdda-sou'>CDDA.SOU</a></div>
   </div>
  </li>
  <li>
   <a href='#tutorials'>Tutorials</a>
   <div class='subtopics'>
    <div class='subtopic'><!--<a href='tutorial-b1.html'>-->Tutorial B1: A PC booter game<!--</a>--></div>
    <div class='subtopic'><!--<a href='tutorial-b1.html'>-->Tutorial B2: A multi-disk PC booter game<!--</a>--></div>
    <div class='subtopic'><!--<a href='tutorial-d1.html'>-->Tutorial D1: A basic DOS game<!--</a>--></div>
    <div class='subtopic'><!--<a href='tutorial-d2.html'>-->Tutorial D2: A CD-ROM based DOS game<!--</a>--></div>
    <div class='subtopic'><!--<a href='tutorial-d3.html'>-->Tutorial D3: A DOS game with an installer<!--</a>--></div>
    <div class='subtopic'><!--<a href='tutorial-w1.html'>-->Tutorial W1: A basic Windows game<!--</a>--></div>
    <div class='subtopic'><!--<a href='tutorial-w2.html'>-->Tutorial W2: A CD-ROM based Windows game<!--</a>--></div>
    <div class='subtopic'><!--<a href='tutorial-w3.html'>-->Tutorial W3: A Windows game with an installer<!--</a>--></div>
   </div>
  </li>
  <li>
   <a href='#deeper'>Deeper Dives</a>
   <div class='subtopics'>
    <div class='subtopic'><a href='dreamm.html'>.dreamm File Specification</a></div>
    <div class='subtopic'><a href='dreammscript.html'>DREAMMScript Reference</a></div>
    <div class='subtopic'><a href='scraping.html'>DREAMM Screen Scraping</a></div>
   </div>
  </li>
 </ul>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>

 <a name='intro'></a>
 <h3>Introduction</h3>

 <p>
 DREAMM was originally created as a DOS emulator for the core set of LucasArts’ SCUMM games.
 </p>
 <p>
 Since then, it has evolved in many different directions, and as of version 4.0 can support the entire catalog of DOS, FM-Towns, and Windows games released by all the Lucas-related companies between 1980 and 2000.
 </p>
 <p>
 After the release of DREAMM 4.0, the question was naturally: what’s next?
 </p>
 <p>
 To figure this out, I decided it was time to articulate DREAMM’s vision.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>

 <a name='vision'></a>
 <h4>The DREAMM Vision</h4>

 <p>
 DREAMM’s goal is to bring high-quality, period-accurate emulation of DOS, Windows, and FM-Towns games to modern platforms in a user-friendly manner.
 </p>

 <p>
 Because DREAMM’s special sauce is the user-friendly aspect of its vision, it’s worth setting more specific goals for this aspect:
 </p>

 <ol class='spaced'>
  <li>
   <b>Ease of installation</b>: installing a supported game should be as easy as drag & drop
   <ul>
    <li>all known official versions of games and demos across all languages should be detected and supported</li>
    <li>all known official updaters should be detected and supported</li>
    <li>modern re-releases from GOG, Steam, and Amazon should be detected and supported</li>
    <li>game installers should either by bypassed or automated to do the right thing</li>
   </ul>
  </li>
  <li>
   <b>Ease of use</b>: games should be playable immediately after install with little friction
   <ul>
    <li>any command-line or external configuration should be mapped to DREAMM’s user interface</li>
    <li>all supported video/sound configurations should be available, even if originally selected at install time</li>
    <li>manual-based copy protection should be automatically detected and solved</li>
   </ul>
  </li>
 </ol>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>

 <a name='limits'></a>
 <h4>DREAMM Limitations</h4>

 <p>
 As I described in my old <a href='https://www.youtube.com/watch?v=Mvg4ewYMsPU' target='_blank'>California Extreme</a> talk about how not to burn out on a project, it’s also important to know what your project is <i>not</i> about.
 </p>

 <p>
 To this end, here are a list of self-imposed limits on what DREAMM will not support.
 </p>

 <ul class='spaced'>
  <li>
   <b>Operating systems</b>: only games that were designed to run on these systems will be supported
   <ul>
    <li>IBM PC Booters</li>
    <li>PC/MS-DOS (any version)</li>
    <li>16-bit Windows 3.x</li>
    <li>32-bit Windows 95/98/Me or Windows NT/2000</li>
    <li>FM-Towns</li>
   </ul>
  </li>
  <li>
   <b>Hardware and OS requirements</b>: only games that meet the following criteria will be supported
   <ul>
    <li>games must support running on a single CPU</li>
    <li>games must run well on Pentium 2 hardware or lower </li>
    <li>Windows games must not require DirectX 8 or later</li>
   </ul>
  </li>
  <li>
   <b>Official releases</b>: only official releases will be supported
   <ul>
    <li>cracked versions are not supported (unless they were sold by the original company)</li>
    <li>later community enhancements or tweaks are explicitly out of scope (with rare exceptions)</li>
    <li>no graphical enhancements (like higher resolutions or 16:9 support)</li>
   </ul>
  </li>
 </ul>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>

 <a name='expanding'></a>
 <h4>Expanding Support</h4>

 <p>
 With DREAMM’s goals and non-goals now spelled out, it is time to revisit the question of what’s next after 4.0?
 </p>
 <p>
 It seemed pretty obvious that either DREAMM could continue to support only Lucas-related games, or I could expand its scope outward to apply DREAMM’s unique user friendliness to games from other companies.
 Ultimately I opted for the latter.
 </p>
 <p>
 However, there is a ton of effort involved in creating a user-friendly experience that meets all of DREAMM’s goals:
 </p>

 <ul>
  <li>Tracking down all released versions of games in all languages</li>
  <li>Creating descriptions identifying all required files for each game</li>
  <li>Testing all the games and identifying any issues</li>
  <li>Automating the installation of games that require installers</li>
  <li>Writing scripts to identify and solve manual-based copy protection</li>
  <li>Creating icons for all the games</li>
 </ul>

 </p>
 If DREAMM is to expand its scope, this effort would be way more than could be managed by a single person.
 </p>
 <p>
 The solution to this is, of course, to open up DREAMM’s internal formats and structures so that outside contributors can extend DREAMM’s support to other games.
 </p>
 <p>
 This process has involved cleaning up and formalizing a lot of DREAMM’s internals, extending support in several directions, and writing this documentation, so that the community can produce their own &ldquo;DREAMMPacks&rdquo; that add support for new games to DREAMM.
 </p>
 <p>
 My hope is that the best of these packs will tick all the ease-of-use boxes described in my <a href='#vision'>vision</a>, and that many of them will be either directly bundled with DREAMM or at the very least, be easily installable with low friction in future releases.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>

 <a name='internals'></a>
 <h3>DREAMM Internals</h3>

 <p>
 In order to develop a DREAMMPack, it can be useful to understand how DREAMM operates internally.
 The sections below spell out several of the more complicated processes in DREAMM so that hopefully DREAMM’s behavior will make sense.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>

 <a name='ifs'></a>
 <h4>DREAMM’s internal filesystem</h4>

 <p>
 DREAMM bundles within itself (or, on macOS, within the application package), a bunch of files that contains various internals.
 </p>
 <p>
 These files are packaged in a standard ZIP container, and can be referenced by several command-line functions using the special filename <span class='fixed'>:ifs</span>:
 </p>
 <p class='indented'>
 // list the files in the internal filesys
 dreamm -dir :ifs

 // extract the contents of the internal filesys
 dreamm -extractall <i>&lt;path&gt;</i> :ifs
 </p>
 <p>
 DREAMM always looks for these files in the <span class='fixed'>dreammifs</span> directory next to the application before referencing the internal ZIP container, so you can effectively override anything contained within the internal filesystem by creating a directory named <span class='fixed'>dreammifs</span> next to the application and laying down replacement files in the appropriate location.
 </p>
 <p>
 Below is a description of what’s contained within each top-level directory:
 </p>

 <ul class='spaced'>
  <li><b>dreammpack</b>:
   This is just the contents of DREAMM’s internal DREAMMPack, with all the Lucas-related games.
   If you are developing your own DREAMMPacks it might be useful to extract these and examine them to see how things work.
  </li>
  <li><b>shader</b>:
   This directory contains both GLSL and HLSL versions of the shaders that DREAMM uses for rendering to the screen in OpenGL and Direct3D modes, respectively.
  </li>
  <li><b>sounds</b>:
   This directory holds sound effects used during floppy emulation.
  </li>
  <li><b>ui</b>:
   This directory holds fonts and images used in DREAMM’s user interface.
  </li>
  <li><b>windows</b>:
   This directory holds DLL stubs and fonts referenced by Windows programs.
   It is mapped to <span class='fixed'>C:\WINDOWS</span> when running Windows programs.
  </li>
 </ul>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>

 <a name='gameids'></a>
 <h4>How game IDs are used</h4>

 <p>
 DREAMM internally uses &ldquo;game IDs&rdquo; to represent a specific combination of variants that describes a game.
 A game ID is generally written as:
 </p>

 <p class='indented'><i>descid</i>:<i>variantid</i>[+<i>addonid</i>...]</p>

 <p>
 where <b>descid</b> is the object ID of the enclosing <b>desc</b> object, <b>variantid</b> is the object ID of the primary game/&#8203;demo/&#8203;installer/&#8203;updater <b>variant</b> object, and the optional <b>addonid</b>s are the object IDs of any <b>addon</b> variants that have been applied to the game.
 </p>

 <p>
 A colon is typically used to separate the <b>descid</b> from the <b>variantid</b>, and plus symbols are used between the <b>variantid</b> and each <b>addonid</b>.
 </p>

 <p>
 In the installation and game delta directories, DREAMM switches the colon for a directory separator, but otherwise stores the data where you would expect:
 </p>

 <p class='indented'><i>install-directory</i>/<i>descid</i>/<i>variantid</i>[+<i>addonid</i>...]
  <i>delta-directory</i>/<i>descid</i>/<i>variantid</i>[+<i>addonid</i>...]</p>

 <p>
 In DREAMM’s current implementation, there can be a maximum of 4 addon variants in a game ID.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>

 <a name='dreammpacks'></a>
 <h4>How DREAMM finds DREAMMPacks</h4>

 <p>
 At startup, DREAMM searches for and loads DREAMMPacks.
 Here is the process explained in detail:
 </p>

 <ul>
  <li>Scan dreammpacks folder for dreammpacks; for each item in the directory:
   <ul>
    <li>If the item is a file with a <span class='fixed'>.dreammpack</span> extension, open it as a ZIP file</li>
    <li>Look for a <span class='fixed'>pack.dreamm</span> file inside the item; if not found or invalid, move on to the next item</li>
    <li>Scan the <span class='fixed'>dreamms/</span> directory; for each .dreamm file found:
     <ul>
      <li>Add the timestamp of the file to a running timestamp checksum</li>
      <li>Load file into memory</li>
      <li>Find all <span class='fixed'>$(include *)</span> instances, load and insert contents</li>
      <li>Parse the combined result; for each <b>desc</b> object found:
       <ul>
        <li>Parse variants and other objects</li>
        <li>Finalize variants; for each <b>variant</b> (game/&#8203;demo/&#8203;installer/&#8203;upgrader/&#8203;addon/&#8203;fragment) object:
         <ul>
          <li>Process the <b>imports</b> property; if found, process each import in reverse order:
           <ul>
            <li>Recursively finalize the import if it hasn’t already been</li>
            <li>For each file defined by the imported variant:</li>
            <ul>
             <li>If the file doesn’t exist in our list, add it to the list for this <b>variant</b></li>
            </ul>
            <li>For each property defined by the imported variant:</li>
            <ul>
             <li>If the property isn’t already defined, import it into the properties for this <b>variant</b></li>
            </ul>
           </ul>
          </li>
          <li>For each property defined by the <b>desc</b> object:</li>
          <ul>
           <li>If the property isn’t already defined, import it into the properties for this <b>variant</b></li>
          </ul>
          <li>Validate the final result</li>
          </li>
         </ul>
        </li>
       </ul>
      </li>
     </ul>
    </li>
   </ul>
  </li>
  <li>If the running timestamp checksum doesn’t match the value at the top of the <span class='fixed'>.fastanchors.txt</span> file:
   <ul>
    <li>Generate a new <span class='fixed'>.fastanchors.txt</span> file</li>
    <li>For each <b>variant</b> in each loaded .dreamm file in each loaded DREAMMPack:
     <ul>
      <li>Find the <b>anchorfile</b> property and separate the filename portion</li>
      <li>Add the filename to a list, with the name of the .dreamm file</li>
     </ul>
    </li>
    <li>Write a new <span class='fixed'>.fastanchors.txt</span> file, containing filenames and the list of .dreamm files that use it as an anchor</li>
   </ul>
  </li>
  <li>Build master list of games, using the <b>icon</b> property to group games</li>
 </ul>

 <p class='important'>
 <b>Note:</b> The purpose of the <span class='fixed'>.fastanchors.txt</span> file is to avoid loading all DREAMMPacks and .dreamm files when DREAMM is launched.
 At startup, DREAMM normally scans the executable directory to see if it contains a game to auto-launch.
 (If you specify the <span class='fixed'>-run</span> command it scans that directory instead.)
 This directory is quickly scanned for a match against the <span class='fixed'>.fastanchors.txt</span> list, and then only .dreamm files whose <b>anchorfile</b>s are present in the directory are considered.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>

 <a name='environment'></a>
 <h4>How a game’s environment is built</h4>

 <p>
 When you select a game in the Game Manager, and when you actually launch a game, DREAMM builds an &ldquo;environment&rdquo; for the game to run.
 This environment is an assemblage of all the game’s properties, combined with user-selected options, and the creation of the game’s virtual filesystem.
 </p>
 <p>
 Here’s a detailed look at the exact steps used to build the game’s environment:
 </p>

 <ol>
  <li>Begin with the properties for the <b>variant</b>
   <ul>
    <li>Note that these have already been combined with those from its <b>import</b>s and from the containing <b>desc</b></li>
   </ul>
  </li>
  <li>
   For each <b>addon</b> that is part of the current game:
    <ul>
     <li>Apply and override properties from the addon</li>
    </ul>
  </li>
  <li>Apply and override properties specified on the command line</li>
  <li>Determine values for <span class='fixed'>ifsinstallpath</span>, <span class='fixed'>ifsdeltapath</span>, and <span class='fixed'>ifsupgradepath</span> variables</li>
  <li>Create an empty virtual filesystem for the game</li>
  <li>For each <b>mount</b> that passes its condition:
   <ul>
    <li>Create a new virtual filesystem from <b>srcpath</b>; if error, either fail or ignore based on <b>optional</b></li>
    <li>Mount the new filesystem into game’s filesystem at <b>vpath</b></li>
    <li>Apply and override properties from the <b>mount</b></li>
   </ul>
  </li>
  <li>For each <b>config</b> that passes its condition: (done after mounts so that <span class='fixed'>file_exists()</span> can work)
   <ul>
    <li>Determine which <b>option</b>s pass their condition</li>
    <li>Find the user-selected option, or revert to highest priority default if not valid</li>
    <li>Apply and override properties from the <b>option</b></li>
   </ul>
  </li>
  <li>If the <b>audiohw</b> property contains mt32 or gmidi, create an option for selecting output</li>
  <li>Create the advanced options for mips and fps overrides</li>
  <li>Find the user-selected values for mips and fps overrides, and apply if present</li>
  <li>Resolve final <b>osenv</b></li>
  <li>Resolve final <b>videohw</b></li>
  <li>Resolve final <b>audiohw</b></li>
  <li>Select first disc (if multiple)</li>
 </ol>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>

 <a name='properties'></a>
 <h4>How properties are stacked</h4>

 <p>
 Properties are an important concept in .dreamm files, and it is important to understand how they are assembled into the final set of properties that the game sees.
 </p>

 <p>
 If you’ve read the sections above, you’ll have noted that properties are built up both at <a href='#dreammpacks'>DREAMMPack loading time</a> as well as at <a href='#environment'>environment building time</a>.
 </p>

 <p>
 This section combines the two into a single conceptual model to help understand how the final set is built.
 Properties farther down the list overwrite/override any properties defined earlier:
 </p>

 <ol>
  <li>Start with the properties defined in the <b>desc</b> object</li>
  <li>If the base <b>variant</b> (game/&#8203;demo/&#8203;installer/&#8203;upgrader) has an <b>import</b> property, then for each import in left-to-right order:
   <ul>
    <li>Compute the properties of that variant and apply those properties</li>
   </ul>
  </li>
  <li>Apply the properties defined in the base <b>variant</b> object</li>
  <li>If the game has <b>addon</b>s, the for each addon:
   <ul>
    <li>Compute the properties of that <b>addon</b> and apply those properties</li>
   </ul>
  </li>
  <li>Apply any properties specified on the command line</li>
  <li>For each <b>mount</b> that passes its condition:
   <ul>
    <li>Apply the properties from the <b>mount</b></li>
   </ul>
  </li>
  <li>For each <b>config</b> that passes its condition:
   <ul>
    <li>Apply the properties from the <b>option</b></li>
   </ul>
  </li>
 </ol>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>

 <a name='scanning'></a>
 <h4>How file scanning works</h4>

 <p>
 When files or folders are dragged onto the DREAMM Game Manager window for installation, DREAMM tries its best to match then against all the games and variations described in the loaded DREAMMPacks.
 This process is fairly complex and has some nuances that are important to understand if you’re struggling to get your games to detect, so here is a detailed explanation of the process.
 </p>
 <ul>
  <li>First, the items are sorted into three different categories: <b>directories</b>, <b>archives</b>, and <b>loose files</b>.</li>
  <li>Next, each item in the <b>directories</b> category is scanned individually.</li>
  <li>If nothing is found, then all the items in the <b>archives</b> category are scanned together as a unit.</li>
  <li>If nothing is found, then all the <b>loose files</b> are scanned together as a unit.</li>
  <li>If nothing is found, then all the <b>directories</b> are scanned together as a unit.</li>
 </ul>
 <p>
 The term &ldquo;scanned together as a unit&rdquo; is important here.
 When multiple items are &ldquo;scanned together as a unit&rdquo;, that means that they are (conceptually) put together in the same directory and scanned as if they all coexisted together.
 This combining also preserves multiple files with the same name but different contents.
 </p>
 <p>
 Next up is the actual scanning process:
 </p>
 <ul>
  <li>First, a complete <b>file list</b> of everything in the directory/archive is built.
   <ul>
    <li>If a known archive format is found, it is (conceptually) expanded into an <span class='fixed'>.expanded</span> directory next to the archive</li>
   </ul>
  <li>Then, if we haven’t yet done so, create an <b>expanded variant list</b> (EVL):
   <ul>
    <li>For each <b>variant</b> defined in each loaded .dreamm file within each loaded DREAMMPack:
     <ul>
      <li>If the <b>variant</b> is a game, demo, installer, or upgrader, add it to the EVL</li>
      <li>For <b>game</b> variants, find any <b>addon</b> variants that can be applied and:
       <ul>
        <li>Create a new entry in the EVL representing that pairing of game + addon</li>
        <li>Recursively find any other <b>addon</b> variants that can be applied and add them</li>
       </ul>
      </li>
     </ul>
    </li>
   </ul>
  </li>
  <li>Next, for each entry in the <b>expanded variant list</b>:
   <ul>
    <li>If the file list for this EVL entry consists of a single file:
     <ul>
      <li>Check for a CRC match against files in the <b>file list</b></li>
      <li>If found, add the EVL to the list of matches and move on</li>
      <li>If not found, and the EVL entry is an <b>installer</b> or <b>upgrader</b>, then consider it not found and move on</li>
      <li>Otherwise, continue searching below</li>
     </ul>
    </li>
    <li>See if the EVL’s <b>anchorfile</b> is present in the <b>file list</b>; if not, move on</li>
    <li>Scan the directory where the anchorfile was found for all the other files needed
     <ul>
      <li>Files are identified either by a name match, or by a CRC match for <b>exactonly</b> entries</li>
     </ul>
    </li>
    <li>If not all required files were found, and the variant has a non-zero <b>anchordepth</b>:
     <ul>
      <li>Scan the directory above where the anchorfile was found</li>
      <li>If not found, repeat up to <b>anchordepth</b> times, moving up the directory one level each time</li>
     </ul>
    </li>
    <li>If not all required files were found at this point, move on
    <li>Otherwise, add the EVL entry and the directory to a list of possibilities</li>
   </ul>
  </li>
  <li>For each directory where a possibility was found:
   <ul>
    <li>For each EVL possibility found in that directory:
     <ul>
      <li>Scan the directory tree for all required files, checking CRC</li>
      <li>Compute the number of exact matches vs. the number of mismatches</li>
     </ul>
    </li>
    <li>Include any 100% perfect matches in the final list</li>
    <li>If no 100% matches, find the EVL with the best ratio of matches to mismatches and add that to the final list</li>
   </ul>
  </li>
 </ul>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>

 <a name='installers'></a>
 <h4>How game installers are run</h4>

 <p>
 When scanning for files, if all the files for an <b>installer</b> variant are found, it is treated as an installer that can install a supported game.
 In this case, DREAMM runs the installer as part of the scanning process.
 </p>

 <p>
 Running an installer is pretty much the same as running a game: properties are accumulated, mounts are created, scripts are compiled and run, etc. just like the full game, unless they are conditionalized with <span class='fixed'>!is_installer()</span>.
 However, there are a few key differences.
 </p>

 <p>
 First, the installer’s files are copied to a temporary directory named <span class='fixed'>~installtemp</span> in the DREAMM installation directory. The <span class='fixed'>ifsinstallpath</span> is then set to point to that directory.
 </p>

 <p>
 Second, a fresh new temporary directory named <span class='fixed'>~installdata</span> is created next to the install folder, and the <span class='fixed'>ifsdeltapath</span> variable is set to point to it.
 </p>

 <p>
 Then the installer is run.
 Presumably, the installer accepts configuration (ideally this is automated) and writes a bunch of files, which are redirected to the temporary <span class='fixed'>~installdata</span> folder.
 </p>

 <p>
 When the installer exits, DREAMM then performs a <a href='#scanning'>file scan</a> looking for game matches.
 This works just like a normal scan, except that DREAMM internally acts as if the game has an <b>anchordepth</b> set to 10, so that once an <b>anchorfile</b> is found, DREAMM scans the entire filesystem looking for files.
 </p>

 <p class='important'>
 <b>Note:</b> Because of the way DREAMM scans the resulting directory, if the game requires CD audio tracks or raw floppy/CD sectors, you must include the appropriate <span class='fixed'>file[]</span> object in both the installer and the final game. This causes DREAMM to extract the audio when setting up the installer, and then it will find the resulting <span class='fixed'>CDDA.SOU</span> when doing the final scan.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>

 <a name='rawdisk'></a>
 <h4>How raw disk access is handled</h4>

 <p>
 Since DREAMM is fundamentally filesystem-based, it requires some kludges to handle games that directly access raw sectors on floppy disks or CD-ROMs.
 </p>

 <p>
 When information queries at the drive level are received, DREAMM uses the <a href='dreamm.html#drivetypes'>drivetypes</a> property to report things like drive geometry and available free space.
 </p>

 <p>
 If the game attempts to directly read sectors from a floppy or CD-ROM drive (generally for copy protection purposes), it is handled as described below.
 </p>

 <h5>Raw Floppy Disk Access</h5>

 <p>
 For floppy disks, if a game attempts to read a sector directly via the floppy controller or INT 13h, DREAMM will first attempt to load a file named <span class='fixed'><i>drive</i>:\FTRACK<i>TT</i>SIDE<i>S</i>.~RAW~</span>, where <i>drive</i> represents the drive letter of the floppy, <i>TT</i> represents a 2-digit track number (usually 0-39 or 0-79), and <i>S</i> represents the side, either 0 or 1.
 If that file exists (and is <a href='#floppy-track'>in the correct format</a>), DREAMM will attempt to extract the requested sector from it and return the result.
 </p>
 <p>
 If the above file is not found, then DREAMM searches all <b>mount</b>s that have a <b>vpath</b> pointing to the drive in question to see if one of them is a floppy disk image.
 If one is found, then the track and sector and loaded from the floppy image.
 </p>
 <p>
 If neither a file nor a disk image is found, then DREAMM will synthesize an empty floppy track, formatted with 18 512-byte sectors filled with the standard &ldquo;empty&rdquo; fill byte of 0xF6.
 </p>

 <p>
 If a game attempts to directly write to a floppy sector, DREAMM will first load the track as described above, and then update the appropriate sector with new data.
 It will then write the result out as a file named <span class='fixed'><i>drive</i>:\FTRACK<i>TT</i>SIDE<i>S</i>.~RAW~</span>.
 Typically, this will go to the delta path, keeping any changes separate from the originally installed floppy data.
 </p>

 <h5>Raw CD-ROM Access</h5>

 <p>
 For CD-ROMs, if the game attempts to read a sector directly via the MSCDEX driver, DREAMM will first attempt to load a file named <span class='fixed'><i>drive</i>:\CDSECTOR<i>NNNNNN</i>.~RAW~</span>, where <i>drive</i> represents the drive letter of the CD-ROM and <i>NNNNNN</i> is the 6-digit LBA sector number requested.
 If that file exists, DREAMM reads 2048 bytes from it and returns the data.
 </p>
 <p>
 If the above file is not found, then DREAMM searches all <b>mount</b>s that have a <b>vpath</b> pointing to the drive in question to see if one of them is a CD-ROM image.
 If one is found, then the sector is loaded from the CD-ROM image and returned.
 </p>
 <p>
 If neither a file nor a disk image is found, then DREAMM will return an error to the read request.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>

 <a name='cdda'></a>
 <h4>How CD audio is handled</h4>

 <p>
 Again, since DREAMM is filesystem-based, CD-ROM audio needs to be handled as yet another special case.
 In this case, DREAMM is quite flexible in the ways it can simulate and manifest audio tracks.
 </p>

 <p class='important'>
 <b>Note:</b> Since games that play CD audio generally need to read the TOC (table of contents) of the CD, DREAMM is set up to synthesize a TOC based on the audio tracks available.
 The rules for synthesizing the TOC are explained at the end of this section.
 </p>

 <h5>Finding the audio</h5>

 <p>
 When the first audio request or TOC request comes in, DREAMM looks around for audio data in the following order:
 </p>

 <ol>
  <li>If a file named <span class='fixed'><i>drive</i>:\CDDA.SOU</span> is found in the <a href='#cdda-sou'>appropriate format</a>, audio comes from that.</li>
  <li>If a folder named <span class='fixed'><i>drive</i>:\~OGGMUSIC</span> is found, it is searched for .ogg-encoded audio tracks.</li>
  <li>If a folder named <span class='fixed'><i>drive</i>:\~MP3MUSIC</span> is found, it is searched for MP3-encoded audio tracks.</li>
  <li>If any <b>mount</b> that maps to <i>drive</i> is found whose <b>vpath</b> points to a CD-ROM image, the audio tracks can be read from there.</li>
  <li>If nothing is found, then the CD is treated as having just a single data track with no audio.</li>
 </ol>

 <p>
 For the OGG/MP3-based options, tracks can be named pretty much anything as long as they have the track numbers embedded in the filename at the same position consistently.
 </p>

 <h5>Building the TOC</h5>

 <p>
 If audio comes from the CDDA.SOU file or from a CD image, the positions of the tracks are explicitly spelled out, so synthesizing a TOC is straightforward.
 </p>
 <p>
 For the OGG/MP3-based case, DREAMM creates a fake TOC by assuming that track 2 starts at the 5 minute position, and then each subsequent track starts afterwards, with a 2 second gap between each track.
 </p>
 <p>
 If more precision is required, you can put a <span class='fixed'>TOC.TXT</span> file in the same folder as the tracks.
 Each line in the file contains the track starting position in <span class='fixed'>mm:ss.ff</span> format, plus the filename of the audio file for that track (except for track 1).
 This file can be easily generated within a .dreamm file to ensure that the TOC shows up as desired.
 </p>
 <p>
 Here is an example TOC.TXT:
 </p>
 <p class='indented'>
00:02.00
05:14.35 Track-2.mp3
05:54.55 Track-3.mp3
10:02.70 Track-4.mp3
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>

 <a name='syscalls'></a>
 <h4>How Windows system calls are handled</h4>

 <p>
 When a Windows binary is loaded, all referenced DLLs are also searched for and loaded at the same time (along with all <i>their</i> dependencies, etc.).
 For system DLLs that DREAMM emulates, what is loaded is actually a custom stub DLL, stored in DREAMM's <a href='#ifs'>internal file system</a>.
 </p>
 <p>
 These stub DLLs are designed to contain all the APIs that the equivalent system DLL has on Windows.
 However, instead of containing any implementations, each API is implemented as a special call into DREAMM, using a special CPU instruction.
 </p>
 <p>
 When that instruction is detected by DREAMM, it looks at the caller to find the name of the API that is being called, and then consults its internal list of APIs that have been implemented.
 If the API is found, then DREAMM patches the stub with a different special CPU instruction representing that API implementation, so that future calls to it go directly there.
 </p>
 <p>
 If the API is not found in DREAMM’s internal list, then an &ldquo;Unimplemented API&rdquo; error happens and DREAMM exits.
 This is why games will load successfully, but later exit out with an error when the API is actually called.
 </p>
 <p>
 As it turns out, many APIs referenced by games are only used in edge cases or error conditions, so waiting until the call is actually made to check for its existence enables many games to run, even if some of the more esoteric APIs are not yet implemented.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>

 <a name='debugging'></a>
 <h3>Debugging in DREAMM</h3>

 <p>
 Sometimes you need to get your hands dirty and understand what a game is doing in order to get it running in DREAMM.
 To help with this, there are a number of useful features and techniques that are available, described here.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>

 <a name='info'></a>
 <h4>Live Information</h4>

 <p>
 While a game is running, you can get a lot of useful information via one of several shortcut keys built into DREAMM.
 Here’s a quick rundown of what they are and what the information means.
 </p>

 <h5>Video Information: Shift+Alt+V (macOS: Shift+⌘V)</h5>

 <p>
 This is probably the most commonly-used information display.
 It is also the most detailed.
 It looks something like this:
 </p>

 <p class='indented' style='text-align:right'>Win32: 640&times;480&times;32 @ 59.94Hz, 11fps [1ms]
 Target: 1920&times;1440 (3.00&times;) direct3d none nosync 11fps [48ms]
 3D: tris 14(15), lines 0, draw 101%/101%, 7fps [74ms&times;2]</p>

 <p>
 The top line will tell you about the current video mode: the name of the video card (CGA/VGA/etc) or Win32 in the case of Windows, followed by the configured video mode <span class='fixed'><i>height</i>&times;<i>width</i>&times;<i>depth</i> @ <i>refresh-rate</i>Hz</span>.
 Follwing that will be an fps number, which is the number of times the video frame information was updated in the past second. The final time in brackets is the total CPU time spent copying data from video memory into intermediate buffers.
 </p>

 <p>
 The second line is all about how the video is presented to you.
 It shows the target resolution as <span class='fixed'><i>height</i>&times;<i>width</i> (<i>scale-factor</i>&times;)</span>, followed by the type of renderer (direct3d, opengl, sdl), followed by the screen effect (none, epx, or crt), followed by the VSYNC setting (sync or nosync), followed by the number of frames that were shown in the past second. The final time in brackets is the CPU time spent building textures and sending rendering commands to your graphics card.
 </p>

 <p>
 The third line only shows up in Windows games that are actively using Direct3D.
 It shows the number of triangle primitives sent, plus, in parentheses, the final number of triangles produced after clipping, followed by the number of line primitives sent.
 After that is the percentage of the screen that was covered by primitives, followed by the percentage of the screen that was covered by primitives that passed both alpha and Z buffer tests.
 Next is the number of scenes that were rendered in the last second (note that some games use more than 1 scene/frame!) Finally, the time in brackets is the total CPU time spent actually rendering 3D, along with the number of threads that were used to do the drawing.
 </p>

 <h5>Audio Information: Shift+Alt+A (macOS: Shift+⌘A)</h5>

 <p>
 The audio summary looks something like this:
 </p>

 <p class='indented' style='text-align:right'>Audio: 48000Hz, ahead 8&times; [10227us avg]
 Digital: 8-bit stereo @ 10989
 SB16 [01______890______7]</p>

 <p>
 The top line shows the final audio output frequency to your sound card, in Hz, along with some internal statistics about whether audio is ahead or trailing the emulation.
 Subsequent lines represent active audio generators, and each displays their own information:
 </p>

 <ul>
 <li>Sound generators with multiple channels will typically show which channels are actively playing</li>
 <li>Digital sound generators will show the sample size and frequency of the source</li>
 <li>MIDI and MT-32 sound generators will show the name of the configured output</li>
 <li>CD audio generators will show the current play time and audio source</li>
 </ul>

 <h5>CPU Information: Shift+Alt+C (macOS: Shift+⌘C)</h5>

 <p>
 The CPU information summary will look like this:
 </p>

 <p class='indented' style='text-align:right'>i386 [paged] 33.40 MIPS fixed
 Throttle: 71047us throttling (0 resets)
 Idle skipping: 767290us idle, 8674us sleep</p>

 <p>
 The top line of information indicates the mode the CPU is executing (either paged or flat), along with the number of instructions executed in the past second. At the end is an indication of whether the system is running in fixed or timed execution mode.
 </p>

 <p>
 The second line shows the current throttling status.
 If throttling is enabled, it will show the number microseconds spent waiting for real time to catch up to CPU time in the last second.
 </p>

 <p>
 The third line shows the amount of time in the past second that was spent idling (i.e., spinning waiting for interrupts).
 By default, DREAMM detects common VSYNC-related loops and goes into idle mode when they are seen.
 The second number shows the amount of time that was explicitly spent returning time to your host OS (typically this is pretty small).
 </p>

 <h5>Memory Information: Shift+Alt+M (macOS: Shift+⌘M)</h5>

 <p>
 Windows applications can display the current memory situation via this shortcut.
 It will show something like this:
 </p>

 <p class='indented' style='text-align:right'>GDI Handles: 125/155
 Resource Handles: 16/16
 Misc Handles: 83/163
 DESKTOP.EXE: 2 threads, 22.59MB
 ALIFE.EXE: 2 threads, 18.70MB</p>

 <p>
 The first three lines show the current and maximum number of handles allocated for each type.
 This can be used to look for handle leaks over time.
 </p>
 <p>
 Following that is one line per active process, showing the number of live threads plus the total memory allocated by that process.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>

 <a name='logging'></a>
 <h4>Logging</h4>

 <p>
 DREAMM internally contains a lot of logging, which is grouped into various categories by name.
 Each message sent to the log is categorized as either <b>important</b>, <b>basic</b>, or <b>verbose</b>.
 By default, all logging is turned off in officially released builds of DREAMM.
 </p>

 <p>
 To enable logging, use the <span class='fixed'>-log</span> command line option, or the <span class='fixed'>log</span> command in the debugger.
 Both commands accept one or more comma-separated names of categories to enable/disable.
 Category names can end with an asterisk to include all categories that begin with the same prefix.
 </p>

 <p>
 Here are some examples:
 </p>
 <p class='indented'>
 // enable output of all important or basic messages in the dos category
 dreamm -log dos

 // enable output of all important, basic, or verbose messages in the dos category
 dreamm -log dos+

 // enable output of all important or basic messages in the dos category,
 // plus all important, basic, or verbose messages in the dos.cdrom category
 dreamm -log dos,dos.cdrom+

 // enable output of all important, basic, or verbose messages in any category
 // whose name starts with 'dos'
 dreamm -log dos*+
 </p>

 <p class='important'>
 <b>Note:</b> logs can be quite verbose, so it is highly recommended that you redirect logging to a file.
 </p>

 <p>
 Here is a list of logs currently used in DREAMM:
 </p>

 <ul>
  <li><b>audio.adlib</b> &mdash; AdLib emulation</li>
  <li><b>audio.cdda</b> &mdash; CD audio (CD/DA) emulation</li>
  <li><b>audio.cms</b> &mdash; CMS emulation</li>
  <li><b>audio.digital</b> &mdash; digital audio emulation</li>
  <li><b>audio.midi</b> &mdash; MIDI emulation</li>
  <li><b>audio.midi.gen</b> &mdash; MIDI output generation</li>
  <li><b>audio.rf5c68</b> &mdash; RF5C68 digital audio emulation</li>
  <li><b>audio.sb16</b> &mdash; SoundBlaster 16 emulation</li>
  <li><b>audio.speaker</b> &mdash; PC speaker emulation</li>
  <li><b>audio.sys</b> &mdash; general audio system</li>
  <li><b>audio.tandy</b> &mdash; Tandy audio emulation</li>
  <li><b>bios</b> &mdash; BIOS calls and behavior</li>
  <li><b>custom</b> &mdash; custom CPU instructions</li>
  <li><b>dma</b> &mdash; DMA controller emulation</li>
  <li><b>dos</b> &mdash; DOS system calls</li>
  <li><b>dos.async</b> &mdash; DOS asynchronous file I/O</li>
  <li><b>dos.cdrom</b> &mdash; MSCDEX calls</li>
  <li><b>dos.himem</b> &mdash; EMS or XMS calls</li>
  <li><b>dos.memory</b> &mdash; DOS memory manager</li>
  <li><b>dos.mouse</b> &mdash; DOS mouse handling</li>
  <li><b>floppy</b> &mdash; floppy controller</li>
  <li><b>fmtowns</b> &mdash; FM-Towns system calls</li>
  <li><b>gameport</b> &mdash; joystick port</li>
  <li><b>keyboard</b> &mdash; keyboard controller</li>
  <li><b>lowmem</b> &mdash; log accesses to low memory</li>
  <li><b>pic</b> &mdash; interrupt controller</li>
  <li><b>pit</b> &mdash; system timer</li>
  <li><b>ppi</b> &mdash; I/O ports (for keyboard and other system functions)</li>
  <li><b>rtc</b> &mdash; real-time clock/CMOS</li>
  <li><b>script</b> &mdash; scripting output and errors</li>
  <li><b>serial</b> &mdash; serial port</li>
  <li><b>stdout</b> &mdash; any text printed to stdout gets sent here</li>
  <li><b>timers</b> &mdash; internal timers</li>
  <li><b>video</b> &mdash; video system</li>
  <li><b>video.bios</b> &mdash; video BIOS calls</li>
  <li><b>video.crtc</b> &mdash; video CRT controller</li>
  <li><b>video.dac</b> &mdash; palette DAC</li>
  <li><b>video.vga.attr</b> &mdash; VGA attribute controller</li>
  <li><b>video.vga.crtc</b> &mdash; VGA CRT controller</li>
  <li><b>video.vga.gc</b> &mdash; VGA graphics controller</li>
  <li><b>video.vga.read</b> &mdash; VGA frame buffer reads</li>
  <li><b>video.vga.seq</b> &mdash; VGA sequencer</li>
  <li><b>video.vga.write</b> &mdash; VGA frame buffer writes</li>
  <li><b>windows</b> &mdash; Windows API calls</li>
  <li><b>windows.msg</b> &mdash; Windows messages (in Spy++ format)</li>
  <li><b>windows.sound</b> &mdash; Windows sound-related logs</li>
  <li><b>windows.unexpected</b> &mdash; unexpected results from Windows that might help point to problems</li>
 </ul>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>

 <a name='debugger'></a>
 <h4>Interactive Debugger</h4>

 <p>
 DREAMM ships with an internal debugger that you can enable by launching with the <span class='fixed'>-debug</span> flag.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>

 <a name='debugtips'></a>
 <h4>Debugging Tips</h4>

 <ul class='spaced'>
 <li>
 You can reference various CPU register values in debugger expressions:
  <ul>
   <li>32-bit registers: <b>eip</b>, <b>eflags</b>, <b>eax</b>, <b>ebx</b>, <b>ecx</b>, <b>edx</b>, <b>esi</b>, <b>edi</b>, <b>ebp</b>, <b>esp</b></li>
   <li>16-bit registers: <b>ip</b>, <b>flags</b>, <b>ax</b>, <b>bx</b>, <b>cx</b>, <b>dx</b>, <b>si</b>, <b>di</b>, <b>bp</b>, <b>sp</b></li>
   <li>8-bit registers: <b>ah</b>, <b>al</b>, <b>bh</b>, <b>bl</b>, <b>ch</b>, <b>cl</b>, <b>dh</b>, <b>dl</b></li>
   <li>individual flags: <b>iflag</b>, <b>dflag</b>, <b>oflag</b>, <b>cflag</b>, <b>zflag</b>, <b>pflag</b>, <b>sflag</b>, <b>aflag</b>, <b>tflag</b>, <b>rflag</b></li>
   <li>linear IP: <b>lip</b> = csbase + (e)ip depending on mode</li>
   <li>linear stack pointer: <b>lsp</b> = ssbase + (e)sp depending on mode</li>
   <li>floating-point stack: <b>st0</b> ... <b>st7</b></li>
   <li>MMX registers: <b>mm0</b> ... <b>mm7</b></li>
   <li>segment registers: <b>cs</b>, <b>ds</b>, <b>es</b>, <b>fs</b>, <b>gs</b>, <b>ss</b></li>
   <li>segment bases: <b>csbase</b>, <b>dsbase</b>, <b>esbase</b>, <b>fsbase</b>, <b>gsbase</b>, <b>ssbase</b></li>
   <li>segment limits: <b>cslimit</b>, <b>dslimit</b>, <b>eslimit</b>, <b>fslimit</b>, <b>gslimit</b>, <b>sslimit</b></li>
   <li>descriptor tables: <b>gdtrbase</b>, <b>gdtrlimit</b>, <b>idtrbase</b>, <b>idtrlimit</b></li>
   <li>control/debug registers: <b>cr0</b> ... <b>cr7</b>, and <b>dr0</b> ... <b>dr7</b></li>
   <li>watchpoint info: <b>wpdata</b>, <b>wpprev</b></li>
   <li>exception info: <b>excode</b></li>
   <li>video position info: <b>vpos</b>, <b>hpos</b>, <b>frame</b></li>
   <li>DOS PSP base: <b>psp</b></li>
  </ul>
 </li>

 <li>
 Debugger expressions can also make use of a few custom functions:
  <ul>
   <li><b>byte(addr)</b><br><b>word(addr)</b><br><b>dword(addr)</b><br><b>qword(addr)</b> &mdash; reads memory of the given size from the addr</li>
   <li><b>fdword(addr)</b><br><b>fqword(addr)</b> &mdash; reads memory of the given size from the addr and interprets it as a floating-point value</li>
   <li><b>set_byte(addr, val)</b><br><b>set_word(addr, val)</b><br><b>set_dword(addr, val)</b><br><b>set_qword(addr, val)</b> &mdash; writes a value to the given addr</li>
   <li><b>set_fdword(addr, val)</b><br><b>set_fqword(addr, val)</b> &mdash; writes a floating-point value to the given addr</li>
   <li><b>string(addr)</b><br><b>wstring(addr)</b> &mdash; reads a 0-terminated string/wide string from memory at the given addr</li>
  </ul>
 </li>

 <li>
 The <b>?</b> command can be used to print the result of an expression to the console.
 (Good for testing out expressions.)
 </li>

 <li>
 There is limited watchpoint support.
 You can set a watchpoint on a range of memory via the <b>wp</b> command, but DREAMM only detects <i>changes</i> to memory, not the act of writing itself.
 If a value is initialized to 0 and you want to catch someone writing a 0 value to that location, you can use the <b>write</b> command to seed it with a non-zero value so that the next write produces a change that DREAMM can notice.
 </li>

 <li>
 Breakpoints and watchpoints can have conditions.
 For example, <b>bpint 21,ah==4c</b> will break on the next INT 21h call but only when AH is 4Ch.
 Any of the variables/functions listed above can be used in the condition.
 </li>

 <li>
 Breakpoints and watchpoints can also have actions.
 These are debugger commands that are executed upon a successful break; multiple commands can be separated by a triple ampersand.
 For example: <b>bpint 21,ah==3d,?string(ds:dx)&&&go</b> sets a conditional breakpoint on the DOS open file command; each time it is hit, it prints the string at ds:dx (should be the filename), and then executes a <b>go</b> command to resume execution.
 This example could be used to trace all file opens in a program.
 </li>

 <li>
 The <b>gc</b> (go conditional) command can be useful for more esoteric cases.
 It single steps and tests the provided condition after each instruction.
 For example, if you are currently in real mode and want to skip until you enter protected mode, you can do <b>gc (cr0 & 1)</b>.
 Or if you are in a loop and want to run until the ECX register is a certain value, you can do <b>gc ecx==value</b>.
 </li>

 <li>
 DREAMM can step backwards!
 In order to step backwards, you must first enable history tracking, which is off by default (because it’s expensive) via the <b>hist</b> command.
 Once enabled, you can step backwards through the history using the <b>sb</b> command (or F9) to step one instruction at a time.
 Note that the highlight on the instruction is now purple instead of yellow, to indicate that you are currently back in time.
 To return to the present time, use the <b>go</b> command (or F5).
 </li>

 <li>
 When stepping backwards, you can use the <b>sob</b> command to “step over” backwards to the call instruction.
 </li>

 <li>
 To debug a crash, use the <b>bpex *</b> command to set a breakpoint on any exception.
 Using this in conjunction with <b>hist</b> enables backwards stepping to help understand the events leading up to the crash.
 </li>

 <li>
 <b>(Windows)</b> The first things you execute will be the various DLL initialization routines.
 If you want to skip past those, use the <b>gu</b> command (or Shift+F11) to skip over each one until the <span class='fixed'>Mod:</span> field on the left side of the debugger shows your executable name, and not kernel32.dll.
 </li>

 <li>
 <b>(Windows)</b> If you find yourself on a thread you don’t care about, you can use the <b>ignore</b> command to ignore it when stepping.
 </li>
 </ul>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>

 <a name='tips'></a>
 <h3>Tips and Tricks</h3>

 <p>
 Quite a lot of how to create .dreamm files is based on experience with typical game and installer behaviors.
 This section attempts to capture some of the common (and less-common) situations that might lead you to a solution.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>

 <a name='misctips'></a>
 <h4>Miscellaneous Tips</h4>

 <ul class='spaced'>
 <li>
 Many CD games check for a volume label as a form of copy protection.
 You can simulate a volume label by creating a script that calls the <a href='dreammscript.html#drive_set_label'>drive_set_label</a> function:
 </li>
 <p class='indented'>script[setlabel]: code = drive_set_label("D", "<i>volumename</i>")</p>

 <li>
 If a DOS game launches but immediately quits, it might have printed something useful to the console before it exited.
 You can use <span class='fixed'>-log stdout</span> to output any console text to your system console.
 </li>

 <li>
 DREAMM supports an internal <b>test</b> property.
 You can set <span class='fixed'>test = true</span> in a <a href='#variant'>variant</a> or <a href='#desc'>desc</a> object and it will not be considered when showing or installing games.
 This is useful if something is broken and you want to fix it, but don’t want people reporting issues.
 </li>

 <li>
 For games that require installation, it can be helpful to install the game once on another emulator, and then see what files (and registry keys in the case of Windows) are placed on the hard disk.
 With that information, you can attempt to recreate the install by hand.
 </li>

 <li>
 Many CD games are organized with most of the files on the CD, and a subset of the files installed to the hard drive.
 In this case, you can often just include <a href='#file'>file</a> objects for the contents of the CD, and then add a mount for the hard drive that points to the directory where the files are copied from.
 For example, if the game lives at <span class='fixed'>D:\GAME\GAME.EXE</span> on the CD, you can add a mount like this:
 </li>
 <p class='indented'>mount[]: vpath = $(rootpath); srcpath = $(ifsinstallpath)/GAME</p>

 <li>
 If a Windows game is complaining about a missing CD, run the game with <span class='fixed'>-log windows</span>.
 Typically, they put up a messagebox, so you search for <b>MessageBox</b> in the log to see where the messagebox was initiated.
 A few steps back from that should be the location where the CD check was &mdash; from there you might get some ideas of what it was looking for.
 </li>

 <li>
 To pre-populate the registry for Windows games, put the contents of a standard .REG file into a variable and use a script to import it:
 </li>
 <p class='indented'>REGVER = Retail
REGISTRY = [[[$(CRLF)
    REGEDIT4
    [HKEY_LOCAL_MACHINE\Software\LucasArts Entertainment Company LLC\Monkey4\$(REGVER)]
    @="$$(rootpath)"
    "CD Path"="D:\\"
    "Executable"="$$(rootpath)\\Monkey4.exe"
    "GUID"="{12345678-9ABC-DEF0-123456789ABCDE01}"
    "Installed"=dword:00000002
    "OpenGL"=dword:00000000
]]]
script[registry]: code = winregistry_import_raw("$$(REGISTRY)")
</p>

 </ul>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>

 <a name='conventions'></a>
 <h4>.dreamm File Conventions</h4>

 <p>
 These are my personal preferences and recommendations, not hard requirements.
 </p>

 <ul class='spaced'>
 <li>
 Use smart quotes in game titles and names. Here they are for copy/pasting: <span class='fixed'>‘ ’</span> instead of <span class='fixed'>'</span>, and <span class='fixed'>“ ”</span> instead of <span class='fixed'>"</span>.
 </li>

 <li>
 Remove unrelated files from game file manifests, unless they are explicitly referenced by the game, or are useful (like documentation). This includes:
 </li>

 <p class='indented'>AUTORUN.INF
SETUP.*
*.CAB
*.ICO
*.ISU</p>

 <li>
 In particular, for CD-based games, there is often shovelware demos and other junk on the CD that should be stripped away.
 (Demos should be installed as separate <a href='#variant'>demo</a> variants, of course!)
 </li>

 <li>
 Text files like <span class='fixed'>READ.ME</span>s should be listed without explicit size or crc.
 This helps to match more versions of the game that might differ only in the text provided.
 </li>

 <li>
 Don’t use Roland MT-32 as the default sound option, as it requires external ROMs to sound good.
 Even if it’s the preferred option with proper ROMs, it should take back seat to another option because many people don’t know how to get good results from it.
 </li>
 </ul>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>

 <a name='fileformats'></a>
 <h3>File Formats</h3>

 <p>
 DREAMM reads and writes files in a couple of custom formats.
 Below is documentation on these files.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>

 <a name='floppy-track'></a>
 <h4>Floppy track files</h4>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>

 <a name='cdda-sou'></a>
 <h4>CDDA.SOU</h4>


</body>
</html>
