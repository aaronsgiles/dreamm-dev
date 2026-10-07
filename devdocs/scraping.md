<!doctype html>
<html lang='en-US' xmlns:og='http://opengraphprotocol.org/schema/' xmlns:fb='http://www.facebook.com/2008/fbml'>
<head>
<!--#set var="title" value="DREAMM 4.0 Scraping Documentation" -->
<!--#set var="description" value="Documentation regarding DREAMM’s screen scraping engine." -->
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
	background-color:#CCD0FF;
	padding:5px 10px;
	border-radius:5px;
	border:1px solid #03063A;
    white-space:pre-wrap;
}
ul.spaced li { padding-bottom:15px }
ul.spaced li:last-child { padding-bottom:0 }
ul.spaced .indented { margin-top:5px }
ul.spaced ul { margin-top:5px }
ul.spaced ul li { padding-bottom:0 }
</style>
</head>

<body lang='en' class='winbody'>
<div class='page'>
 <div class='logobg'>
  <div class='center'>
   <a href='/'><img src='/img/dreamm-logo.png'></a>
  </div>
 </div>

 <h2 class='center'>DREAMM Screen Scraping</h2>
 <p class='center'>As of DREAMM 4.0x</p>

 <p>
 This page is an attempt to document DREAMM’s screen scraping engine and the debugging tools available to help you use it.
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
    <div class='subtopic'><a href='#types'>Types of Scraping</a></div>
    <div class='subtopic'><a href='#regions'>Scraping Regions</a></div>
   </div>
  </li>
  <li>
   <a href='#image'>Image Matching</a>
   <div class='subtopics'>
    <div class='subtopic'><a href='#image-fingerprints'>Fingerprints</a></div>
    <div class='subtopic'><a href='#image-flags'>Flags</a></div>
    <div class='subtopic'><a href='#image-functions'>Functions</a></div>
   </div>
  </li>
  <li>
   <a href='#string'>String Matching</a>
   <div class='subtopics'>
    <div class='subtopic'><a href='#string-bitmasks'>Bitmasks</a></div>
    <div class='subtopic'><a href='#string-colors'>Colors</a></div>
    <div class='subtopic'><a href='#string-chars'>Character Matching</a></div>
    <div class='subtopic'><a href='#string-flags'>Flags</a></div>
    <div class='subtopic'><a href='#string-functions'>Functions</a></div>
   </div>
  </li>
  <li>
   <a href='#debugging'>Debugging Tools</a>
   <div class='subtopics'>
    <div class='subtopic'><a href='#debugging-scrape'>The scrape Command</a></div>
    <div class='subtopic'><a href='#debugging-cs-commands'>The cs Commands</a></div>
    <div class='subtopic'><a href='#debugging-csid-regions'>csid Regions</a></div>
    <div class='subtopic'><a href='#debugging-csid-charset'>csid Charsets</a></div>
   </div>
  </li>
 </ul>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>

 <a name='intro'></a>
 <h3>Introduction</h3>

 <p>
 Screen scraping is a technique that can be leveraged from within DREAMM’s <a href='dreammscript.html'>scripting engine</a> to facilitate automation of operations based on what the user sees.
 It is currently used extensively for LucasArts games to offer solutions and auto-solve the manual-based copy protections present in many of the games, but could easily be extended to other operations, like auto-switching disks or automating other tasks.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>

 <a name='types'></a>
 <h4>Types of Scraping</h4>

 <p>
 DREAMM’s scraping engine supports two different forms of scraping:
 </p>

 <ul>
  <li><b><a href='#image'>Image Matching</a></b> &mdash; matches pixels on the screen against one or more <i>fingerprints</i>, which are specialized hashes that break the image area into several regions and provide independent checksums for each.</li>
  <li><b><a href='#string'>String Matching</a></b> &mdash; extracts columns of pixels from left-to-right and matches them against a <a href='dreamm.html#charset'>charset</a> to produce a text string which can be examined by the script.</li>
 </ul>

 <p>
 Both types of scraping can be used at the same time, and are independent of each other, although they do share some common concepts like <a href='#regions'>regions</a>.
 </p>

 <p>
 Image Matching is obviously most useful for situations that don’t involve text, but becomes more involved when having to deal with multiple different video modes that games may support.
 </p>

 <p>
 String Matching is best employed in situations where there are text-based instructions that need to be parsed; however, supporting games that ship in multiple languages creates additional challenges.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>

 <a name='regions'></a>
 <h4>Scraping Regions</h4>

 <p>
 Both Image Matching and String Matching rely on <b>regions</b>, which are simply lists of rectangular areas of interest.
 These lists are assembled together and encoded into a <b>region string</b>, which is then passed to the various script functions that do the matching.
 </p>

 <p>
 An important thing to understand about regions is that they are <b>resolution independent</b>, meaning that the coordinates are stored as fractions (currently 12-bit fixed point) and multiplied by the current resolution.
 This means that the same region can often be used for games that run at multiple resolutions.
 </p>

 <p>
 Regions are encoded in a base64-like string format both for compactness and to ensure that translation between fractional and screen coordinates is handled consistently.
 DREAMM uses the string as a hash to create a cache of regions that help optimize its computations.
 </p>

 <p>
 If you really need to create a region dynamically, you can use the <a href='dreammscript.html#scraper_region'>scraper_region</a> function to build up a region programmatically, using coordinates relative to the current video mode.
 You can also use <a href='dreammscript.html#scraper_rects'>scraper_rects</a> to get a list of the rectangles in a region, mapped to the current video dimensions.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>

 <a name='image'></a>
 <h3>Image Matching</h3>

 <p>
 Image Matching is a scraping tool that computes a <b>fingerprint</b> for the pixels described by a given <a href='#regions'>region</a>.
 This fingerprint can then be compared against one or more known fingerprints to produce a match.
 </p>

 <p>
 When performing image matching, if you provide a region that has multiple overlapping rectangles, the rectangles will cancel each other out in areas that overlap (technically, they are exclusive-or’ed together).
 This allows you to define a large region and then easily carve out areas to ignore by just adding new rectangles on top of the outer region.
 </p>

 <a name='image-fingerprints'></a>
 <h4>Fingerprints</h4>

 <p>
 The fingerprint computation is not just a simple checksum over the pixels.
 Rather, the image region is split into a 3x3 grid of tiles, and each tile is checksummed independently.
 In addition, the fingerprint also contains an encoding of the center pixel within each tile, which can be used as a quick negative check to save having to compute the full checksum.
 </p>

 <p>
 The reason for the 3x3 grid is to help in situations where there is a user-controlled mouse pointer which may disrupt the checksum calculation.
 In this situation, the pointer may overlap up to 4 of the tiles (imagine the case where the pointer is right on an inner corner between tiles), and so a match might still be considered valid with as few as 5 matching tiles.
 Obviously this mitigation only works if the size of the overall region is at least 3&times; the size of the mouse pointer.
 </p>

 <a name='image-flags'></a>
 <h4>Flags</h4>

 <p>
 When doing image matching, you can specify a few flags to control its operation:
 </p>

 <ul class='spaced'>
 <li><b>MF_COLLAPSE16</b> &mdash; this flag &ldquo;collapses&rdquo; 8-bit pixel values down to 4 bits, by ORing the top and bottom nibbles together. This flag is admittedly fairly specialized for some of the LucasArts 16-color adventure games, which use a custom palette in 256-color mode that allows them to write the color value to either nibble. Combining the upper and lower nibbles in this case produces identical fingerprints to normal 16-color modes.</li>
 <li><b>MF_MOUSE</b> &mdash; this flag indicates that a mouse pointer may be present, and requires only 5 of the 9 tiles to match in order to be considered a valid fingerprint match. Without the flag, all 9 tiles must match to be considered valid.</li>
 </ul>

 <p>
 These flags may be used together if desired.
 </p>

 <a name='image-functions'></a>
 <h4>Functions</h4>

 <p>
 There are two scripting functions related to image matching:
 </p>

 <ul class='spaced'>
 <li>
 <a href='dreammscript.html#scraper_match'>scraper_match</a> lets you specify a region and a list of one or more comma-separated fingerprints to match against. Each fingerprint can have an associated string, which is returned if it is the best match (and meets the minimum requirements for a match, either 5 or 9 tiles).
 </li>
 <li>
 <a href='dreammscript.html#scraper_fingerprint'>scraper_fingerprint</a> lets you compute the current fingerprint value for the specified region.
 This is useful in scripts if you have a region already picked out and want to output the fingerprint when you encounter an image you don’t recognize.
 </li>
 </ul>

 <p>
 Both of these functions support the <b>MF_*</b> flags above.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>

 <a name='string'></a>
 <h3>String Matching</h3>

 <p>
 The second form of scraping is string matching.
 When doing a string match, you specify a <a href='#regions'>region</a>, a <a href='dreamm.html#charset'>charset</a>, and a color, and DREAMM attempts to use that information to read the pixels on the screen and construct a string of text.
 </p>

 <a name='string-bitmasks'></a>
 <h4>Bitmasks</h4>

 <p>
 Regions behave a bit differently than in <a href='#image'>Image Matching</a> in that each rectangle describes an independent line of text.
 Overlapping regions don’t cancel either out; rather, each rectangle is processed in order.
 Only the top, left, and right sides of each rectangle matter in this case; the height is implicitly defined by the charset.
 </p>

 <p>
 DREAMM begins string scraping by creating a list of columns.
 Each column is the height of the charset, and is stored as a bitmask, where a 1 means the pixel matches the specified color, and 0 means it doesn’t.
 DREAMM works from left to right within each rectangle, taking each column of pixels and creating a bitmask for it.
 Once a rectangle is fully scanned, an empty bitmask is inserted into the list, and then the next rectangle is processed, adding to the same list.
 </p>

 <a name='string-colors'></a>
 <h4>Colors</h4>

 <p>
 The way that color is processed is dependent upon the current video mode.
 In video modes with 256 or fewer colors, DREAMM will use the palette index as the color to match against.
 In addition, you can specify up to 8 different colors to match against by specifying them in a tuple.
 For example, to match against any of color 0, 2, or 74, you can specify a color of <span class='fixed'>[ 0, 2, 74 ]</span>.
 </p>

 <p>
 In video modes with more than 256 colors, you specify the RGB values directly, and only one color is supported.
 So to match against #FF7788 you would pass 0xff7788 as the color.
 </p>

 <a name='string-chars'></a>
 <h4>Character Matching</h4>

 <p>
 When the full list of bitmasks is complete, DREAMM then begins identifying characters.
 </p>

 <p>
 First, it skips over any blank columns.
 Then it walks through the characters defined in the charset looking for matches against the current set of bitmasks.
 Of all the matches found, DREAMM selects the longest one and adds that character to the string before proceeding.
 </p>

 <p>
 If the current pattern does not match any characters, DREAMM ignores the mystery character and skips ahead to the next blank column.
 (Or you can instruct DREAMM to insert a placeholder character instead; see the <a href='#string-flags'>Flags</a> section below.)
 </p>

 <p class='important'>
 <b>Note:</b> Because DREAMM skips over blank columns, the final string will contain no spaces.
 </p>

 <a name='string-flags'></a>
 <h4>Flags</h4>

 <p>
 When doing string matching, you can specify a few flags to control its operation:
 </p>

 <ul class='spaced'>
 <li><b>SF_HALFWIDTH</b> &mdash; this flag only makes sense for video modes with 16 colors or less. What it does is work in pairs of columns rather than a single column. For each pair of columns, the pixel values are combined by taking the first column’s pixel value, shifting it up by the pixel depth (1, 2, or 4) and adding it to the second column’s pixel value. The purpose of this flag is to allow matching for games that generate dithered high-resolution graphics where the text may not be a solid color due to the dithering.</li>
 <li><b>SF_BGCOLOR</b> &mdash; this flag indicates that the color specified is a background color rather than the color of the text. This effectively inverts the bitmask before comparing, and can be useful in circumstances where text is drawn against a solid color background.</li>
 <li><b>SF_PLACEHOLDERS</b> &mdash; this flag indicates that a placeholder character (ASCII 1) should be inserted into the string whenever an unexpected character is encountered. Normally DREAMM just skips over anything it doesn’t recognize. This can be useful for debugging or making sure that your character set is complete.
 </ul>

 <p>
 These flags may be used together if desired.
 </p>

 <a name='string-functions'></a>
 <h4>Functions</h4>

 <p>
 At the moment there is just a single function for string matching:
 </p>

 <ul class='spaced'>
 <li>
 <a href='dreammscript.html#scraper_string'>scraper_string</a> lets you specify a region, a charset, and a color to extract, along with 0 or more of the flags above. It simply returns the string that it found.
 </li>
 </ul>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>

 <a name='debugging'></a>
 <h3>Debugging Tools</h3>

 <p>
 DREAMM’s debugger offers some powerful tools for defining scraping regions, creating fingerprints, and generating charsets.
 To enable the debugger, run DREAMM with the <span class='fixed'>-debug</span> flag.
 </p>

 <p>
 When broken into the debugger, you can type the <span class='fixed'>g</span> command, or press the <span class='fixed'>F5</span> key, to resume execution.
 </p>

 <p>
 While in the debugger you can also press the <span class='fixed'>F4</span> to view the current screen without resuming execution.
 Any key or mouse click will return you to back to the debugger proper.
 </p>

 <p>
 While the game is running, press the <span class='fixed'>Right Shift</span> key to break back into the debugger.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>

 <a name='debugging-scrape'></a>
 <h4>The scrape Command</h4>

 <p>
 To define an image matching region and generate a fingerprint, break into the debugger when the image you want is on screen.
 </p>

 <p>
 Once you’re at the debugger prompt, use the <b>scrape</b> command to begin creating a region.
 If you already have a region and want to modify it, include the encoded region string as a parameter to the scrape command.
 </p>

 <p>
 <b>scrape</b> will show you the game screen, and your cursor should be a crosshair.
 Click and drag a rectangle around the region you want to include in the fingerprint; areas outside of this region will be drawn dimmed.
 </p>

 <p class='important'>
 <b>Note:</b> The border of each rectangle is drawn <i>outside</i> of the actual area, so be aware that the border pixel is not included in your region.
 </p>

 <p>
 To add rectangles to the region, hold down the <span class='fixed'>Shift</span> key and click to drag a second rectangle.
 Note how the rectangles interact with each other; a pixel is only included if it is contained within an odd number of rectangles.
 This allows you to define an overall region to include and carve out exceptions by adding rectangles within the first.
 </p>

 <p>
 If you want to remove a rectangle you have drawn, right click on it.
 </p>

 <p>
 Once you’re happy with how the region looks, press <span class='fixed'>Return</span> or <span class='fixed'>Esc</span> to get back to the debugger, which will show you both the region string as well as the fingerprint for what’s currently there.
 </p>

 <p>
 In addition to navigating with the mouse, some keyboard shortcuts/commands are also available:
 </p>

 <table>
  <tr><th>Keys</th><th>Behavior</th></tr>
  <tr>
   <td style='text-align:center'>
    <span class='fixed'>&larr;</span>
    <span class='fixed'>&rarr;</span>
    <span class='fixed'>&uarr;</span>
    <span class='fixed'>&darr;</span>
   </td>
   <td>
    Moves the highlighted rectangle by one pixel in the given direction.
   </td>
  </tr>
  <tr>
   <td style='text-align:center'>
    <span class='fixed'>Shift&nbsp;&larr;</span>
    <span class='fixed'>Shift&nbsp;&rarr;</span>
   </td>
   <td>
    Increases or decreases the width of the highlighted rectangle.
   </td>
  </tr>
  <tr>
   <td style='text-align:center'>
    <span class='fixed'>Shift&nbsp;&uarr;</span>
    <span class='fixed'>Shift&nbsp;&darr;</span>
   </td>
   <td>
    Increases or decreases the height of the highlighted rectangle.
   </td>
  </tr>
  <tr>
   <td style='text-align:center'>
    <span class='fixed'>Backspace</span>
    <span class='fixed'>Delete</span>
   </td>
   <td>
    <b>Delete</b>. Removes the currently highlighted rectangle from the list.
   </td>
  </tr>
  <tr>
   <td style='text-align:center'>
    <span class='fixed'>D</span>
   </td>
   <td>
    <b>Duplicate</b>. Makes a copy of the currently highlighted rectangle.
    The new rectangle is offset by 4 pixels in both directions and becomes the highlighted rectangle.
   </td>
  </tr>
  <tr>
   <td style='text-align:center'>
    <span class='fixed'>A</span>
   </td>
   <td>
    <b>All</b>. Replaces the current region with a single rectangle covering the entire screen.
   </td>
  </tr>
  <tr>
   <td style='text-align:center'>
    <span class='fixed'>H</span>
   </td>
   <td>
    <b>Horizontal Mirror</b>. Mirrors the left half of the region into the right half.
   </td>
  </tr>
  <tr>
   <td style='text-align:center'>
    <span class='fixed'>V</span>
   </td>
   <td>
    <b>Vertical Mirror</b>. Mirrors the top half of the region into the bottom half.
   </td>
  </tr>
  <tr>
   <td style='text-align:center'>
    <span class='fixed'>N</span>
   </td>
   <td>
    <b>Toggle Info</b>. Enables or disables the display of rectangle coordinates (in the top-left corner) and size (in the bottom-right corner).
   </td>
  </tr>
 </table>

 <p>
 For non-destructive commands that operate on the highlighted rectangle, if no rectangle is currently highlighted, the topmost rectangle is implied and becomes highlighted.
 As long as you don’t move the mouse, the highlight will stick with the current rectangle, even if it moves out from under the crosshair.
 Once you move the mouse, all bets are off and the highlighted rectangle tracks with the mouse.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>

 <a name='debugging-cs-commands'></a>
 <h4>The cs Commands</h4>

 <p>
 For string matching, DREAMM’s debugger features a number of commands related to managing charsets.
 All charset-related commands begin with a 'cs' prefix:
 </p>

 <table>
  <tr><th>Command</th><th>Description</th></tr>
  <tr>
   <td><span class='fixed'>cslist</span></td>
   <td>
    Lists the available charsets.
   </td>
  </tr>
  <tr>
   <td><span class='fixed'>cssel <i>&lt;name&gt;</i></span></td>
   <td>
    Selects a charset to work with.
   </td>
  </tr>
  <tr>
   <td><span class='fixed'>csload <i>&lt;filename&gt;</i></span></td>
   <td>
    Loads the charset defined in the given file.
   </td>
  </tr>
  <tr>
   <td><span class='fixed'>cssave <i>&lt;filename&gt;</i></span></td>
   <td>
    Saves the current charset to the given file.
   </td>
  </tr>
  <tr>
   <td><span class='fixed'>csid</span></td>
   <td>
    Identify characters using the selected charset.
   </td>
  </tr>
 </table>

 <p>
 To begin working with the cs functions, you must first select a charset via the <b>cssel</b> command.
 This charset can be one that already exists in the current game’s .dreamm file, or one that you loaded via the <b>csload</b> command, or one of the <a href='dreammscript.html#scraper_string'>built-in charsets</a>.
 To create a new charset, just specify a non-existent name to <b>cssel</b> and a blank charset will be created.
 </p>

 <p>
 Once you have a charset selected, use the <b>csid</b> command to switch to the game screen and begin working.
 <b>csid</b> is a multi-purpose tool that lets you define string matching regions, determine character colors, and modify charsets.
 If you already have a region you want to work with or modify, pass the encoded region string as a parameter to the <b>csid</b> command.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>

 <a name='debugging-csid-regions'></a>
 <h4>csid Regions</h4>

 <p>
 Much like the <a href='#debugging-scrape'>scrape</a> command, you can use the <b>csid</b> command to define a region for string matching.
 The interface is very similar, albeit with some extra features needed for charsets and strings.
 </p>

 <p>
 Click and drag a rectangle around the region you want to include in the identification; areas outside of this region will be drawn dimmed.
 The height of the rectangle you draw is fixed to the charset height, which defaults to 8 pixels.
 If you need to change the height of the charset, use the <span class='fixed'>Shift&nbsp;&uarr;</span> and <span class='fixed'>Shift&nbsp;&darr;</span> keyboard commands.
 </p>

 <p class='important'>
 <b>Note:</b> The border of each rectangle is drawn <i>outside</i> of the actual area, so be aware that the border pixel is not included in your region.
 </p>

 <p>
 To add rectangles to the region, hold down the shift key and click to drag a second rectangle.
 Unlike with the <b>scrape</b> command, each rectangle represents an individual line of text.
 </p>

 <p>
 If you want to remove a rectangle you have drawn, right click on it.
 </p>

 <p>
 In the bottom-left corner you will see an indication of the current charset height and color, along with any flags that are active.
 Below that will be the current scraping results; once you have everything aligned and set up properly this should be the string you want.
 Any unrecognized characters will show up as a smiley face.
 </p>

 <p>
 Once you’re happy with how the region looks, press <span class='fixed'>Return</span> or <span class='fixed'>Esc</span> to get back to the debugger, which will show you both the region string as well as the string that was identified.
 </p>

 <p>
 In addition to navigating with the mouse, some keyboard shortcuts/commands are also available:
 </p>

 <table>
  <tr><th>Keys</th><th>Behavior</th></tr>
  <tr>
   <td style='text-align:center'>
    <span class='fixed'>&larr;</span>
    <span class='fixed'>&rarr;</span>
    <span class='fixed'>&uarr;</span>
    <span class='fixed'>&darr;</span>
   </td>
   <td>
    Moves the highlighted rectangle by one pixel in the given direction.
   </td>
  </tr>
  <tr>
   <td style='text-align:center'>
    <span class='fixed'>Shift&nbsp;&larr;</span>
    <span class='fixed'>Shift&nbsp;&rarr;</span>
   </td>
   <td>
    Increases or decreases the width of the highlighted rectangle.
   </td>
  </tr>
  <tr>
   <td style='text-align:center'>
    <span class='fixed'>Shift&nbsp;&uarr;</span>
    <span class='fixed'>Shift&nbsp;&darr;</span>
   </td>
   <td>
    Increases or decreases the height of the charset (affects all rectangles).
   </td>
  </tr>
  <tr>
   <td style='text-align:center'>
    <span class='fixed'>Backspace</span>
    <span class='fixed'>Delete</span>
   </td>
   <td>
    <b>Delete</b>. Removes the currently highlighted rectangle from the list.
   </td>
  </tr>
  <tr>
   <td style='text-align:center'>
    <span class='fixed'>D</span>
   </td>
   <td>
    <b>Duplicate</b>. Makes a copy of the currently highlighted rectangle.
    The new rectangle is offset to just below the current rectangle.
   </td>
  </tr>
  <tr>
   <td style='text-align:center'>
    <span class='fixed'>C</span>
   </td>
   <td>
    <b>Toggle Color</b>. Cycles to the next color represented in the selected region. Colors that don’t match the specified color are dimmed, so you can use this as a way to figure out the text color.
   </td>
  </tr>
  <tr>
   <td style='text-align:center'>
    <span class='fixed'>1-8</span>
   </td>
   <td>
    <b>Select Color Slot</b>. Switches between one of 8 color slots to be targeted by Toggle Color. In 256-color and lower modes, you can have up to 8 different colors to match against, while in true-color modes you can have 2 colors.
   </td>
  </tr>
  <tr>
   <td style='text-align:center'>
    <span class='fixed'>Shift&nbsp;C</span>
   </td>
   <td>
    <b>Toggle Secondary Color</b>. Cycles to the next secondary color represented in the selected region.
   </td>
  </tr>
  <tr>
   <td style='text-align:center'>
    <span class='fixed'>H</span>
   </td>
   <td>
    <b>Toggle Half-Width</b>. Switches between normal and half-width mode.
   </td>
  </tr>
  <tr>
   <td style='text-align:center'>
    <span class='fixed'>B</span>
   </td>
   <td>
    <b>Toggle Background</b>. Switches between foreground and background color matching mode.
   </td>
  </tr>
  <tr>
   <td style='text-align:center'>
    <span class='fixed'>E</span>
   </td>
   <td>
    <b>Edit Charset</b>. Enables editing of the matched string. See the <a href='#debugging-csid-charset'>csid Charsets</a> section for more details.
   </td>
  </tr>
  <tr>
   <td style='text-align:center'>
    <span class='fixed'>N</span>
   </td>
   <td>
    <b>Toggle Info</b>. Enables or disables the display of rectangle coordinates (in the top-left corner) and size (in the bottom-right corner).
   </td>
  </tr>
 </table>

 <p>
 For non-destructive commands that operate on the highlighted rectangle, if no rectangle is currently highlighted, the topmost rectangle is implied and becomes highlighted.
 As long as you don’t move the mouse, the highlight will stick with the current rectangle, even if it moves out from under the crosshair.
 Once you move the mouse, all bets are off and the highlighted rectangle tracks with the mouse.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>

 <a name='debugging-csid-charset'></a>
 <h4>csid Charsets</h4>

 <p>
 In addition to defining regions, the <b>csid</b> command also allows you to identify bit patterns as characters, creating charsets that can be included in your .dreamm files.
 </p>

 <p>
 If you’re creating a new character set, pick a unique name and execute <span class='fixed'>cssel <i>&lt;name&gt;</i></span>.
 This creates a blank character set with a height of 8 pixels that you can add new characters to.
 </p>

 <p>
 If you want to modify a character set that’s already in a .dreamm file, use the <b>cslist</b> command to get a list of character sets by name, and then execute <span class='fixed'>cssel <i>&lt;name&gt;</i></span> on it to select it.
 </p>

 <p>
 If you have a charset already saved to disk, then you can load it by running <span class='fixed'>csload <i>&lt;filename&gt;</i></span>.
 Loading a character set implicitly selects the set that you loaded, so no need for a <b>cssel</b> command here.
 </p>

 <p>
 Once loaded, use the <b>csid</b> command on a screen where text appears, and position the rect over a line of text.
 If your character set is larger/smaller than 8 pixels, adjust the height via <span class='fixed'>Shift&nbsp;&uarr;</span>/<span class='fixed'>Shift&nbsp;&darr;</span>.
 Use the <span class='fixed'>C</span> key to cycle through colors until the text is highlighted.
 </p>

 <p>
 At this point, the bottom-left of the screen will show the scraped string, with smiley face characters inserted wherever an unknown character pattern is seen.
 Use the <span class='fixed'>E</span> key to enter edit mode.
 A cursor will appear on the string line at the bottom, and only one of the characters will remain highlighted on the screen.
 </p>

 <p>
 Use the arrow keys to navigate between characters, and type any missing/incorrect characters to assign the highlighted bit pattern to that character.
 When finished, press <span class='fixed'>Enter</span> or <span class='fixed'>Esc</span> to exit.
 </p>

 <p>
 DREAMM assumes that characters are separated by blank columns, but if that’s not the case, you can use <span class='fixed'>Shift&nbsp;&larr;</span> and <span class='fixed'>Shift&nbsp;&rarr;</span> to adjust the width of the highlighted character before typing it to commit the bits to the charset.
 </p>

 <p>
 Save your work using <span class='fixed'>cssave <i>&lt;filename&gt;</i></span>.
 If you loaded your character set via <b>csload</b>, or if you’ve previously saved, you don’t need to re-specify the filename; <b>cssave</b> will use the last filename.
 </p>

</body>
</html>
