FbTerm is a fast terminal emulator for linux with frame buffer device or VESA video card. Features include:

  * mostly as fast as terminal of linux kernel while accelerated scrolling is enabled
  * select font with fontconfig and draw text with freetype2, same as Qt/Gtk+ based GUI apps
  * dynamically create/destroy up to 10 windows initially running default shell
  * record scroll-back history for every window
  * auto-detect text encoding with current locale, support double width scripts like Chinese, Japanese etc
  * switch between configurable additional text encodings with hot keys on the fly
  * copy/past selected text between windows with mouse when gpm server is running
  * change the orientation of screen display, a.k.a. screen rotation
  * lightweight input method framework with client-server architecture
  * background image for eye candy


## News ##
### [2010-10-06] Release version 1.7 ###
  1. added redirecting /dev/tty0 output to FbTerm's sub-window
  1. added option "ambiguous-wide" to treat ambiguous CJK characters as wide width
  1. added option "font-height" to force font height
  1. added shortcut CTRL\_ALT\_K to kill the frozen IM server
  1. fixed a bug where curses line drawing characters give inverted questions marks
  1. fixed a text auto selection bug
  1. fixed a logical error in terminal insert mode
  1. fixed a few other bugs

From version 1.7, FbTerm redirects /dev/tty0 output to the pseudo terminal of current sub-window. In linux before version 2.6.10, anybody can do this as long as the output was not redirected yet; since version 2.6.10, only root or a process with the CAP\_SYS\_ADMIN capability may do this.

In a number of CJK encodings there are ambiguous width characters which have a width of either narrow or wide depending on the context of their use. By default, FbTerm treats them as narrow width characters, the new added option "ambiguous-wide" may be used to change the behavior.

### [2009-11-14] Release version 1.6 ###
  1. added VESA video card support
  1. added rendering messages for IM server development
  1. fixed a bug where Ctrl+Space is a shortcut even user run FbTerm without "input-method" option
  1. fixed a bug where user compile FbTerm without gpm mouse support but run it in a gpm server enabled environment
  1. fixed a IM program dead loop bug triggered by FbTerm's crash
  1. fixed several spelling errors in FbTerm's help message and man-page

From version 1.6, FbTerm adds VESA video card support. By default, FbTerm tries frame buffer device first, if failure, then tries to use VESA device with highest resolution and color depth. option "vesa-mode" may be given to force only open VESA device with the specified video mode. To see available video modes for your VESA card, execute _'fbterm --vesa-mode=list'_.

VESA support requires root privilege to work, and user don't force to use VESA device on the system with frame buffer device enabled, they maybe conflict with each other.

### [2009-04-26] Release version 1.5 ###
  1. added support for text rendering with background image
  1. added command-line arguments to customize command executed in sub-window
  1. added Alt-Fn and all FbTerm's shortcuts support when input method is active
  1. added option "-v/--verbose" to show some useful information
  1. fixed some text color issues with version 1.4
  1. fixed encoding selection error when locale is C/POSIX
  1. fixed a bug where screen is cleared on startup even in inactive tty
  1. fixed a bug where variable HOME is not defined

From version 1.5, user can execute FbTerm with _`fbterm [options] [--] [command [arguments]]`_. Normally FbTerm checks the SHELL variable. If that is not set, it tries to use the user's login shell program specified in the password file. If that is not set, /bin/sh will be used. _`command [arguments]`_ may be given to override the built-in choice of shell program. You should use the _--_ argument to separate FbTerm's options from the arguments supplied to the _command_.


Background image support has been added in this version. FbTerm doesn't load and parse any  image file with various formats directly, instead it takes a screenshot of frame buffer device on startup if variable FBTERM\_BACKGROUND\_IMAGE is defined, then uses this screenshot as the background for text rendering. In order to enable background image support, user should first put a image to frame buffer device with a image viewer. The updated FbTerm's man page contains a demo script using [fbv](http://s-tech.elsat.net.pl/fbv/). Here is a screenshot:

![http://fbterm.googlecode.com/files/fbterm-with-background-images.png](http://fbterm.googlecode.com/files/fbterm-with-background-images.png)
<br>

<h3>[2009-03-14] Release version 1.4</h3>
<ol><li>improved text rendering performance<br>
</li><li>added private escape sequences for 256 color mode support<br>
</li><li>added a option "font-width" to adjust character cell width<br>
</li><li>added support for older 2.2/2.4 kernel<br>
</li><li>fixed a crash bug with bitmap fonts<br>
</li><li>fixed a configure failure in cross-compiling environment</li></ol>

xterm has a <a href='http://www.frexx.de/xterm-256-notes/'>256 color mode</a> extension, FbTerm also add it in this version. But xterm's 256 color escape sequences conflict with the linux sequences implemented by FbTerm, so private escape sequences were introduced to support this feature:<br>
<pre><code>ESC [ 1 ; n }                   set foreground color to n (0 - 255)<br>
ESC [ 2 ; n }                   set background color to n (0 - 255)<br>
ESC [ 3 ; n ; r ; g ; b }       set color n to (r, g, b) ,  n, r, g, b all in (0 - 255)<br>
</code></pre>

and a new terminfo database entry named "fbterm" was added to use these private sequences, all program based on terminfo should work with it. By default, FbTerm sets environment variable "TERM" to value "linux", you need run "TERM=fbterm /path/to/program" to enable 256 color mode.<br>
<br>
<h3>[2008-12-20] Release version 1.3</h3>
<ol><li>added command line arguments to change option values<br>
</li><li>added client-server based input method framework<br>
</li><li>added screen rotation support<br>
</li><li>added support for visual type DIRECTCOLOR used by ATI cards (thanks for Witek's patch)<br>
</li><li>fixed a bug that user can't input some unicode characters<br>
</li><li>fixed a bug of maybe not restore original console state after FbTerm exited<br>
</li><li>fixed several trivial bugs<br>
</li><li>added using file system capability attributes offered by kernel 2.6.27, instead of setting set-user-ID bit on FbTerm<br>
</li><li>decreased memory usage of every shell instance by changing size of the struct saving every character's attribute from 4 to 2 bytes</li></ol>

In version 1.3, FbTerm first uses option value specified in command line arguments, then in the configure file $HOME/.fbtermrc. The format of configure file has been changed from "option_name=val" to "option-name=val", and several option names been modified to keep conformance with command line arguments. Old $HOME/.fbtermrc should be deleted when your begin to run this new version.<br>
<br>
Instead of adding input method directly in FbTerm, a client-server based input method framework is designed to do this work. FbTerm acts as a client, standalone IM program as a server, they run in separated processes and communicate each other with predefined<br>
IM messages in a unix socket pair. FbTerm provides a guide document and a IM demo to help developers understand the IM framework, and the fundamental sources of demo will make IM development more easier. Here is the screenshot of this demo on clockwise 270 degrees rotated screen.<br>
<br>
<a href='http://fbterm.googlecode.com/svn/image/im.png'><img src='http://fbterm.googlecode.com/svn/image/im_s.png' /></a>

Everyones who want to write IM servers for FbTerm are very welcome. At present, two IM servers are under heavy development. If you are a normal IM user, please stay tuned!<br>
<br>
<br>
<h3>[2008-10-12] Release version 1.2</h3>
<ol><li>improved escape and control sequences compatibility with linux console, support sequences for changing the color palette and cursor shape<br>
</li><li>added option to adjust default cursor shape and flash interval<br>
</li><li>added option to modify chars considered as part of a word while auto-selecting text<br>
</li><li>added handle signal SIGHUP, FbTerm will exit normally while shutdown system directly in it<br>
</li><li>fixed a freeze issue caused by changing console win-size under kernel 2.6.26<br>
</li><li>fixed a crash bug when font size is greater than screen size<br>
</li><li>cleaned text selection code and fixed a crash bug<br>
</li><li>fixed a error of not repainting screen when switch back to virtual console which FbTerm running on, but FbTerm not have active window<br>
</li><li>fixed a buffer overflow issue which may cause denial of service via application crash</li></ol>

FbTerm now support linux control sequence for changing the color palette, let's see a example:<br>
<pre><code>    if [ "$TERM" = "linux" ]; then<br>
        echo -en "\e]P0222222" #black<br>
        echo -en "\e]P8222222" #darkgray<br>
        echo -en "\e]P1803232" #darkred<br>
        echo -en "\e]P9982b2b" #red<br>
        echo -en "\e]P25b762f" #darkgreen<br>
        echo -en "\e]PA89b83f" #green<br>
        echo -en "\e]P3aa9943" #brown<br>
        echo -en "\e]PBefef60" #yellow<br>
        echo -en "\e]P4324c80" #darkblue<br>
        echo -en "\e]PC2b4f98" #blue<br>
        echo -en "\e]P5706c9a" #darkmagenta<br>
        echo -en "\e]PD826ab1" #magenta<br>
        echo -en "\e]P692b19e" #darkcyan<br>
        echo -en "\e]PEa1cdcd" #cyan<br>
        echo -en "\e]P7ffffff" #lightgray<br>
        echo -en "\e]PFdedede" #white<br>
        clear #for background artifacting<br>
    fi<br>
</code></pre>

As with all escape codes, it begins with a little prefix indicating what the escape is actually doing: in this case "\e]P", which is the "set color" escape. The format for the data is "XRRGGBB" where X is the number of the color to modify (in hex). This is a standard 16 color notation, which I have commented above. RRGGBB indicates the red/green/blue values (0-255). To reset the color palette, use "\e]R".<br>
<br>
<br>
<h3>[2008-08-23] Release version 1.1</h3>
<ol><li>add configurable additional text encoding support and switch between them with hot keys<br>
</li><li>add option in configure file to let user adjust max scroll-back history lines of every window<br>
</li><li>fixed cursor drawing issue while shell command line containing double width characters<br>
</li><li>fixed a spelling error in source for older kernel support<br>
</li><li>fixed a compile failure while kernel headers version less than 2.6.24</li></ol>


<h3>[2008-07-05] Release version 1.0</h3>