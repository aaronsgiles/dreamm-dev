<!doctype html>
<html lang='en-US' xmlns:og='http://opengraphprotocol.org/schema/' xmlns:fb='http://www.facebook.com/2008/fbml'>
<head>
<!--#set var="title" value="DREAMM 4.0 Scripting Documentation" -->
<!--#set var="description" value="Documentation about DREAMM's scripting engine." -->
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
.funcdesc
{
   margin:10px 0 10px;
   font-family:monospace;
   font-size:80%;
   line-height:120%;
   background-color:#CCD0FF;
   padding:8px 10px;
   border-radius:5px;
   border:1px solid #03063A;
   white-space:pre-wrap;
}
.example
{
   margin:20px 20px;
   font-family:monospace;
   font-size:80%;
   line-height:120%;
   background-color:#efefef;
   padding:5px 10px;
   border:1px dotted #03063A;
   white-space:pre-wrap;
}
ul.spaced li { padding-bottom:15px }
ul.spaced li:last-child { padding-bottom:0 }
p ul { margin-top:0 }
</style>
</head>

<body lang='en' class='winbody'>
<div class='page'>
 <div class='logobg'>
  <div class='center'>
   <a href='/'><img src='/img/dreamm-logo.png'></a>
  </div>
 </div>

 <h2 class='center'>DREAMMScript Reference</h2>
 <p class='center'>As of DREAMM 4.0x30</p>

 <p>
 This page describes DREAMM’s internal scripting language.
 It is a work in progress.
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
   <a href='#language'>Language description</a>
   <div class='subtopics'>
    <div class='subtopic'><a href='#basics'>Language basics</a></div>
    <div class='subtopic'><a href='#customs'>Custom objects</a></div>
   </div>
  </li>
  <li>
   <a href='#environments'>Scripting environments</a>
   <div class='subtopics'>
    <div class='subtopic'><a href='#env-condition'>Condition environment</a></div>
    <div class='subtopic'><a href='#env-prelaunch'>Pre-launch environment</a></div>
    <div class='subtopic'><a href='#env-ingame'>In-game environment</a></div>
    <div class='subtopic'><a href='#notify'>Notifications</a></div>
   </div>
  </li>
  <li><a href='#funcs-general'>Built-in functions: General-purpose</a></li>
  <li><a href='#funcs-condition'>Built-in functions: Condition environment</a></li>
  <li>
   <a href='#funcs-prelaunch'>Built-in functions: Pre-launch and in-game environments</a>
   <div class='subtopics'>
    <div class='subtopic'><a href='#funcs-extrastring'>Extra string functions</a></div>
    <div class='subtopic'><a href='#funcs-gameenv'>Game environment functions</a></div>
    <div class='subtopic'><a href='#funcs-disk'>Disk and mount functions</a></div>
    <div class='subtopic'><a href='#funcs-file'>File functions</a></div>
    <div class='subtopic'><a href='#funcs-blob'>Blob functions</a></div>
  </li>
  <li>
   <a href='#funcs-ingame'>Built-in functions: In-game environment only</a>
   <div class='subtopics'>
    <div class='subtopic'><a href='#funcs-notify'>Notification functions</a></div>
    <div class='subtopic'><a href='#funcs-system'>System functions</a></div>
    <div class='subtopic'><a href='#funcs-overlay'>Overlay functions</a></div>
    <div class='subtopic'><a href='#funcs-keyboard'>Keyboard functions</a></div>
    <div class='subtopic'><a href='#funcs-dosmouse'>DOS mouse functions</a></div>
    <div class='subtopic'><a href='#funcs-input'>Misc input functions</a></div>
    <div class='subtopic'><a href='#funcs-scraper'>Screen scraping functions</a></div>
    <div class='subtopic'><a href='#funcs-video'>Video functions</a></div>
    <div class='subtopic'><a href='#funcs-floppy'>Floppy functions</a></div>
    <div class='subtopic'><a href='#funcs-cpu'>CPU functions</a></div>
    <div class='subtopic'><a href='#funcs-cdda'>CD-DA functions</a></div>
    <div class='subtopic'><a href='#funcs-dos'>DOS functions</a></div>
    <div class='subtopic'><a href='#funcs-windows'>Windows functions</a></div>
  </li>
 </ul>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>

 <a name='language'></a>
 <h3>Language description</h3>

 <p>
 DREAMM’s scripting language is a C-like language that is used internally in several places within DREAMM.
 This page describes where it is used, its syntax, and the list of built-in functions.
 </p>

 <a name='basics'></a>
 <h4>Language basics</h4>

 <p>
 DREAMMScript is fundamentally a C-like language, but tailored for a scripting environment.
 </p>

 <ul class='spaced'>

 <li>
 <b>Operators:</b> The standard set of C operators and operator precedence is supported, including ternary ?: and short-circuiting logical and/or.
 However, because DREAMMScript doesn’t support arrays or structures, some operators (like square brackets and the . or -> operators) don’t have any meaning.
 </li>

 <li>
 <b>Control flow:</b> DREAMMScript supports standard C syntax <b>if</b>, <b>for</b>, and <b>while</b> statements (but not <b>do...while</b>).
 </li>

 <li>
 <b>Variable declarations:</b> Variables are not explicitly declared.
 Rather, to declare a variable, just begin using it.
 </li>

 <li>
 <b>Variable types:</b> Variables are not explicitly typed.
 The type of a variable is inferred from its usage.
 Internally, DREAMMScript treats variables as either integers, floating-point, or strings.
 A couple of special <a href='#customs'>custom objects</a> are also used by some built-in functions.
 </li>

 <li>
 <b>Variable scope:</b> All variables in DREAMMScript are global to a given script, even those used in functions.
 This means that functions should use uniquely-named variables.
 It also means that recursion isn’t really possible.
 </li>

 <li>
 <b>Code flow:</b> DREAMMScript code doesn’t have a <span class='fixed'>main</span> function.
 Rather, execution of statements flows through the code (similar to JavaScript).
 </li>

 <li>
 <b>Functions:</b> Functions can be defined via the <span class='fixed'>func</span> keyword.
 Functions can have parameters, which are treated like local variables.
 </li>

 </ul>

 <p>
 Here is an example script showing some of the behaviors described above:
 </p>

 <p class='example'>
 // this is executed first; echo is a built-in function
 echo("Hello");

 // defines a function taking one parameter
 func myfunc(value)
 {
    // this appends value to 'totalstring', which is initialized elsewhere
    totalstring += value;

    // return a value from the function
    // since 'value' is implicitly typed, the result of value + 1 depends on the type
    return value + 1;
 }

 // this implicitly declares a variable named 'totalstring' with a string type
 totalstring = "";

 // call our function
 // after this call 'totalstring' will be "23" and 'result' will be 24
 result = myfunc(23);

 // call our function again
 // after this call 'totalstring' will be "23Skidoo" and 'result' will be "Skidoo1"
 result = myfunc("Skidoo");
 </p>


 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>

 <a name='customs'></a>
 <h4>Custom objects</h4>

 <p>
 DREAMMScript has support for custom objects.
 Currently there are two custom objects defined:
 </p>

 <a name='file'></a>
 <p>
 <b>file</b> objects represent a handle to an open file.
 They are created by <a href='#file_open'>file_open</a> or <a href='#file_create'>file_create</a> functions.
 Remember that since all variables are global, files opened this way remain open until explicitly closed via <a href='#file_close'>file_close</a>.
 </p>

 <ul>
  <li>To check whether a file is valid, convert it to an integer type or use it in a boolean expression. True/non-zero means valid.</li>
  <li>To get the path of the file, convert it to a string type, or use it in a string expression.</li>
 </ul>

 <p class='example'>
 // open a file
 file = file_open("README.TXT");

 // is it valid?
 if (file)
     echo("File path is: " + str(file));

 // close the file
 file_close(file)
 </p>

 <a name='blob'></a>
 <p>
 <b>blob</b> objects represent an arbitrary block of binary data, with an optional mask.
 Blobs are created by <a href='#blob_create'>blob_create</a> and are used in a number of <a href='#func-file'>file</a> and <a href='#func-cpu'>CPU</a> functions as parameters.
 </p>

 <ul>
  <li>To get the size of a blob, convert it to an integer type or use it in an integer expression.</li>
  <li>To get a string representation of the blob, convert it to a string type, or use it in a string expression.</li>
 </ul>

 <p class='example'>
 // create a blob
 blob = blob_create("String", 0);

 // return the size (7 in this case)
 size = int(blob);

 // output the blob contents (careful -- may generate a HUGE string!)
 echo("Blob = ", str(blob));

 // delete the blob by overwriting any variables holding it
 blob = 0;
 </p>


 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>

 <a name='environments'></a>
 <h3>Scripting environments</h3>

 <p>
 The core scripting language is used in three distinct environments, all described within a <a href='dreamm.html'>.dreamm</a> file:
 </p>

 <ul>
  <li><a href='#env-condition'>Condition environment:</a> within a <span class='fixed'>condition</span> statement</li>
  <li><a href='#env-prelaunch'>Pre-launch environment:</a> before a game is launched</li>
  <li><a href='#env-ingame'>In-game environment:</a> while a game is running</li>
 </ul>

 <p>
 Because these environments are quite distinct from each other, they each have their own sets of available <a href='#funcs'>built-in functions</a>.
 </p>

 <a name='env-condition'></a>
 <h4>Condition environment</h4>

 <p>
 The <b>condition environment</b> is the most unique among these three environments.
 Conditions are used in .dreamm files for many objects, and are simple expressions that must ultimately return a Boolean true/false value.
 </p>

 <p>
 Conditions do not support functions or control flow (if/while/for).
 </p>

 <p>
 Conditions are evaluated when creating the environment for a game, which is done both in the frontend when a game variant is selected, and also when the game or an installer/upgrader is launched.
 </p>

 <p>
 Below are some real-world examples of condition expressions:
 </p>

 <p class='example'>
 condition = $(JOYSTICK_WARNING)
 condition = is_installer() || is_upgrader()
 condition = file_exists("$$(rootpath)\\SETUP.EXE")
 </p>

 <p class='important'>
 <b>IMPORTANT:</b> Because scripts are defined in .dreamm files, all dreamm file variable substitution is handled prior to the script being processed.
 See the <a href='dreamm.html#substitution'>.dreamm File Specification</a> for details on how variable substitution is handled.
 </p>

 <p>
 Condition environment expressions have access to the core scripting language, plus these functions:
 </p>

 <ul>
  <li><a href='#funcs-general'>General-purpose functions</a></li>
  <li><a href='#funcs-condition'>Condition environment functions</a></li>
 </ul>

 <a name='env-prelaunch'></a>
 <h4>Pre-launch environment</h4>

 <p>
 The <b>pre-launch environment</b> is available when a script is run outside of the context of a running game.
 At the moment this is used exclusively for custom configuration options, where a script can be directly embedded via the <span class='fixed'>script</span> property.
 </p>

 <p>
 Pre-launch scripts are executed in the frontend when a game variant is selected; when the particular configuration option is selected; and just before the game is launched.
 </p>

 <p>
 Here is a minimal example to show the environment:
 </p>

 <p class='example'>
 config[pilot]:
    title = Pilot
    condition = is_game() || is_demo()
    script = [[[$(CRLF)
        func populate()
        {
            options = "option[-create]: name = New Pilot...\n";
            if (!game_running())
                options += "option[-delete]: name = Delete Pilot...\n";
            return options;
        }
    ]]]
 end
 </p>

 <p>
 Pre-launch environment scripts have access to the full scripting language, plus these functions:
 </p>

 <ul>
  <li><a href='#funcs-general'>General-purpose functions</a></li>
  <li><a href='#funcs-extrastring'>Extra string functions</a></li>
  <li><a href='#funcs-gameenv'>Game environment functions</a></li>
  <li><a href='#funcs-disk'>Disk and mount functions</a></li>
  <li><a href='#funcs-file'>File functions</a></li>
  <li><a href='#funcs-blob'>Blob functions</a></li>
 </ul>

 <a name='env-ingame'></a>
 <h4>In-game environment</h4>

 <p>
 The <b>in-game environment</b> is used when executing a script while a game is actually loaded and running.
 This environment provides the richest set of functions, allowing for quite a bit of manipulation.
 </p>

 <p>
 In-game scripts are executed when the game is launched.
 At this time, they may register for <a href='#notify'>notifications</a> to act on particular events.
 </p>

 <p>
 In-game environment scripts are defined as standalone script objects in .dreamm files:
 </p>

 <p class='example'>
 script[modesel]:
    condition = is_demo()
    code = [[[$(CRLF)
        input_lockout(true);
        wait("int21", 0x0b); keyboard_type("$(VIDEOMODE)");
        input_lockout(false);
    ]]]
 end
 </p>

 <p>
 In-game environment scripts have access to the full language, plus these functions:
 </p>

 <ul>
  <li><a href='#funcs-extrastring'>Extra string functions</a></li>
  <li><a href='#funcs-gameenv'>Game environment functions</a></li>
  <li><a href='#funcs-disk'>Disk and mount functions</a></li>
  <li><a href='#funcs-file'>File functions</a></li>
  <li><a href='#funcs-blob'>Blob functions</a></li>
  <li><a href='#funcs-notify'>Notification functions</a></li>
  <li><a href='#funcs-system'>System functions</a></li>
  <li><a href='#funcs-overlay'>Overlay functions</a></li>
  <li><a href='#funcs-keyboard'>Keyboard functions</a></li>
  <li><a href='#funcs-dosmouse'>DOS mouse functions</a></li>
  <li><a href='#funcs-input'>Misc input functions</a></li>
  <li><a href='#funcs-scraper'>Screen scraping functions</a></li>
  <li><a href='#funcs-video'>Video functions</a></li>
  <li><a href='#funcs-floppy'>Floppy functions</a></li>
  <li><a href='#funcs-cpu'>CPU functions</a></li>
  <li><a href='#funcs-cdda'>CD-DA functions</a></li>
  <li><a href='#funcs-dos'>DOS functions</a></li>
  <li><a href='#funcs-windows'>Windows functions</a></li>
 </ul>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>

 <a name='notify'></a>
 <h4>Notifications</h4>

 <p>
 In the in-game environment, the system will ping the scripting engine with notifications when various events occur.
 Each notification has a name and an optional parameter.
 A few notifications allow callbacks to return a response.
 </p>

 <p>
 These events can be detected and acted on in several ways:
 </p>

 <ul>
  <li>Using the <a href='#wait'>wait</a> function, which blocks execution until the event occurs.</li>
  <li>Using the <a href='#hook'>hook</a> function to register a script function that will be called when the event occurs.</li>
  <li>Defining a script function with the same name as the notification, and it will be implicitly registered as if <a href='#hook'>hook</a> had be called.</li>
 </ul>

 <p class='important'>
 <b>IMPORTANT:</b> Functions registered with <a href='#hook'>hook</a> (either explicitly or implicitly) are expected to complete quickly, and thus cannot call blocking functions.
 If you wish to call a blocking function from one of these handlers, use <a href='#script_run_async'>script_run_async</a> to call another function asynchronously, and have that function make the blocking calls.
 </p>

 <a name='notifications'></a>
 <p>
 The following notifications are currently defined for all OS environments:
 </p>

 <table>
  <tr><th>Notification</th><th>Description</th></tr>
  <tr>
   <td style='text-align:left; font-family:monospace; font-size:80%'>exit</td>
   <td>
    Signalled when the system is exiting.<br>
    <b>Parameter:</b> none
   </td>
  </tr>
  <tr>
   <td style='text-align:left; font-family:monospace; font-size:80%'>progload</td>
   <td>
    Signalled when a new program is loaded, before execution.<br>
    In Windows environments, this is called after the EXE and all dependencies are processed.<br>
    <b>Parameter:</b> (string) the EXE filename (not full path)
   </td>
  </tr>
  <tr>
   <td style='text-align:left; font-family:monospace; font-size:80%'>progexit</td>
   <td>
    Signalled when a program exits.<br>
    <b>Parameter:</b> (string) the EXE filename (not full path)
   </td>
  </tr>
  <tr>
   <td style='text-align:left; font-family:monospace; font-size:80%'>update</td>
   <td>
    Signalled whenever a new snapshot of the video screen is completed.<br>
    <b>Parameter:</b> none
   </td>
  </tr>
  <tr>
   <td style='text-align:left; font-family:monospace; font-size:80%'>vsync</td>
   <td>
    Signalled at the start and end of VSYNC.<br>
    <b>Parameter:</b> (integer) 1 for VSYNC start, 0 for VSYNC end.
   </td>
  </tr>
  <tr>
   <td style='text-align:left; font-family:monospace; font-size:80%'>videomode</td>
   <td>
    Signalled whenever the video mode changes.<br>
    <b>Parameter:</b> (integer) mode encoded as <span class='fixed'>((width &lt;&lt; 20) | (height &lt;&lt; 8) | depth)</span>.
   </td>
  </tr>
 </table>

 <p>
 The following notifications are currently defined for DOS environments:
 </p>

 <table>
  <tr><th>Notification</th><th>Description</th></tr>
  <tr>
   <td style='text-align:left; font-family:monospace; font-size:80%'>int16</td>
   <td>
    Signalled whenever an INT 16h call (BIOS keyboard) is made.<br>
    <b>Parameter:</b> (integer) the value of AH<br>
    <b>Return:</b> a function registered with <a href='#hook'>hook</a> can return true to suppress the call
   </td>
  </tr>
  <tr>
   <td style='text-align:left; font-family:monospace; font-size:80%'>int21</td>
   <td>
    Signalled whenever an INT 21h call (DOS services) is made.<br>
    <b>Parameter:</b> (integer) the value of AH<br>
    <b>Return:</b> a function registered with <a href='#hook'>hook</a> can return true to suppress the call
   </td>
  </tr>
  <tr>
   <td style='text-align:left; font-family:monospace; font-size:80%'>int2f</td>
   <td>
    Signalled whenever an INT 2Fh call (DOS multiplexed services) is made.<br>
    <b>Parameter:</b> (integer) the value of AH<br>
    <b>Return:</b> a function registered with <a href='#hook'>hook</a> can return true to suppress the call
   </td>
  </tr>
  <tr>
   <td style='text-align:left; font-family:monospace; font-size:80%'>int33</td>
   <td>
    Signalled whenever an INT 33h call (DOS mouse) is made.<br>
    <b>Parameter:</b> (integer) the value of AX<br>
    <b>Return:</b> a function registered with <a href='#hook'>hook</a> can return true to suppress the call
   </td>
  </tr>
  <tr>
   <td style='text-align:left; font-family:monospace; font-size:80%'>mouseirq</td>
   <td>
    Signalled whenever a DOS mouse user polling IRQ is generated.<br>
    <b>Parameter:</b> none
   </td>
  </tr>
 </table>

 <p>
 The following notifications are currently defined for Windows environments:
 </p>

 <table>
  <tr><th>Notification</th><th>Description</th></tr>
  <tr>
   <td style='text-align:left; font-family:monospace; font-size:80%'>wintitle</td>
   <td>
    Signalled whenever a new window becomes active.<br>
    <b>Parameter:</b> (string) the name of the window
   </td>
  </tr>
  <tr>
   <td style='text-align:left; font-family:monospace; font-size:80%'>winclass</td>
   <td>
    Signalled whenever a new window becomes active.<br>
    <b>Parameter:</b> (string) the class name of the window
   </td>
  </tr>
  <tr>
   <td style='text-align:left; font-family:monospace; font-size:80%'>winmodload</td>
   <td>
    Signalled whenever a new module (EXE/DLL) is loaded, before imports are processed.<br>
    <b>Parameter:</b> (string) the filename of the module (not full path)
   </td>
  </tr>
 </table>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>

 <a name='funcs-general'></a>
 <h3>General-purpose functions</h3>

 <p>
 These functions are available to all scripts, both <b>condition</b> expressions as well as system scripts.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='chr'></a>
 <h5>chr</h5>
 <p class='funcdesc'>chr(<i>unicode</i>)</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>unicode</i></b> &mdash; integer specifying the Unicode character to generate</li>
 </ul>

 <p>
 Returns a string consisting of the single Unicode character represented by <b><i>unicode</i></b>.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='float'></a>
 <h5>float</h5>
 <p class='funcdesc'>float(<i>value</i>)</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>value</i></b> &mdash; any variable type that you want to convert to a floating-point value</li>
 </ul>

 <p>
 Explicitly coerces <b><i>value</i></b> into floating-point form:
 String values are parsed into floating-point values, and integer values are promoted to floating-point.
 </p>
 <p>
 The behavior of other value types depends on the particular type.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='hexstr'></a>
 <h5>hexstr</h5>
 <p class='funcdesc'>hexstr(<i>value</i>[, <i>mindigits</i>])</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>value</i></b> &mdash; integer value you wish to represent</li>
 <li><b><i>mindigits</i></b> (optional) &mdash; integer specifying the minimum number of digits in the output</li>
 </ul>

 <p>
 Converts <b><i>value</i></b> to an integer and then produces a hexadecimal string.
 </p>
 <p>
 If <b><i>mindigits</i></b> is specifed, then the resulting string will have at least <b><i>mindigits</i></b> digits in it.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='if'></a>
 <h5>if</h5>
 <p class='funcdesc'>if(<i>bool</i>, <i>trueval</i>, <i>falseval</i>)</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>bool</i></b> &mdash; Boolean value to test</li>
 <li><b><i>trueval</i></b> &mdash; any variable type that you want to return if <b><i>bool</i></b> is true</li>
 <li><b><i>falseval</i></b> &mdash; any variable type that you want to return if <b><i>bool</i></b> is false</li>
 </ul>

 <p>
 Selects either <b><i>trueval</i></b> or <b><i>falseval</i></b> based on whether <b><i>bool</i></b> evaluates to true or false.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='int'></a>
 <h5>int</h5>
 <p class='funcdesc'>int(<i>value</i>)</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>value</i></b> &mdash; any variable type that you want to convert to an integer</li>
 </ul>

 <p>
 Explicitly coerces <b><i>value</i></b> into integral form:
 String values are parsed into integers; floating-point values are truncated to integers.
 </p>
 <p>
 The behavior of other value types depends on the particular type.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='ord'></a>
 <h5>ord</h5>
 <p class='funcdesc'>ord(<i>string</i>)</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>string</i></b> &mdash; string value to operate on</li>
 </ul>

 <p>
 Returns an integer representing the Unicode value of the first character of <b><i>string</i></b>.
 </p>
 <p>
 This function is the opposite of <a href='#chr'>chr</a>.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='str'></a>
 <h5>str</h5>
 <p class='funcdesc'>str(<i>value</i>)</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>value</i></b> &mdash; any variable type that you want to convert to a string</li>
 </ul>

 <p>
 Explicitly coerces <b><i>value</i></b> into string form:
 Integer and float values are converted to strings.
 </p>
 <p>
 The behavior of other value types depends on the particular type.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='strlen'></a>
 <h5>strlen</h5>
 <p class='funcdesc'>strlen(<i>string</i>)</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>string</i></b> &mdash; string whose length you wish to query</li>
 </ul>

 <p>
 Returns the length, in characters, of the provided <b><i>string</i></b>.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='strrstr'></a>
 <h5>strrstr</h5>
 <p class='funcdesc'>strrstr(<i>haystack</i>, <i>needle</i>)</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>haystack</i></b> &mdash; string you wish to search</li>
 <li><b><i>needle</i></b> &mdash; string you wish to search for</li>
 </ul>

 <p>
 Returns the position of the last occurrence of <b><i>needle</i></b> in the string <b><i>haystack</i></b>.
 </p>
 <p>
 If <b><i>needle</i></b> is not found, this function returns -1.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='strstr'></a>
 <h5>strstr</h5>
 <p class='funcdesc'>strstr(<i>haystack</i>, <i>needle</i>[, <i>startpos</i>])</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>haystack</i></b> &mdash; string you wish to search</li>
 <li><b><i>needle</i></b> &mdash; string you wish to search for</li>
 <li><b><i>startpos</i></b> (optional) &mdash; integer specifying the position within <b><i>haystack</i></b> to begin searching</li>
 </ul>

 <p>
 Returns the position of the first occurrence of <b><i>needle</i></b> in the string <b><i>haystack</i></b>.
 </p>
 <p>
 If <b><i>startpos</i></b> is provided, the search starts from that character index; otherwise, the search starts at the beginning of the string.
 </p>
 <p>
 If <b><i>needle</i></b> is not found, this function returns -1.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='substr'></a>
 <h5>substr</h5>
 <p class='funcdesc'>substr(<i>string</i>, <i>start</i>[, <i>length</i>])</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>string</i></b> &mdash; string value to extract the substring from</li>
 <li><b><i>start</i></b> &mdash; integer specifying the first character to start</li>
 <li><b><i>length</i></b> (optional) &mdash; integer specifying the number of characters to extract</li>
 </ul>

 <p>
 Returns a copy of a substring of <b><i>string</i></b>, starting at character position <b><i>start</i></b> and consisting of <b><i>length</i></b> characters.
 </p>
 <p>
 If <b><i>length</i></b> is omitted, the substring includes all characters up to the end of the string.
 </p>
 <p>
 If <b><i>start</i></b> is negative, it represents a position from the end of the string; for example, setting <b><i>start</i></b> to -3 would indicate a position 3 characters from the end.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>

 <a name='funcs-condition'></a>
 <h3>Condition environment functions</h3>

 <p>
 These functions are available only when used in <span class='fixed'>condition</span> expressions.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='file_exists_cond'></a>
 <h5>file_exists</h5>
 <p class='funcdesc'>file_exists(<i>filename</i>)</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>filename</i></b> &mdash; string describing the path to the file to test</li>
 </ul>

 <p>
 Returns true if the given <b><i>filename</i></b> exists in the game’s virtual file system.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='file_size_cond'></a>
 <h5>file_size</h5>
 <p class='funcdesc'>file_size(<i>filename</i>)</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>filename</i></b> &mdash; string describing the path to the file to query</li>
 </ul>

 <p>
 Returns the size of <b><i>filename</i></b> if it is present within the game’s virtual file system; otherwise it returns 0.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='is_composite'></a>
 <h5>is_composite</h5>
 <p class='funcdesc'>is_composite()</p>

 <p>
 Returns true if the composite video is enabled.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='is_dos'></a>
 <a name='is_windows'></a>
 <a name='is_fmtowns'></a>
 <h5>is_dos, is_windows, is_fmtowns</h5>
 <p class='funcdesc'>is_dos()
is_windows()
is_fmtowns()</p>

 <p>
 Returns true if the <a href='dreamm.html#sysprops'>system property</a> <b>osenv</b> corresponds to the given operating system.
 </p>
 <p>
 These functions are just more friendly ways of querying <span class='fixed'>prop("osenv")</span> and dissecting the results.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='is_game'></a>
 <a name='is_demo'></a>
 <a name='is_installer'></a>
 <a name='is_upgrader'></a>
 <h5>is_game, is_demo, is_installer, is_upgrader</h5>
 <p class='funcdesc'>is_game()
is_demo()
is_installer()
is_upgrader()</p>

 <p>
 Returns true if the current environment is derived from a variant of the given type.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='is_hercules'></a>
 <a name='is_cga'></a>
 <a name='is_ega'></a>
 <a name='is_mcga'></a>
 <a name='is_vga'></a>
 <a name='is_svga'></a>
 <h5>is_hercules, is_ega, is_mcga, is_vga, is_svga</h5>
 <p class='funcdesc'>is_hercules()
is_cga()
is_ega()
is_mcga()
is_vga()
is_svga()</p>

 <p>
 Returns true if the given video mode is enabled.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='prop_cond'></a>
 <h5>prop</h5>
 <p class='funcdesc'>prop(<i>name</i>)</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>name</i></b> &mdash; string providing the name of the property you wish to query</li>
 </ul>

 <p>
 Returns the string value of the property <b><i>name</i></b> in the current environment’s list of <a href='dreamm.html#sysprops'>system properties</a>.
 </p>
 <p>
 If no property by the name of <b><i>name</i></b> exists, this function will return an empty string.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>

 <a name='funcs-prelaunch'></a>
 <a name='funcs-extrastring'></a>
 <h3>Extra string functions</h3>

 <p>
 These functions are available as additions to the <a href='#funcs-general'>standard set</a> of general purpose functions when running in either the pre-launch or in-game environments.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='echo'></a>
 <h5>echo</h5>
 <p class='funcdesc'>echo(<i>value</i>[, <i>value</i>...])</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>value</i></b> &mdash; string(s) to be output</li>
 </ul>

 <p>
 Outputs one or more values as text to the <b>script</b> log stream.
 </p>
 <p>
 Each <b><i>value</i></b> is converted to a string and concatenated into a a final string, which is output as a single line.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='make_id'></a>
 <h5>make_id</h5>
 <p class='funcdesc'>make_id(<i>string</i>)</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>string</i></b> &mdash; value to convert into an id</li>
 </ul>

 <p>
 Converts the given <b><i>string</i></b> to a valid ID that meets the strictest requirements for .dreamm object IDs.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='strmap'></a>
 <h5>strmap</h5>
 <p class='funcdesc'>strmap(<i>key</i>, <i>map</i>)</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>key</i></b> &mdash; string containing the key to look up</li>
 <li><b><i>map</i></b> &mdash; string containing the map</li>
 </ul>

 <p>
 Looks up the given <b><i>key</i></b> in the map string <b><i>map</i></b>.
 </p>
 <p>
 The first character of <b><i>map</i></b> specifies the separator between keys and values in the <b><i>map</i></b>, and the second character of <b><i>map</i></b> specifies the separator between entries in the <b><i>map</i></b>.
 </p>
 <p>
 For example:
 </p>
 <p class='example'>val = strmap("bar", "=;foo=1;bar=2")</p>
 <p>
 would split the second string into two entries using the second character (semicolon), and would treat each entry as a key/value pair separated by the first character (equal sign).
 In this example, the resulting value would be 2.
 </p>
 <p>
 Note that the format of <b><i>map</i></b> is well-suited to the <a href='https://dreamm.aarongiles.com/docs/v40/dreamm.html#multiline'>multi-line values</a> support in .dreamm files:
 </p>
 <p class='example'> MAP = [[[; // specifies to insert a ; character after each line
    =       // first character is separator; the ; is inserted after it
    foo=1   // each line has a ; inserted after
    bar=2
 ]]]

 script[example]:
   code = strmap("bar", "$$(MAP)");
 end</p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='strword'></a>
 <h5>strword</h5>
 <p class='funcdesc'>strword(<i>index</i>, <i>string</i>)</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>index</i></b> &mdash; integer index of the word to return</li>
 <li><b><i>map</i></b> &mdash; string containing words to be returned</li>
 </ul>

 <p>
 Returns word number <b><i>index</i></b> from the given <b><i>string</i></b>.
 Words are assumed to be separated by spaces.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>

 <a name='funcs-gameenv'></a>
 <h3>Game environment functions</h3>

 <p>
 These functions can query and manipulate the game’s environment.
 Game environment functions are available in both pre-launch and in-game environments.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='env_get'></a>
 <h5>env_get</h5>
 <p class='funcdesc'>env_get(<i>name</i>)</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>name</i></b> &mdash; string providing the name of the environment variable to look up</li>
 </ul>

 <p>
 Returns the current environment string associated with <b><i>name</i></b>, or an empty string if there is no value defined.
 </p>
 <p>
 This can be used at initialization time to set up or modify the game’s environment variables.
 </p>
 <p>
 Note that this reflects the environment as it will be passed to the system when starting the game, and won’t reflect any changes made by the game once it starts running.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='env_set'></a>
 <h5>env_set</h5>
 <p class='funcdesc'>env_set(<i>name</i>, <i>value</i>)</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>name</i></b> &mdash; string providing the name of the environment variable to set</li>
 <li><b><i>value</i></b> &mdash; string containing the new value</li>
 </ul>

 <p>
 Sets the environment string associated with <b><i>name</i></b> to <b><i>value</i></b>, replacing any existing value.
 </p>
 <p>
 This can be used at initialization time to set up or modify the game’s environment variables.
 </p>
 <p>
 Note that this only affects the environment as it will be passed to the system when starting the game; once the game is running, this function won’t alter the running environment.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='game_id'></a>
 <h5>game_id</h5>
 <p class='funcdesc'>game_id()</p>

 <p>
 Returns the ID of the currently-running system.
 </p>
 <p>
 This ID is a concatenation of the descriptor ID and the variant ID, separated by a colon (<span class='fixed'>:</span>).
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='game_running'></a>
 <h5>game_running</h5>
 <p class='funcdesc'>game_running()</p>

 <p>
 Returns true if the system is currently executing.
 </p>
 <p>
 This is useful to distinguish between the pre-launch and game environments, for scripts that might execute in both situations.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='prop'></a>
 <h5>prop</h5>
 <p class='funcdesc'>prop(<i>name</i>)</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>name</i></b> &mdash; string providing the name of the property you wish to query</li>
 </ul>

 <p>
 Returns the string value of the property <b><i>name</i></b> in the current environment’s list of system properties.
 </p>
 <p>
 If <b><i>name</i></b> doesn’t exist, it will return an empty string.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>

 <a name='funcs-disk'></a>
 <h3>Disk and mount functions</h3>

 <p>
 These functions let you control the virtual filesystem configuration at the disk or mount level.
 Disk and mount functions are available in both pre-launch and in-game environments.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='disc_insert'></a>
 <h5>disc_insert</h5>
 <p class='funcdesc'>disc_insert(<i>name</i>[, <i>writeprotected</i>])</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>name</i></b> &mdash; string describing the name of the disc to insert</li>
 <li><b><i>writeprotected</i></b> (optional) &mdash; Boolean value indicating whether to insert the disc as write protected</li>
 </ul>

 <p>
 Changes the currently-inserted disc to <b><i>name</i></b>, setting its read-only status to <b><i>writeprotected</i></b>.
 If <b><i>writeprotected</i></b> is not specified, the disc is inserted read/write.
 </p>
 <p>
 This is only applicable if your game description contains <a href='https://dreamm.aarongiles.com/docs/v40/dreamm.html#mount'>mounts</a> with the <b>disc</b> property.
 </p>
 <p>
 Returns true if a valid disc name was provided, or false otherwise.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='drive_set_label'></a>
 <h5>drive_set_label</h5>
 <p class='funcdesc'>drive_set_label(<i>letter</i>, <i>label</i>)</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>letter</i></b> &mdash; string specifying the drive letter of the disc to modify</li>
 <li><b><i>label</i></b> &mdash; value to return for the volume label</li>
 </ul>

 <p>
 Sets the disc label for the given drive.
 </p>
 <p>
 Note that the label tracks with the current disc, so if you use disc_insert() to change discs, the label will change along with the set of discs.
 This means if you have multiple discs you are swapping between, and the label needs to be different between them, you will need to use disc_insert() to select the disc you want to set the label on prior to calling this function.
 </p>
 <p>
 Returns true if the change was successful, or false otherwise.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='mount_set_writable'></a>
 <h5>mount_set_writable</h5>
 <p class='funcdesc'>mount_set_writable(<i>id</i>, <i>writable</i>)</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>id</i></b> &mdash; string specifying the id of the mount to modify</li>
 <li><b><i>writable</i></b> &mdash; Boolean value indicating whether to the mount should be writable</li>
 </ul>

 <p>
 Modifies the writable state of the given mount.
 Only mounts that have bracketed ids can be modified in this way.
 </p>
 <p>
 If the srcpath of the given mount points to the $(ifsinstallpath), then any attempts to enable writing will fail.
 </p>
 <p>
 Returns true if the change was successful, or false otherwise.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>

 <a name='funcs-file'></a>
 <h3>File functions</h3>

 <p>
 These functions let you examine, create, and modify files within the game’s virtual filesystem.
 File functions are available in both pre-launch and in-game environments.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='file_close'></a>
 <h5>file_close</h5>
 <p class='funcdesc'>file_close(<i>file</i>)</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>file</i></b> &mdash; file object to close</li>
 </ul>

 <p>
 Closes the given <b><i>file</i></b> object, which must have been opened by <a href='#file_create'>file_create</a> or <a href='#file_open'>file_open</a>.
 </p>
 <p>
 Returns true if <b><i>file</i></b> was successfully closed.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='file_copy'></a>
 <h5>file_copy</h5>
 <p class='funcdesc'>file_copy(<i>destname</i>, <i>srcname</i>[, <i>srcname</i>...])</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>destname</i></b> &mdash; string containing path to the target file</li>
 <li><b><i>srcname</i></b> &mdash; one or more strings containing path(s) to the source file(s)</li>
 </ul>

 <p>
 Copies the contents of the file at <b><i>srcname</i></b> to a new file at <b><i>destname</i></b>.
 </p>
 <p>
 If more than one <b><i>srcname</b></i> is provided, then the contents of all provided files are concatenated to the result.
 </p>
 <p>
 This function returns the number of source files it successfully copied.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='file_crc'></a>
 <h5>file_crc</h5>
 <p class='funcdesc'>file_crc(<i>filename</i>)</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>filename</i></b> &mdash; string containing path to the file whose contents you want a CRC computed for</li>
 </ul>

 <p>
 Loads the file specified by the given <b><i>filename</i></b> and computes the file’s CRC-32.
 </p>
 <p>
 If the file is not found, this function will return 0.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='file_create'></a>
 <h5>file_create</h5>
 <p class='funcdesc'>file_create(<i>filename</i>)</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>filename</i></b> &mdash; string containing path to the file you wish to create</li>
 </ul>

 <p>
 Attempts to create a new file named <b><i>filename</i></b>.
 </p>
 <p>
 If the file already exists, it will be overwritten and truncated.
 </p>
 <p>
 This function returns a file object; you can test for success by checking <span class='fixed'>if (file)</span>.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='file_delete'></a>
 <h5>file_delete</h5>
 <p class='funcdesc'>file_delete(<i>filename</i>[, <i>filename</i>...])</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>filename</i></b> &mdash; one or more strings containing path(s) to the file(s) you wish to delete</li>
 </ul>

 <p>
 Deletes one or more files, specified by <b><i>filename</i></b>.
 </p>
 <p>
 This function returns the number of files it successfully deleted.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='file_exists'></a>
 <h5>file_exists</h5>
 <p class='funcdesc'>file_exists(<i>filename</i>)</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>filename</i></b> &mdash; string describing the path to the file to test</li>
 </ul>

 <p>
 Returns true if the given <b><i>filename</i></b> exists in the game’s virtual file system.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='file_load_blob'></a>
 <h5>file_load_blob</h5>
 <p class='funcdesc'>file_load_blob(<i>filename</i>)</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>filename</i></b> &mdash; string containing the path of the file to load</li>
 </ul>

 <p>
 Reads the full contents of <b><i>filename</i></b> into a <a href='#blob'>blob object</a>.
 </p>
 <p>
 If the file is not found, the size of the resulting blob will be 0.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='file_load_string'></a>
 <h5>file_load_string</h5>
 <p class='funcdesc'>file_load_string(<i>filename</i>)</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>filename</i></b> &mdash; string containing the path of the file to load</li>
 </ul>

 <p>
 Reads the full contents of <b><i>filename</i></b> into a string.
 </p>
 <p>
 If the file is not found, this function returns an empty string.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='file_open'></a>
 <h5>file_open</h5>
 <p class='funcdesc'>file_open(<i>filename</i>[, <i>writable</i>])</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>filename</i></b> &mdash; string containing the path of the file to open</li>
 <li><b><i>writable</i></b> (optional) &mdash; Boolean value specifying whether the file is open for write as well as reads</li>
 </ul>

 <p>
 Attempts to open an existing file named <b><i>filename</i></b>.
 </p>
 <p>
 By default the file is opened as read-only, but if you pass a <b><i>writable</i></b> value that evaluates to true, the file will be opened read-write.
 </p>
 <p>
 This function returns a file object; you can test for success by checking <span class='fixed'>if (file)</span>.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='file_read'></a>
 <h5>file_read</h5>
 <p class='funcdesc'>file_read(<i>file</i>, <i>count</i>)</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>file</i></b> &mdash; file object to read from</li>
 <li><b><i>count</i></b> &mdash; integer specifying the number of bytes to read</li>
 </ul>

 <p>
 Reads <b><i>count</i></b> bytes from the <b><i>file</i></b> at the current position
 </p>
 <p>
 The data is returned as a <a href='#blob'>blob object</a>; the size of the resulting blob tells you how many bytes were read.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='file_read_stringz'></a>
 <h5>file_read_stringz</h5>
 <p class='funcdesc'>file_read_stringz(<i>file</i>)</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>file</i></b> &mdash; file object to read from</li>
 </ul>

 <p>
 Reads a null-terminated string from <b><i>file</i></b> at the current position.
 </p>
 <p>
 If the file contains no nulls, then the entire file contents are read.
 </p>
 <p>
 Returns the result as a string.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='file_save'></a>
 <h5>file_save</h5>
 <p class='funcdesc'>file_save(<i>filename</i>, [<i>data</i>...])</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>filename</i></b> &mdash; string containing the path of the file to create</li>
 <li><b><i>data</i></b> (optional) &mdash; one or more data items to write to the file</li>
 </ul>

 <p>
 Creates a new file named <b><i>filename</i></b> and writes the provided <b><i>data</i></b> to it.
 </p>
 <p>
 The <b><i>data</i></b> to write is specified in the same format as for <a href='#blob_create'>blob_create</a>.
 </p>
 <p>
 Returns true if successful, or false on a failure.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='file_seek'></a>
 <h5>file_seek</h5>
 <p class='funcdesc'>file_seek(<i>file</i>[, <i>offset</i>[, <i>whence</i>]])</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>file</i></b> &mdash; file object to seek within</li>
 <li><b><i>offset</i></b> (optional) &mdash; integer offset to seek to</li>
 <li><b><i>whence</i></b> (optional) &mdash; integer describing whether to seek absolutely, from the current position, or from the end</li>
 </ul>

 <p>
 Seeks to a new position within an open <b><i>file</b></i> object.
 </p>
 <p>
 If <b><i>offset</i></b> is not specified, then this function does nothing except return the current file position.
 </p>
 <p>
 If <b><i>whence</i></b> is either 0 or not specified, then <b><i>offset</i></b> is treated as an absolute position.
 </p>
 <p>
 If <b><i>whence</i></b> is 1, then <b><i>offset</i></b> is relative to the current file position.
 </p>
 <p>
 If <b><i>whence</i></b> is 2, then <b><i>offset</i></b> is relative to the current end of file.
 </p>
 <p>
 This function returns the resulting file position.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='file_size'></a>
 <h5>file_size</h5>
 <p class='funcdesc'>file_size(<i>filename</i>)</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>filename</i></b> &mdash; string describing the path to the file to query</li>
 </ul>

 <p>
 Returns the size of <b><i>filename</i></b> if it is present within the game’s virtual file system; otherwise it returns 0.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='file_write'></a>
 <h5>file_write</h5>
 <p class='funcdesc'>file_write(<i>file</i>, [<i>data</i>...])</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>file</i></b> &mdash; file object to write to</li>
 <li><b><i>data</i></b> (optional) &mdash; one or more data items to write to the file</li>
 </ul>

 <p>
 Writes data to the given <b><i>file</i></b> at the current position.
 </p>
 <p>
 The <b><i>data</i></b> to write is specified in the same format as for <a href='#blob_create'>blob_create</a>.
 </p>
 <p>
 Returns the number of bytes written.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='path_cat'></a>
 <h5>path_cat</h5>
 <p class='funcdesc'>path_cat(<i>part</i>, [<i>part</i>...])</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>part</i></b> &mdash; one or more strings containing partial paths to assemble</li>
 </ul>

 <p>
 Concatenates each <b><i>part</i></b> provided, separated by backslashes, into a final path string.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='path_change'></a>
 <h5>path_change</h5>
 <p class='funcdesc'>path_change(<i>path</i>)</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>path</i></b> &mdash; string describing the directory to set as current</li>
 </ul>

 <p>
 Changes the current directory to <b><i>path</i></b>.
 </p>
 <p>
 Returns true on success, or false on failure.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='path_create'></a>
 <h5>path_create</h5>
 <p class='funcdesc'>path_create(<i>path</i>)</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>path</i></b> &mdash; string describing the path to the directory to create</li>
 </ul>

 <p>
 Creates a new directory named <b><i>path</i></b>.
 </p>
 <p>
 Returns true on success, or false on failure.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='path_enumerate'></a>
 <h5>path_enumerate</h5>
 <p class='funcdesc'>path_enumerate(<i>pathspec</i>, <i>function</i>)</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>pathspec</i></b> &mdash; string describing the file specification to scan for</li>
 <li><b><i>function</i></b> &mdash; string providing the name of a function to call for each entry</li>
 </ul>

 <p>
 Enumerates all the files present in <b><i>pathspec</i></b>, by calling <b><i>function</i></b> for each one.
 </p>
 <p>
 <b><i>pathspec</i></b> can be either a directory or filename, and can contain wildcards.
 </p>
 <p>
 <b><i>function</i></b> is passed as a string and should be defined as:
 </p>
 <p class='example'>func enum_callback(filename)</p>
 <p>
 Returns the number of files enumerated.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='path_stem'></a>
 <h5>path_stem</h5>
 <p class='funcdesc'>path_stem(<i>path</i>)</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>path</i></b> &mdash; string containing the path to extract the stem from</li>
 </ul>

 <p>
 Returns the stem (aka filename minus extension) of the given path.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>

 <a name='funcs-blob'></a>
 <h3>Blob functions</h3>

 <p>
 These functions let you create and manipulate blobs, which are custom objects containing binary data and an optional mask.
 Blob functions are available in both pre-launch and in-game environments.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='blob_create'></a>
 <h5>blob_create</h5>
 <p class='funcdesc'>blob_create([<i>data</i>[, <i>data</i>...]])</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>data</i></b> (optional) &mdash; one or more data items to insert into the blob</li>
 </ul>

 <p>
 Creates a new empty blob object.
 </p>
 <p>
 If any <b><i>data</i></b> parameters are provided, they are appended in order to the empty blob, with each parameter treated as follows:
 </p>
 <ul>
  <li>strings &mdash; raw string bytes are appended to the blob</li>
  <li>extended strings &mdash; strings beginning with a <span class='fixed'>`</span> character are extended strings, described below</li>
  <li>blobs &mdash; other blob objects are appended in their entirety, including their masks</li>
  <li>positive integers &mdash; values are appended at the smallest size that can contain them, either 1, 2, 4, or 8 bytes</li>
  <li>negative integers &mdash; indicate one or more &ldquo;empty&rdquo; bytes, setting the mask to 0; the absolute value specifies the number of bytes to append</li>
 </ul>
 <p>
 If a string value beginning with a <span class='fixed'>`</span> character is specified, it indicates an &ldquo;extended string,&rdquo; which is a concatenation of one or more of the following special sequences:
 </p>
 <table>
  <tr><th>Sequence</th><th>Description</th></tr>
  <tr>
   <td style='text-align:left; font-family:monospace; font-size:80%'>`b:<i>&lt;value&gt;</i>[,<i>&lt;value&gt;</i>...]</td>
   <td>
    Appends one or more comma-separated byte values.
    Values are decimal values by default unless prefixed by an 0x prefix to indicate they are hexadecimal values.
   </td>
  </tr>
  <tr>
   <td style='text-align:left; font-family:monospace; font-size:80%'>`w:<i>&lt;value&gt;</i>[,<i>&lt;value&gt;</i>...]</td>
   <td>
    Appends one or more comma-separated little-endian word (2-byte) values.
    Values are decimal values by default unless prefixed by an 0x prefix to indicate they are hexadecimal values.
   </td>
  </tr>
  <tr>
   <td style='text-align:left; font-family:monospace; font-size:80%'>`W:<i>&lt;value&gt;</i>[,<i>&lt;value&gt;</i>...]</td>
   <td>
    Appends one or more comma-separated big-endian word (2-byte) values.
    Values are decimal values by default unless prefixed by an 0x prefix to indicate they are hexadecimal values.
   </td>
  </tr>
  <tr>
   <td style='text-align:left; font-family:monospace; font-size:80%'>`d:<i>&lt;value&gt;</i>[,<i>&lt;value&gt;</i>...]</td>
   <td>
    Appends one or more comma-separated little-endian dword (4-byte) values.
    Values are decimal values by default unless prefixed by an 0x prefix to indicate they are hexadecimal values.
   </td>
  </tr>
  <tr>
   <td style='text-align:left; font-family:monospace; font-size:80%'>`D:<i>&lt;value&gt;</i>[,<i>&lt;value&gt;</i>...]</td>
   <td>
    Appends one or more comma-separated big-endian dword (4-byte) values.
    Values are decimal values by default unless prefixed by an 0x prefix to indicate they are hexadecimal values.
   </td>
  </tr>
  <tr>
   <td style='text-align:left; font-family:monospace; font-size:80%'>`q:<i>&lt;value&gt;</i>[,<i>&lt;value&gt;</i>...]</td>
   <td>
    Appends one or more comma-separated little-endian qword (8-byte) values.
    Values are decimal values by default unless prefixed by an 0x prefix to indicate they are hexadecimal values.
   </td>
  </tr>
  <tr>
   <td style='text-align:left; font-family:monospace; font-size:80%'>`Q:<i>&lt;value&gt;</i>[,<i>&lt;value&gt;</i>...]</td>
   <td>
    Appends one or more comma-separated big-endian qword (8-byte) values.
    Values are decimal values by default unless prefixed by an 0x prefix to indicate they are hexadecimal values.
   </td>
  </tr>
  <tr>
   <td style='text-align:left; font-family:monospace; font-size:80%'>`h:<i>&lt;hexstream&gt;</i></td>
   <td>
    Appends a series of byte values from the given <i>&lt;hexstream&gt;</i>.
    (This is identical to the old <span class='fixed'>HEX:</span> string parsing.)
    1 or 2 valid hex characters are consumed for each byte.
    All non-hex characters are ignored.
   </td>
  </tr>
  <tr>
   <td style='text-align:left; font-family:monospace; font-size:80%'>`s:<i>&lt;string&gt;</i></td>
   <td>
    Appends a string value, starting immediately after the colon and ending at either the next <span class='fixed'>`</span> character or the end of string.
    Each character in <i>&lt;string&gt;</i> is appended as-is to the blob.
   </td>
  </tr>
  <tr>
   <td style='text-align:left; font-family:monospace; font-size:80%'>`f:<i>&lt;fill&gt;</i></td>
   <td>
    Sets the fill byte for the pad operation, described below.
    This does not append any data to the stream.
    The default fill byte is 0.
   </td>
  </tr>
  <tr>
   <td style='text-align:left; font-family:monospace; font-size:80%'>`p:<i>&lt;length&gt;</i></td>
   <td>
    Pads the current data out to <i>&lt;length&gt;</i> bytes, using the fill byte.
    Note that the padding only applies to the current string, so if you do <span class='fixed'>blob_create("`p:8", "`p:8")</span>, each string will be padded to 8 bytes and concatenated, resulting in a 16-byte blob.
   </td>
  </tr>
  <tr>
   <td style='text-align:left; font-family:monospace; font-size:80%'>`r:<i>&lt;count&gt;</i></td>
   <td>
    Repeats the previous entry <i>&lt;count&gt;</i> times.
   </td>
  </tr>
  <tr>
   <td style='text-align:left; font-family:monospace; font-size:80%'>`i:<i>&lt;bytes&gt;</i></td>
   <td>
    Appends <i>&lt;bytes&gt;</i> of &ldquo;ignore&rdquo; data (i.e., fills both the data and mask with 0).
   </td>
  </tr>
 </table>

 <p>
 Some examples:
 </p>
 <p class='example'>
 // creates a 5-byte blob containing the ASCII representation of the string 'Hello'
 blob1 = blob_create("Hello");

 // creates an 8-byte blob containing the hex values: 12 34 56 78 9A BC DE F0
 blob2 = blob_create("`h:12,34,0x56 78 9A bc DEF0");

 // creates a 13-byte blob that contains blob1's contents followed by blob2's contents
 blob3 = blob_create(blob1, blob2);

 // creates a 16-byte blob: 12 34 78 56 CD AB 09 00 9A 78 56 34 12 00 00 00
 // note that multi-byte values are stored little-endian
 blob4 = blob_create(0x12, 0x34, 0x5678, 0x9ABCD, 0x123456789A);

 // alternate version of blob4 using an extended string
 blob4 = blob_create("`b:0x12,0x34`w:0x5678`d:0x9ABCD`q:0x123456789A");

 // creates an 11-byte blob with data: CD 21 00 00 00 00 00 00 00 CD 21
 //                          and mask: FF FF 00 00 00 00 00 00 00 FF FF
 blob5 = blob_create(0xcd, 0x21, -7, 0xcd, 0x21);

 // alternate version of blob5 using an extended string
 blob5a = blob_create("`b:0xcd,0x21`i:7`b:0xcd,0x21");
 </p>

<!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='blob_delete'></a>
 <h5>blob_delete</h5>
 <p class='funcdesc'>blob_delete(<i>blob</i>, <i>offset</i>[, <i>size</i>])</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>blob</i></b> &mdash; blob object to modify</li>
 <li><b><i>offset</i></b> &mdash; integer offset within the blob to begin deleting</li>
 <li><b><i>size</i></b> (optional) &mdash; number of bytes to delete</li>
 </ul>

 <p>
 Deletes <b><i>size</i></b> bytes from a blob starting at offset <b><i>offset</i></b>.
 </p>
 <p>
 If <b><i>offset</i></b> is negative, <b><i>size</i></b> bytes are removed from the end of the blob.
 </p>
 <p>
 If <b><i>size</i></b> is not specified, the blob is truncated to <b><i>offset</i></b>.
 </p>
 <p>
 Returns the resulting blob, which is the modified original (not a copy).
 </p>

<!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='blob_extract'></a>
 <h5>blob_extract</h5>
 <p class='funcdesc'>blob_extract(<i>blob</i>, <i>offset</i>[, <i>size</i>])</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>blob</i></b> &mdash; blob object to extract from</li>
 <li><b><i>offset</i></b> &mdash; integer offset within the blob to extract</li>
 <li><b><i>size</i></b> (optional) &mdash; number of bytes to extract</li>
 </ul>

 <p>
 Returns a new blob that is a subset of an existing <b><i>blob</i></b>, starting at the given <b><i>offset</i></b> and being of <b><i>size</i></b> bytes.
 </p>
 <p>
 If <b><i>offset</i></b> is negative, then the final <b><i>size</i></b> bytes are extracted from the blob.
 </p>
 <p>
 If <b><i>size</i></b> is not specified, this function extracts everything from <b><i>offset</i></b> until the end of the blob.
 </p>

<!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='blob_fill'></a>
 <h5>blob_fill</h5>
 <p class='funcdesc'>blob_fill(<i>blob</i>, <i>offset</i>, <i>repeats</i>, <i>data</i>[, <i>data</i>...])</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>blob</i></b> &mdash; blob object to modify</li>
 <li><b><i>offset</i></b> &mdash; integer offset within the blob to write to</li>
 <li><b><i>repeats</i></b> &mdash; integer number of repetitions of the data to write</li>
 <li><b><i>data</i></b> &mdash; one or more data items to write into the blob</li>
 </ul>

 <p>
 Writes <b><i>repeats</i></b> copies of <b><i>data</i></b> into an existing <b><i>blob</i></b> at the provided <b><i>offset</i></b>.
 </p>
 <p>
 If <b><i>offset</i></b> is negative, <b><i>data</i></b> is written starting at the end of the blob.
 </p>
 <p>
 The size of blob is expanded to fit if necessary.
 </p>
 <p>
 Note that by setting <b><i>repeats</i></b> to 1 this function can be used to write data into a blob at any position.
 </p>
 <p>
 See <a href='#blob_create'>blob_create</a> for a description of how <b><i>data</i></b> is interpreted.
 </p>
 <p>
 Returns the resulting blob, which is the modified original (not a copy).
 </p>

<!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='blob_insert'></a>
 <h5>blob_insert</h5>
 <p class='funcdesc'>blob_insert(<i>blob</i>, <i>offset</i>, <i>data</i>[, <i>data</i>...])</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>blob</i></b> &mdash; blob object to modify</li>
 <li><b><i>offset</i></b> &mdash; integer offset within the blob to insert at</li>
 <li><b><i>data</i></b> &mdash; one or more data items to write into the blob</li>
 </ul>

 <p>
 Inserts <b><i>data</i></b> into an existing <b><i>blob</i></b> at the provided <b><i>offset</i></b>.
 </p>
 <p>
 Existing data after <b><i>offset</i></b> is shifted to after the newly-inserted <b><i>data</i></b>.
 </p>
 <p>
 If <b><i>offset</i></b> is negative, <b><i>data</i></b> is appended starting at the end of the blob.
 </p>
 <p>
 See <a href='#blob_create'>blob_create</a> for a description of how <b><i>data</i></b> is interpreted.
 </p>
 <p>
 Returns the resulting blob, which is the modified original (not a copy).
 </p>

<!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='blob_mask_fill'></a>
 <h5>blob_mask_fill</h5>
 <p class='funcdesc'>blob_mask_fill(<i>blob</i>, <i>offset</i>, <i>repeats</i>, <i>data</i>[, <i>data</i>...])</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>blob</i></b> &mdash; blob object to modify</li>
 <li><b><i>offset</i></b> &mdash; integer offset within the blob to write to</li>
 <li><b><i>repeats</i></b> &mdash; integer number of repetitions of the data to write</li>
 <li><b><i>data</i></b> &mdash; one or more data items to write into the blob</li>
 </ul>

 <p>
 Writes <b><i>repeats</i></b> copies of <b><i>data</i></b> into an existing <b><i>blob</i></b>’s mask at the provided <b><i>offset</i></b>.
 </p>
 <p>
 If <b><i>offset</i></b> is negative, <b><i>data</i></b> is written starting at the end of the blob.
 </p>
 <p>
 The size of blob is expanded to fit if necessary.
 </p>
 <p>
 Note that by setting <b><i>repeats</i></b> to 1 this function can be used to write data into a blob’s mask at any position.
 </p>
 <p>
 See <a href='#blob_create'>blob_create</a> for a description of how <b><i>data</i></b> is interpreted.
 </p>
 <p>
 Returns the resulting blob, which is the modified original (not a copy).
 </p>

<!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='blob_mask_value'></a>
 <h5>blob_mask_value</h5>
 <p class='funcdesc'>blob_mask_value(<i>blob</i>, <i>offset</i>[, <i>size</i>])</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>blob</i></b> &mdash; blob object to read from</li>
 <li><b><i>offset</i></b> &mdash; integer offset within the blob to read from</li>
 <li><b><i>size</i></b> (optional) &mdash; integer size of the data to return</li>
 </ul>

 <p>
 Returns the integer value stored in <b><i>blob</i></b>’s mask at the given <b><i>offset</i></b> as an integer of the specified <i>size</i>.
 </p>
 <p>
 If <b><i>offset</i></b> is negative, then the final <b><i>size</i></b> bytes will be returned.
 </p>
 <p>
 If <b><i>size</i></b> is not specified, this function returns a single byte.
 </p>

<!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='blob_value'></a>
 <h5>blob_value</h5>
 <p class='funcdesc'>blob_value(<i>blob</i>, <i>offset</i>[, <i>size</i>])</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>blob</i></b> &mdash; blob object to read from</li>
 <li><b><i>offset</i></b> &mdash; integer offset within the blob to read from</li>
 <li><b><i>size</i></b> (optional) &mdash; integer size of the data to return</li>
 </ul>

 <p>
 Returns the integer value stored in <b><i>blob</i></b> at the given <b><i>offset</i></b> as an integer of the specified <i>size</i>.
 </p>
 <p>
 If <b><i>offset</i></b> is negative, then the final <b><i>size</i></b> bytes will be returned.
 </p>
 <p>
 If <b><i>size</i></b> is not specified, this function returns a single byte.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>

 <a name='funcs-ingame'></a>
 <a name='funcs-notify'></a>
 <h3>Notification functions</h3>

 <p>
 These functions are used in conjunction with <a href='#notify'>notifications</a>.
 They are only available to in-game environments.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='hook'></a>
 <h5>hook</h5>
 <p class='funcdesc'>hook(<i>notification</i>, <i>function</i>[, <i>parameter</i>])</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>notification</i></b> &mdash; string containing the name of the notification to register</li>
 <li><b><i>function</i></b> &mdash; string specifying the name of the function to call</li>
 <li><b><i>parameter</i></b> (optional) &mdash; parameter to match against (may be string or integer depending on notification)</li>
 </ul>

 <p>
 Registers the given <b><i>function</i></b> to be called whenever the specified <b><i>notification</i></b> is received.
 </p>

 <p>
 If a <b><i>parameter</i></b> is provided, then the function will only be called if the notification parameter matches <b><i>parameter</i></b>.
 </p>

 <p>
 See the <a href='#notifications'>notifications</a> section for a list of available notifications and their parameters.
 </p>

 <p class='important'>
 <b>IMPORTANT:</b> Functions with names matching any of the standard <a href='#notifications'>notifications</a> are automatically registered as a hook for that notification.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='script_run_async'></a>
 <h5>script_run_async</h5>
 <p class='funcdesc'>script_run_async(<i>function</i>[, <i>parameter</i>...])</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>function</i></b> &mdash; string specifying the name of the function to call</li>
 <li><b><i>parameter</i></b> (optional) &mdash; one or more parameters to pass to the function</li>
 </ul>

 <p>
 Calls the given function, allowing it to execute asynchronous calls.
 </p>

 <p>
 <b><i>function</i></b> is passed to this function as a string, and may take any number of parameters.
 </p>

 <p>
 The <b><i>parameter</i></b>s passed here are forwarded on to the function.
 </p>

 <p>
 Execution of the function proceeds synchronously until a blocking function is executed, at which point control returns to the caller.
 Thus, if <b><i>function</i></b> never makes an asynchronous function call, it will execute synchronously.
 </p>

 <p>
 This function returns a unique identifier that can be used by the <a href='#script_running'>script_running</a> function to see if the function has completed.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='script_running'></a>
 <h5>script_running</h5>
 <p class='funcdesc'>script_running(<i>id</i>)</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>id</i></b> &mdash; integer ID of the asynchronous script</li>
 </ul>

 <p>
 Returns true if the given script has not yet completed.
 </p>

 <p>
 The <b><i>id</i></b> passed here must be a value returned from a previous call to <a href='#script_run_async'>script_run_async</a>.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='sleep'></a>
 <h5>sleep [blocking]</h5>
 <p class='funcdesc'>sleep(<i>msec</i>)</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>msec</i></b> &mdash; integer duration of the sleep, in milliseconds</li>
 </ul>

 <p>
 Suspends execution for <b><i>msec</i></b> milliseconds.
 </p>

 <p>
 This is a blocking function; it can only be executed from within an asynchronous script.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='wait'></a>
 <h5>wait [blocking]</h5>
 <p class='funcdesc'>wait(<i>notification</i>[, <i>parameter</i>])</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>notification</i></b> &mdash; string containing the name of the notification to register</li>
 <li><b><i>parameter</i></b> (optional) &mdash; parameter to match against (may be string or integer depending on notification)</li>
 </ul>

 <p>
 Suspends execution until the specified <b><i>notification</i></b> is received.
 </p>

 <p>
 If a <b><i>parameter</i></b> is provided, execution will only resume if the notification parameter matches <b><i>parameter</i></b>.
 </p>

 <p>
 See the <a href='#notifications'>notifications</a> section for a list of available notifications and their parameters.
 </p>

 <p>
 This is a blocking function; it can only be executed from within an asynchronous script.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>

 <a name='funcs-system'></a>
 <h3>System functions</h3>

 <p>
 These functions are used to control system-wide behaviors.
 They are only available to in-game environments.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='system_throttle'></a>
 <h5>system_throttle</h5>
 <p class='funcdesc'>system_throttle([<i>enable</i>])</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>enable</i></b> (optional) &mdash; turns system-wide time throttling on if true, or off if false; if this parameter is omitted, the setting remains unchanged</li>
 </ul>

 <p>
 Controls system-wide throttling behavior.
 Normally, DREAMM runs such that time in the emulated system ticks by at the same rate as real time.
 </p>

 <p>
 However, a script may want to temporary &ldquo;fast forward&rdquo; emulation to get past something slow or tedious.
 To do that, you can call this function with <b><i>enable</i></b> set to false to tell DREAMM to run as quickly as possible, and then set it back to true when complete.
 </p>

 <p>
 The return value of this function is the previous throttle setting.
 If you call it with no parameters, the current throttle setting remains unchanged, just providing the return value.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>

 <a name='funcs-overlay'></a>
 <h3>Overlay functions</h3>

 <p>
 These functions allow scripts to show messages in overlays.
 They are only available to in-game environments.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='overlay_hide'></a>
 <h5>overlay_hide</h5>
 <p class='funcdesc'>overlay_hide(<i>id</i>)</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>id</i></b> &mdash; string containing the ID of the overlay to hide</li>
 </ul>

 <p>
 Hides the previously-shown overlay with ID <b><i>id</i></b>.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='overlay_show'></a>
 <h5>overlay_show</h5>
 <p class='funcdesc'>overlay_show(<i>id</i>, <i>position</i>, <i>message</i>[, <i>duration</i>])</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>id</i></b> &mdash; string containing the ID of the overlay</li>
 <li><b><i>position</i></b> &mdash; integer specifying either -1 or 1 as the overlay position</li>
 <li><b><i>message</i></b> &mdash; string containing message to display</li>
 <li><b><i>duration</i></b> (optional) &mdash; integer specifying how long to show the overlay, in seconds</li>
 </ul>

 <p>
 Displays or updates an overlay.
 </p>

 <p>
 The <b><i>id</i></b> parameter provides an ID that is used internally to represent this overlay.
 Calling <a href='#overlay_show'>overlay_show</a> with the same ID replaces the existing overlay.
 The same ID can be passed to <a href='#overlay_hide'>overlay_hide</a> to hide the overlay.
 </p>

 <p>
 The <b><i>position</i></b> parameter specifies whether the overlay should appear at the top (-1) or bottom (1) of the screen.
 </p>

 <p>
 The <b><i>text</i></b> parameter specifies the text to display.
 </p>

 <p>
 Custom images may be embedded within the text by using the escape sequence <span class='fixed'>!!<i>filename</i>[@<i>scale</i>]!!</span>,
 where <i>filename</i> is the name of a PNG file located in the DREAMMPack’s customs directory,
 and <i>scale</i> is an optional scale factor, with 100 being normal scale.
 </p>

 <p>
 If a <b><i>duration</i></b> parameter is provided, it auto-dismisses the overlay after the specified number of seconds;
 otherwise, the overlay remains onscreen until explicitly hidden.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>

 <a name='funcs-keyboard'></a>
 <h3>Keyboard functions</h3>

 <p>
 These functions allow scripts to block and/or simulate keypresses.
 They are only available to in-game environments.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='keyboard_install_hook'></a>
 <h5>keyboard_install_hook</h5>
 <p class='funcdesc'>keyboard_install_hook(<i>scancode</i>, <i>function</i>)</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>scancode</i></b> &mdash; integer specifying the scancode to respond to</li>
 <li><b><i>function</i></b> &mdash; string containing the name of the function to call when the key is pressed</li>
 </ul>

 <p>
 Registers a callback <b><i>function</i></b> to be called whenever the key matching <b><i>scancode</i></b> is pressed.
 </p>

 <p>
 Keys registered in this way are blocked from passing through to the emulated machine, so they can be used to add cheats or other helpers.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='keyboard_lockout'></a>
 <h5>keyboard_lockout</h5>
 <p class='funcdesc'>keyboard_lockout(<i>enable</i>)</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>enable</i></b> &mdash; Boolean value indicating the state of the lockout</li>
 </ul>

 <p>
 This function enables or disables a system-wide keyboard lockout.
 When enabled, user-intiated keyboard events are ignored.
 </p>

 <p>
 This can be used in scripts that take over input, in order to prevent the user from messing up typing behaviors.
 </p>

 <p>
 See also: <a href='#input_lockout'>input_lockout</a>.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='keyboard_type'></a>
 <h5>keyboard_type [blocking]</h5>
 <p class='funcdesc'>keyboard_type(<i>text</i>[, <i>interkey</i>[, <i>makebreak</i>]])</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>text</i></b> &mdash; string containing the text to type</li>
 <li><b><i>interkey</i></b> &mdash; integer containing the inter-key delay, in milliseconds</li>
 <li><b><i>makebreak</i></b> &mdash; integer containing the make-break delay, in milliseconds</li>
 </ul>

 <p>
 Types the specified <b><i>text</i></b> on the keyboard.
 </p>

 <p>
 The <b><i>text</i></b> string can be a normal ASCII string, including mixed case and symbols.
 DREAMM presumes the Shift and CapsLock states are both clear when typing.
 </p>

 <p>
 For more detailed control, direct scancodes can be embedded within the string using the <span class='fixed'>`<i>XX</i></span> escape sequence, where <i>XX</i> is a two-character hexadecimal scancode number.
 To type a regular <span class='fixed'>`</span> character, include a double <span class='fixed'>``</span> sequence.
 </p>

 <p>
 If <b><i>interkey</i></b> is provided, then it specifies the delay between successive keypresses, in milliseconds.
 The default delay is 250ms.
 </p>

 <p>
 If <b><i>makebreak</i></b> is provided, then it specifies the delay between the key down and key up signals, in milliseconds.
 By default, both are sent immediately, with no delay between.
 </p>

 <p>
 This is a blocking function; it can only be executed from within an asynchronous script.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>

 <a name='funcs-dosmouse'></a>
 <h3>DOS mouse functions</h3>

 <p>
 These functions allow scripts to block and/or simulate mouse behaviors in emulated DOS machines.
 They are only available to in-game environments.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='mouse_buttons'></a>
 <h5>mouse_buttons</h5>
 <p class='funcdesc'>mouse_buttons()</p>

 <p>
 Returns the current state of the mouse buttons as a bitmask.
 Mouse button 0 (left) is bit 0, mouse button 1 (right) is bit 1, etc.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='mouse_click'></a>
 <h5>mouse_click [blocking]</h5>
 <p class='funcdesc'>mouse_click(<i>button</i>[, <i>duration</i>])</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>button</i></b> &mdash; integer specifying the button index</li>
 <li><b><i>duration</i></b> &mdash; integer specifying the length of the click, in milliseconds</li>
 </ul>

 <p>
 Simulates a mouse button down/up sequence.
 </p>

 <p>
 <b><i>button</i></b> specifies the button index, with 0 meaning the left button, 1 the right, etc.
 </p>

 <p>
 If provided, <b><i>duration</i></b> controls how long the button is left in the down state, in milliseconds.
 The default duration is 250ms.
 </p>

 <p>
 This is a blocking function; it can only be executed from within an asynchronous script.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='mouse_force_relative_zero'></a>
 <h5>mouse_force_relative_zero</h5>
 <p class='funcdesc'>mouse_force_relative_zero(<i>enable</i>)</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>enable</i></b> &mdash; Boolean value indicating whether to force relative motion to 0</li>
 </ul>

 <p>
 Enables or disables the &ldquo;forced relative zero&rdquo; mode.
 When this mode is enabled, all attempts by the DOS mouse driver to read relative motion return zero.
 </p>

 <p>
 This can be used by scripts that take over mouse handling for games, ensuring that the games always see zero motion, under the assumption that the real coordinates are updated elsewhere in the script.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='mouse_lockout'></a>
 <h5>mouse_lockout</h5>
 <p class='funcdesc'>mouse_lockout(<i>enable</i>)</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>enable</i></b> &mdash; Boolean value indicating the state of the lockout</li>
 </ul>

 <p>
 This function enables or disables a system-wide mouse lockout.
 When enabled, user-intiated mouse movement events are ignored.
 </p>

 <p>
 This can be used in scripts that take over input, in order to prevent the user from messing up mousing behaviors.
 </p>

 <p>
 See also: <a href='#input_lockout'>input_lockout</a>.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='mouse_move_abs'></a>
 <h5>mouse_move_abs [blocking]</h5>
 <p class='funcdesc'>mouse_move_abs(<i>xcoord</i>, <i>ycoord</i>[, <i>pixpersec</i>])</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>xcoord</i></b> &mdash; integer value indicating the target mouse X coordinate</li>
 <li><b><i>ycoord</i></b> &mdash; integer value indicating the target mouse Y coordinate</li>
 <li><b><i>pixpersec</i></b> &mdash; integer value indicating movement speed, in pxiels/second</li>
 </ul>

 <p>
 Moves the mouse from its current position to the screen position (<b><i>xcoord</i></b>, <b><i>ycoord</i></b>).
 </p>

 <p class='important'>
 <b>Note:</b> mouse coordinates are provided in native DOS mouse driver coordinates.
 Typically, this is a 640&times;200 grid regardless of the screen resolution, unless otherwise changed by the game.
 </p>

 <p>
 If provided, <b><i>pixpersec</i></b> controls the speed of movement, in pixels/second.
 The default speed is 320 pixels/second.
 </p>

 <p>
 This can be used in scripts to position the mouse to a particular spot, for games that query the absolute position of the mouse from the mouse driver.
 </p>

 <p>
 This is a blocking function; it can only be executed from within an asynchronous script.
 </p>

 <p>
 See also: <a href='#mouse_move_rel'>mouse_move_rel</a>.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='mouse_move_rel'></a>
 <h5>mouse_move_rel</h5>
 <p class='funcdesc'>mouse_move_rel(<i>xmickeys</i>, <i>ymickeys</i>)</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>xmickeys</i></b> &mdash; integer value indicating mouse movement in the X direction</li>
 <li><b><i>ymickeys</i></b> &mdash; integer value indicating mouse movement in the Y direction</li>
 </ul>

 <p>
 Supplies a one-shot delta movement amount of (<b><i>xmickeys</i></b>, <b><i>ymickeys</i></b>) that will be applied to the next relative mouse read.
 </p>

 <p>
 This can be used in scripts to affect mouse movement for games that query relative mouse movements.
 </p>

 <p>
 See also: <a href='#mouse_move_abs'>mouse_move_abs</a>.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='mouse_pos_abs'></a>
 <h5>mouse_pos_abs</h5>
 <p class='funcdesc'>mouse_pos_abs()</p>

 <p>
 Returns the current absolute mouse position, as a screen position.
 This value is returned as a 2-entry tuple, with the X coordinate in the first entry and the Y coordinate in the second.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>

 <a name='funcs-input'></a>
 <h3>Misc input functions</h3>

 <p>
 These functions allow scripts to lock out certain types of inputs.
 They are only available to in-game environments.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='gameport_lockout'></a>
 <h5>gameport_lockout</h5>
 <p class='funcdesc'>gameport_lockout(<i>enable</i>)</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>enable</i></b> &mdash; Boolean value indicating the state of the lockout</li>
 </ul>

 <p>
 This function enables or disables a system-wide gameport (joystick) lockout.
 When enabled, user-intiated joystick movement events are ignored.
 </p>

 <p>
 This can be used in scripts that take over input, in order to prevent the user from messing up input behaviors.
 </p>

 <p>
 See also: <a href='#input_lockout'>input_lockout</a>.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='input_lockout'></a>
 <h5>input_lockout</h5>
 <p class='funcdesc'>input_lockout(<i>enable</i>)</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>enable</i></b> &mdash; Boolean value indicating the state of the lockout</li>
 </ul>

 <p>
 This function enables or disables a system-wide input lockout, affecting keyboard, mouse, and gameport.
 When enabled, user-intiated input events are ignored.
 </p>

 <p>
 This can be used in scripts that take over input, in order to prevent the user from messing up input behaviors.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>

 <a name='funcs-scraper'></a>
 <h3>Screen scraping functions</h3>

 <p>
 These functions query the state of installed scaper objects.
 They are only available to in-game environments.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='scraper_fingerprint'></a>
 <h5>scraper_fingerprint</h5>
 <p class='funcdesc'>scraper_fingerprint(<i>region</i>[, <i>flags</i>])</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>region</i></b> &mdash; string containing an encoded region definition</li>
 <li><b><i>flags</i></b> (optional) &mdash; integer containing zero or more flag values</li>
 </ul>

 <p>
 This function creates a fingerprint from the pixels in the given <b><i>region</i></b>, optionally after applying the specified <b><i>flags</i></b>.
 </p>

 <p>
 The currently supported values for <b><i>flags</i></b> are:
 </p>
 <ul>
 <li><b>0</b> (default) &mdash; no transformations</li>
 <li><b>1</b> (or <b>MF_COLLAPSE16</b>) &mdash; reduces 256-color pixel values to 16 colors by ORing the top nibble with the bottom nibble; useful for some LucasArts games that play fast and loose with this</li>
 <li><b>2</b> (or <b>MF_MOUSE</b>) &mdash; loosens match requirements so that only 5/9 tiles must match; this is to account for a user-controlled mouse cursor that might overlap one or more tiles</li>
 </ul>

 <p>
 The <b><i>region</i></b> parameter is a string describing the region.
 It can be generated from within the DREAMM debugger via the <span class='fixed'>scrape</span> command, or via the <a href='#scraper_region'>scraper_region</a> function.
 </p>

 <p>
 The return value is a string fingerprint that can be used in the <a href='#scraper_match'>scraper_match</a> function.
 Often this function is used within scripts to output unexpected matches.
 </p>

 <p>
 Note that for this function (and <a href='#scraper_match'>scraper_match</a>) overlapping rectangles are treated as exclusive OR, meaning that only pixels that are contained within an odd number of rectangles are considered.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='scraper_match'></a>
 <h5>scraper_match</h5>
 <p class='funcdesc'>scraper_match(<i>region</i>, <i>fingerprints</i>[, <i>flags</i>])</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>region</i></b> &mdash; string containing an encoded region definition</li>
 <li><b><i>fingerprints</i></b> &mdash; string containing one or more fingerprint=val strings, separated by commas</li>
 <li><b><i>flags</i></b> (optional) &mdash; integer containing zero or more flag values</li>
 </ul>

 <p>
 This function attempts to match pixels in the given <b><i>region</i></b> against the provided <b><i>fingerprints</i></b>, optionally after applying the specified <b><i>flags</i></b>.
 </p>

 <p>
 The <b><i>region</i></b> parameter is a string describing the region.
 It can be generated from within the DREAMM debugger via the <span class='fixed'>scrape</span> command, or via the <a href='#scraper_region'>scraper_region</a> function.
 </p>

 <p>
 Supported values for <b><i>flags</i></b> are described in the <a href='#scraper_fingerprint'>scraper_fingerprint</a> function.
 </p>

 <p>
 The <b><i>fingerprints</i></b> parameter is a string in the form of a comma-separated list of <span class='fixed'>&lt;fingerprint&gt;=&lt;id&gt;</span>.
 If the specified fingerprint is the best match for the given <b><i>region</i></b>, then the return from this function will be the associated id.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='scraper_read'></a>
 <h5>scraper_read</h5>
 <p class='funcdesc'>scraper_read(<i>region</i>[, <i>flags</i>])</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>region</i></b> &mdash; string containing an encoded region definition</li>
 <li><b><i>flags</i></b> (optional) &mdash; integer containing zero or more flag values</li>
 </ul>

 <p>
 This function attempts to reads the raw pixel values from the given <b><i>region</i></b> and returns them as a <a href='#blob'>blob</a>, optionally after applying the specified <b><i>flags</i></b>.
 </p>

 <p>
 The <b><i>region</i></b> parameter is a string describing the region.
 It can be generated from within the DREAMM debugger via the <span class='fixed'>scrape</span> command, or via the <a href='#scraper_region'>scraper_region</a> function.
 </p>

 <p>
 The currently supported values for <b><i>flags</i></b> are:
 </p>
 <ul>
 <li><b>0</b> (default) &mdash; no transformations; in video modes that are 256-color or less, this function returns raw pixel values, while deeper color modes return RGB values in R-G-B order</li>
 <li><b>1</b> (or <b>RF_FORCERGB</b>) &mdash; forces all video modes to return pixel values in R-G-B order</li>
 </ul>

 <p>
 The order of pixels is left-to-right, top-to-bottom within each rectangle.
 If multiple rectangles are described by <b><i>region</i></b> then the pixels for each rectangle are concatenated together into a single blob.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='scraper_region'></a>
 <h5>scraper_region</h5>
 <p class='funcdesc'>scraper_region([<i>region</i>, ] <i>rectangle</i>[, ...])</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>region</i></b> (optional) &mdash; string containing a region to append to</li>
 <li><b><i>rectangle</i></b> &mdash; one or more tuples containing rectangle coordinates</li>
 </ul>

 <p>
 This function defines a new region or appends to an existing region, based on coordinates in the current video mode.
 It returns a string representing the new region, which can be used with other scraper functions.
 </p>

 <p>
 If <b><i>region</i></b> is specified, then new rectangles are appended to that region; otherwise, a fresh empty region is used as the starting point.
 </p>

 <p>
 The bounds for each rectangle are specified as a tuple in the order: [ left, top, right, bottom ].
 Multiple rectangles can be specified in one command.
 Note that the behavior of multiple overlapping rectangles can vary depending on the function.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='scraper_rects'></a>
 <h5>scraper_rects</h5>
 <p class='funcdesc'>scraper_rects(<i>region</i>)</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>region</i></b> &mdash; string containing a region to expand</li>
 </ul>

 <p>
 This function returns a tuple of rectangles described by <b><i>region</i></b>, scaled to the current video mode.
 Each rectangle is itself a tuple of coordinates in the order [ left, top, right, bottom ].
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='scraper_string'></a>
 <h5>scraper_string</h5>
 <p class='funcdesc'>scraper_string(<i>region</i>, <i>charset</i>, <i>color</i>[, <i>flags</i>])</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>region</i></b> &mdash; string containing a region to examine</li>
 <li><b><i>charset</i></b> &mdash; string containing the name of the character set</li>
 <li><b><i>color</i></b> &mdash; integer or tuple containing the colors to consider</li>
 <li><b><i>flags</i></b> (optional) &mdash; integer containing zero or more flag values</li>
 </ul>

 <p>
 This function attempts to identify a string in the given <b><i>region</i></b> using the provided <b><i>charset</i></b>.
 Only pixels with the specified <b><i>color</i></b> are considered.
 The optional <b><i>flags</i></b> field lets you control the behavior:
 </p>
 <ul>
 <li><b>0</b> (default) &mdash; default behavior</li>
 <li><b>1</b> (or <b>SF_HALFWIDTH</b>) &mdash; for 4-bit and lower video modes, combines two neighboring pixels into a single pixel, with the first pixel value in the upper bits and the second pixel value in the lower bits; this is useful for early games that ran in high-resolution with dither patterns as it combines pairs of pixels together for identification purposes</li>
 <li><b>2</b> (or <b>SF_BGCOLOR</b>) &mdash; treat color matches as inverted, i.e., match pixels that <i>don’t</i> match any of the colors; this allows you to specify the background color if it’s easier
 <li><b>4</b> (or <b>SF_PLACEHOLDERS</b>) &mdash; include placeholder (ASCII 1) characters in the string for unrecognized characters; by default, these are just ignored
 </ul>

 <p>
 The <b><i>region</i></b> parameter is a string describing the region.
 It can be generated from within the DREAMM debugger via the <span class='fixed'>csid</span> command.
 </p>

 <p>
 The <b><i>charset</i></b> parameter must specify a valid charset as defined in the game’s .dreamm file, or one of the built-in charsets: <b>8x8</b>, <b>8x14</b>, <b>9x14</b>, <b>8x16</b>, <b>9x16</b>, or <b>system</b>.
 The <b>system</b> charset is special in that it selects one of the other built-in charsets based on the current video mode (useful for text mode scraping).
 </p>

 <p class='important'>
 <b>Note:</b> Built-in fonts can be subsetted by specifying the name followed by the list of character indices to include in brackets <span class='fixed'>[]</span> afterwards.
 Both ranges and individual characters are supported, and all numbers are specified as hexadecimal numbers.
 For example, <span class='fixed'>8x8[30-3f+40-5f+61+63]</span> specifies to include characters in the 0x30-0x3F range, plus the characters in the 0x40-0x5F range, plus the characters 0x61 and 0x63.
 </p>

 <p>
 The <b><i>color</i></b> parameter specifies the color(s) that are included.
 For video modes above 8-bit, there can be only a single color, described by the low 24 bits in R-G-B order.
 For other video modes, up to 8 colors can be included for consideration, specified as a tuple.
 For example, to specify both colors 2 and 4, provide a <b><i>color</i></b> of [2, 4].
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>

 <a name='funcs-video'></a>
 <h3>Video functions</h3>

 <p>
 These functions query or control the state of the video output.
 They are only available to in-game environments.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='video_size'></a>
 <h5>video_size</h5>
 <p class='funcdesc'>video_size()</p>

 <p>
 Returns a tuple containing information about the current video mode.
 There are 5 entries in the tuple: [ pixel_width, pixel_height, bits_per_pixel, text_columns, text_rows ].
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='video_suppress'></a>
 <h5>video_suppress</h5>
 <p class='funcdesc'>video_suppress(<i>enable</i>)</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>enable</i></b> &mdash; Boolean value indicating the new suppression state</li>
 </ul>

 <p>
 This function enables or disables a video output.
 For example, it can be used to hide short-lived screens while selections are made.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='video_text'></a>
 <h5>video_text</h5>
 <p class='funcdesc'>video_text()</p>

 <p>
 Returns true if the current video mode is a text mode.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>

 <a name='funcs-floppy'></a>
 <h3>Floppy functions</h3>

 <p>
 These functions allow for loading/saving raw floppy sectors.
 They are only available to in-game environments.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='floppy_track_format'></a>
 <h5>floppy_track_format</h5>
 <p class='funcdesc'>(1) floppy_track_format(<i>drive</i>, <i>tracknum</i>, <i>headnum</i>, <i>sectors</i>)
(2) floppy_track_format(drive</i>, <i>tracknum</i>, <i>headnum</i>, <i>formatinfo</i>)</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>drive</i></b> &mdash; string path to drive root, e.g. "A:"</li>
 <li><b><i>tracknum</i></b> &mdash; integer track number</li>
 <li><b><i>headnum</i></b> &mdash; integer head (side) number</li>
 <li><b><i>sectors</i></b> (1) &mdash; integer count of the number of sectors to lay out on the track</li>
 <li><b><i>formatinfo</i></b> (2) &mdash; <a href='#blob'>blob</a> containing raw formatting info, in 4-byte chunks</li>
 </ul>

 <p>
 (1) Formats the track <b><i>tracknum</i></b> on side <b><i>headnum</i></b> of the floppy mounted in <b><i>drive</i></b> with the specified number of <b><i>sectors</i></b>.
 </p>
 <p>
 (2) Formats the track <b><i>tracknum</i></b> on side <b><i>headnum</i></b> of the floppy mounted in <b><i>drive</i></b>, using the provided raw <b><i>formatinfo</i></b>. The <b><i>formatinfo</i></b> is a blob describing the track layout and consists of a sequence of 4 bytes for each sector.
 These 4 bytes are the raw data passed to the floppy controller and are, in order: track, head, sector number, sector size (typically 2).
 </p>

 <p>
 These two examples are equivalent:
 </p>
 <p class='example'>
 // format track 4, side 1, with 8 sectors
 floppy_track_format("A:", 4, 1, 8);

 // same deal but passing low-level formatting info
 floppy_track_format("A:", 4, 1, blob_create(4, 1, 1, 2,
                                             4, 1, 2, 2,
                                             4, 1, 3, 2,
                                             4, 1, 4, 2,
                                             4, 1, 5, 2,
                                             4, 1, 6, 2,
                                             4, 1, 7, 2,
                                             4, 1, 8, 2));
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='floppy_sector_load'></a>
 <h5>floppy_sector_load</h5>
 <p class='funcdesc'>floppy_sector_load(<i>drive</i>, <i>tracknum</i>, <i>headnum</i>, <i>sectornum</i>)</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>drive</i></b> &mdash; string path to drive root, e.g. "A:"</li>
 <li><b><i>tracknum</i></b> &mdash; integer track number</li>
 <li><b><i>headnum</i></b> &mdash; integer head (side) number</li>
 <li><b><i>sectornum</i></b> &mdash; integer sector number</li>
 </ul>

 <p>
 Fetches the raw floppy track specified by the <b><i>drive</i></b>/<b><i>tracknum</i></b>/<b><i>headnum</i></b> combination and returns sector <b><i>sectornum</i></b> from it.
 </p>
 <p>
 Data is returned as a <a href='#blob'>blob</a>.
 If unable to load the sector, an empty blob will be returned.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='floppy_sector_save'></a>
 <h5>floppy_sector_save</h5>
 <p class='funcdesc'>floppy_sector_save(<i>drive</i>, <i>tracknum</i>, <i>headnum</i>, <i>sectornum</i>, <i>blob</i>)</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>drive</i></b> &mdash; string path to drive root, e.g. "A:"</li>
 <li><b><i>tracknum</i></b> &mdash; integer track number</li>
 <li><b><i>headnum</i></b> &mdash; integer head (side) number</li>
 <li><b><i>sectornum</i></b> &mdash; integer sector number</li>
 <li><b><i>blob</i></b> &mdash; a <a href='#blob'>blob</a> containing the data to write</li>
 </ul>

 <p>
 Writes the given <b><i>blob</i></b> to the <b><i>sectornum</i></b> on the floppy track described by the <b><i>drive</i></b>/<b><i>tracknum</i></b>/<b><i>headnum</i></b> combination.
 </p>
 <p>
 Returns true if successful; false otherwise.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>

 <a name='funcs-cpu'></a>
 <h3>CPU functions</h3>

 <p>
 These functions allow for querying or setting the current CPU state.
 They are only available to in-game environments.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='cpu_copymem'></a>
 <h5>cpu_copymem</h5>
 <p class='funcdesc'>cpu_copymem(<i>destaddr</i>, <i>srcaddr</i>, <i>size</i>)</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>destaddr</i></b> &mdash; integer destination address for the copy</li>
 <li><b><i>srcaddr</i></b> &mdash; integer source address for the copy</li>
 <li><b><i>size</i></b> &mdash; integer count of bytes to copy</li>
 </ul>

 <p>
 Copies <b><i>size</i></b> bytes from CPU memory at virtual address <b><i>srcaddr</i></b> to virtual address <b><i>dstaddr</i></b>.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='cpu_crcmem'></a>
 <h5>cpu_crcmem</h5>
 <p class='funcdesc'>cpu_crcmem(<i>address</i>, <i>size</i>)</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>address</i></b> &mdash; integer starting address of region to CRC</li>
 <li><b><i>size</i></b> &mdash; integer count of bytes to CRC</li>
 </ul>

 <p>
 Computes and returns the CRC-32 of <b><i>size</i></b> bytes from CPU memory at virtual address <b><i>address</i></b>.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='cpu_cs'></a>
 <a name='cpu_ds'></a>
 <a name='cpu_es'></a>
 <a name='cpu_fs'></a>
 <a name='cpu_gs'></a>
 <a name='cpu_ss'></a>
 <h5>cpu_cs, cpu_ds, cpu_es, cpu_fs, cpu_gs, cpu_ss</h5>
 <p class='funcdesc'>cpu_cs()
cpu_ds()
cpu_es()
cpu_fs()
cpu_gs()
cpu_ss()</p>

 <p>
 Returns the current value of the given segment selector.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='cpu_csbase'></a>
 <a name='cpu_dsbase'></a>
 <a name='cpu_esbase'></a>
 <a name='cpu_fsbase'></a>
 <a name='cpu_gsbase'></a>
 <a name='cpu_ssbase'></a>
 <h5>cpu_csbase, cpu_dsbase, cpu_esbase, cpu_fsbase, cpu_gsbase, cpu_ssbase</h5>
 <p class='funcdesc'>cpu_csbase()
cpu_dsbase()
cpu_esbase()
cpu_fsbase()
cpu_gsbase()
cpu_ssbase()</p>

 <p>
 Returns the current base of the given segment.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='cpu_eax'></a>
 <a name='cpu_ebx'></a>
 <a name='cpu_ecx'></a>
 <a name='cpu_edx'></a>
 <a name='cpu_esi'></a>
 <a name='cpu_edi'></a>
 <a name='cpu_ebp'></a>
 <a name='cpu_esp'></a>
 <h5>cpu_eax, cpu_ebx, cpu_ecx, cpu_edx, cpu_esi, cpu_edi, cpu_ebp, cpu_esp</h5>
 <p class='funcdesc'>cpu_eax()
cpu_ebx()
cpu_ecx()
cpu_edx()
cpu_esi()
cpu_edi()
cpu_ebp()
cpu_esp()</p>

 <p>
 Returns the current value of the general purpose register specified.
 To access 8-bit or 16-bit portions of registers, simply mask the results.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='cpu_findmem'></a>
 <h5>cpu_findmem</h5>
 <p class='funcdesc'>cpu_findmem(<i>address</i>, <i>size</i>, <i>data</i>[, <i>data</i>...])</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>address</i></b> &mdash; integer starting address of region to search</li>
 <li><b><i>size</i></b> &mdash; integer count of bytes to search</li>
 <li><b><i>data</i></b> &mdash; one or more data items to serach for</li>
 </ul>

 <p>
 Searches <b><i>size</i></b> bytes of CPU memory starting at <b><i>address</i></b> for the pattern specified by <b><i>data</i></b>.
 </p>

 <p>
 The <b><i>data</i></b> to search for is specified in the same format as for <a href='#blob_create'>blob_create</a>.
 Masks are respected when searching, so wildcards can be used.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='cpu_install_hook'></a>
 <h5>cpu_install_hook</h5>
 <p class='funcdesc'>cpu_install_hook(<i>address</i>, <i>function</i>)</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>address</i></b> &mdash; integer address of code to break on</li>
 <li><b><i>function</i></b> &mdash; string contianing the name of the script function to call</li>
 </ul>

 <p>
 Installs a breakpoint at <b><i>address</i></b> that will call <b><i>function</i></b> when reached.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='cpu_protected'></a>
 <h5>cpu_protected</h5>
 <p class='funcdesc'>cpu_protected()</p>

 <p>
 Returns true if the CPU is currently running in protected mode.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='cpu_readmem'></a>
 <h5>cpu_readmem</h5>
 <p class='funcdesc'>cpu_readmem(<i>address</i>, <i>size</i>)</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>address</i></b> &mdash; integer address of memory to read from</li>
 <li><b><i>size</i></b> &mdash; integer number of bytes to read</li>
 </ul>

 <p>
 Reads <b><i>size</i></b> bytes from CPU memory at virtual address <b><i>address</i></b>.
 </p>

 <p>
 If <b><i>size</i></b> is 1, 2, 4, or 8, the value is returned as an integer; otherwise, it is returned as a <a href='#blob'>blob</a>.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='cpu_set_eax'></a>
 <a name='cpu_set_ebx'></a>
 <a name='cpu_set_ecx'></a>
 <a name='cpu_set_edx'></a>
 <a name='cpu_set_esi'></a>
 <a name='cpu_set_edi'></a>
 <a name='cpu_set_ebp'></a>
 <a name='cpu_set_esp'></a>
 <h5>cpu_set_eax, cpu_set_ebx, cpu_set_ecx, cpu_set_edx, cpu_set_esi, cpu_set_edi, cpu_set_ebp, cpu_set_esp</h5>
 <p class='funcdesc'>cpu_set_eax(<i>value</i>)
cpu_set_ebx(<i>value</i>)
cpu_set_ecx(<i>value</i>)
cpu_set_edx(<i>value</i>)
cpu_set_esi(<i>value</i>)
cpu_set_edi(<i>value</i>)
cpu_set_ebp(<i>value</i>)
cpu_set_esp(<i>value</i>)</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>value</i></b> &mdash; integer value to set the given register to</li>
 </ul>

 <p>
 Sets the value of the given general purpose register.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='cpu_writemem'></a>
 <h5>cpu_writemem</h5>
 <p class='funcdesc'>cpu_writemem(<i>address</i>, <i>size</i>, <i>data</i>)
cpu_writemem(<i>address</i>, <i>blob</i>)</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>address</i></b> &mdash; integer address of memory to write to</li>
 <li><b><i>size</i></b> &mdash; integer number of bytes to write</li>
 <li><b><i>data</i></b> &mdash; integer value to write</li>
 <li><b><i>blob</i></b> &mdash; blob of data to write</li>
 </ul>

 <p>
 The first form writes the <b><i>data</i></b> value to CPU memory at virtual address <b><i>address</i></b>, occupying <b><i>size</i></b> bytes.
 <b><i>size</i></b> can be 1, 2, 4, or 8 bytes.
 </p>

 <p>
 The second form writes an arbitrary <a href='#blob'>blob</a> of data to CPU memory at virtual address <b><i>address</i></b>.
 If the blob contains a mask, the mask is used when applying the data.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>

 <a name='funcs-cdda'></a>
 <h3>CD-DA functions</h3>

 <p>
 These functions query or control playback of CD digital audio.
 They are only available to in-game environments.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='cdda_lba_to_track'></a>
 <h5>cdda_lba_to_track</h5>
 <p class='funcdesc'>cdda_lba_to_track(<i>lba</i>)</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>lba</i></b> &mdash; integer specifying LBA address to convert</li>
 </ul>

 <p>
 Converts the provided linear block address <b><i>lba</i></b> to a track number (1-99).
 </p>

 <p>
 Invalid <b><i>lba</i></b> values return 0.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='cdda_measure_silence'></a>
 <h5>cdda_measure_silence</h5>
 <p class='funcdesc'>cdda_measure_silence(<i>track</i>)</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>track</i></b> &mdash; integer specifying the audio track to examine</li>
 </ul>

 <p>
 Scans the audio data for track number <b><i>track</i></b> to find the first non-zero sector.
 </p>

 <p>
 Returns the track-relative sector index of the first non-zero sector.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='cdda_play'></a>
 <h5>cdda_play</h5>
 <p class='funcdesc'>cdda_play(<i>lbastart</i>, <i>lbastop</i>)</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>lbastart</i></b> &mdash; integer specifying the starting audio playback position</li>
 <li><b><i>lbastop</i></b> &mdash; integer specifying the ending audio playback position</li>
 </ul>

 <p>
 Begins audio playback at <b><i>lbastart</i></b>, stopping at <b><i>lbastop</i></b>.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='cdda_set_offset'></a>
 <h5>cdda_set_offset</h5>
 <p class='funcdesc'>cdda_set_offset(<i>sectors</i>)</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>sectors</i></b> &mdash; integer specifying the sector offset</li>
 </ul>

 <p>
 Sets the global audio playback offset to <b><i>sectors</i></b>, meaning that all future play requests will be implicitly offset by the provided amount.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='cdda_status'></a>
 <h5>cdda_status</h5>
 <p class='funcdesc'>cdda_status()</p>

 <p>
 Returns 1 if CD audio is currently playing, 0 if CD audio is stopped or paused, or -1 if there is no CD audio available.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='cdda_track_to_lba'></a>
 <h5>cdda_track_to_lba</h5>
 <p class='funcdesc'>cdda_track_to_lba(<i>track</i>)</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>track</i></b> &mdash; integer specifying the track number to convert</li>
 </ul>

 <p>
 Returns the linear block address of the start of <b><i>track</i></b>.
 </p>

 <p>
 Invalid <b><i>track</i></b> values return 0.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>

 <a name='funcs-dos'></a>
 <h3>DOS functions</h3>

 <p>
 These functions are specific to DOS systems.
 They are only available to in-game environments.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='dos_psp'></a>
 <h5>dos_psp</h5>
 <p class='funcdesc'>dos_psp()</p>

 <p>
 Returns the segment index of the current program’s PSP.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>
 <!------------------------------------------------------------------------------------------------------------------------------------>

 <a name='funcs-windows'></a>
 <h3>Windows functions</h3>

 <p>
 These functions are specific to Windows systems.
 They are only available to in-game environments.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='winactive'></a>
 <h5>winactive</h5>
 <p class='funcdesc'>winactive()</p>

 <p>
 Returns a handle to the currently active window.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='wincontext_get'></a>
 <h5>wincontext_get</h5>
 <p class='funcdesc'>wincontext_get()</p>

 <p>
 Returns a handle to the currently executing process context.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='wincontext_set'></a>
 <h5>wincontext_set</h5>
 <p class='funcdesc'>wincontext_set(<i>process</i>)</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>process</i></b> &mdash; integer (handle) or string (name) of process whose context we want</li>
 </ul>

 <p>
 Sets the currently active process context to <b><i>process</i></b>, which can be specified either by handle or by name.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='wincontrol_click'></a>
 <h5>wincontrol_click</h5>
 <p class='funcdesc'>wincontrol_click(<i>id</i>[, <i>window</i>])</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>id</i></b> &mdash; integer specifying the control ID</li>
 <li><b><i>window</i></b> (optional) &mdash; string specifying the title of the window containing the control</li>
 </ul>

 <p>
 Simulates a click on the control specified by <b><i>id</i></b>.
 </p>

 <p>
 If <b><i>window</i></b> is specified, DREAMM looks for the control in the provided window; otherwise it looks in the active window.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='winregistry_import_file'></a>
 <h5>winregistry_import_file</h5>
 <p class='funcdesc'>winregistry_import_file(<i>filename</i>)</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>filename</i></b> &mdash; string containing the path to the file to import</li>
 </ul>

 <p>
 Reads the contents of the .REG file at <b><i>filename</i></b> and imports those entries into the current registry.
 </p>

 <p>
 The .REG file is interpreted as a standard Windows Registry file, with two minor extensions:
 </p>

 <ol>
 <li>
 Registry keys are recursively created. This means you don’t need to provide empty keys leading up to the current key.
 </li>
 <li>
 You can provide values that only get written if the value doesn’t already exist by using <span class='fixed'>=?</span> instead of <span class='fixed'>=</span>. For example <span class='fixed'>"WindowHeight"=?dword:000001e0</span> will only write the WindowHeight value if it’s not already present. This can be used to set default values that the user can later override.
 </li>
 </ol>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='winregistry_import_raw'></a>
 <h5>winregistry_import_raw</h5>
 <p class='funcdesc'>winregistry_import_raw(<i>string</i>)</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>string</i></b> &mdash; string containing the contents of a .REG file</li>
 </ul>

 <p>
 Treats <b><i>string</i></b> as a .REG file and imports those entries into the current registry.
 </p>

 <p>
 Note that this registry string supports the same extensions described in <a href='#winregistry_import_file'>winregistry_import_file</a>.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='winmodule_base'></a>
 <h5>winmodule_base</h5>
 <p class='funcdesc'>winmodule_base(<i>module</i>)</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>module</i></b> &mdash; string containing the name of the module</li>
 </ul>

 <p>
 Returns the image base for the given <b><i>module</i></b>, which can be specified either by handle or by name.
 </p>

 <!------------------------------------------------------------------------------------------------------------------------------------>
 <a name='winmodule_size'></a>
 <h5>winmodule_size</h5>
 <p class='funcdesc'>winmodule_size(<i>module</i>)</p>

 <p style='margin-bottom:0'>Parameters:</p>
 <ul style='margin-top:0'>
 <li><b><i>module</i></b> &mdash; string containing the name of the module</li>
 </ul>

 <p>
 Returns the in-memory size of the given <b><i>module</i></b>, which can be specified either by handle or by name.
 </p>

</body>
</html>
