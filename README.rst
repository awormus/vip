=============================
VIP - VIM integration for PHP
=============================

:Author:    Tobias Schlitt
:Revision:  $Rev: 22 $
:Date:      $Date: 2006-11-29 22:06:50 +0100 (Wed, 29 Nov 2006) $
:Status:    Beta

This repository contains the most recent version of VIP, VIM integration for
PHP, including the phpDocumentor for VIM (PDV) script. The main component is a
VIM file type plugin, which configures a lot of VIM enhancements for more
comfortable PHP editing in VIM. 

In addition, this repository also contains useful VIM files I found over the
web and some self made enhancements for PHP and other languages. Copyright and
licensing is noted in the specific files, if different than my own.

.. contents::

Installing the whole VIP package
================================

If you want to just use the whole VIP package, maybe because you don't have any
vim settings yet, you can use the ``install.sh`` command.

It will tell you to backup/remove your current vim configs and symlink the
necessary VIP folders to your home directory.::

   git clone https://github.com/tobyS/vip.git
   cd vip
   ./install.sh
   # Don't remove your clone as the files are only symlinked


PDV - phpDocumentor for VIM
===========================

PDV provides auto documentation functionality for PHP code in VIM. You can
easily create documentation templates for a lot of PHP code constructs easily,
which are pre-filled with sensible values. The documentation generated by PDV is
parseable by phpDocumentor_, the standard tool for PHP inline documentation.

The following code constructs are documentable using PDV:

- classes
- methods
- attributes
- functions
- constants

You can either use PDV on a single code construct, or on a range of code
constructs. PDV will generate the appropriate doc tags for all contained
elements. It will fill in the standard values you provide and try to guess as
much data from your code as possible (like the data type of attributes and
function/method parameters).

.. _phpDocumentor: http://phpdoc.org

Installing PDV
--------------

To install PDV, you simply need to download php-doc.vim from this repository
and put it into your local VIM directory, which should be (on Unix systems)
located in::

    ~/.vim/

Additionally, you need to source this file from your .vimrc or your VIM
file type plugin for PHP files. To source the script, simply add the following
line to the file of your choice::

    source ~/.vim/php-doc.vim

If you simply want to test PDV once, without using it every time you edit a PHP
file, you can also source it once in a running VIM session. Just type ::

    :source ~/.vim/php-doc.vim

in normal mode.

Now you have PDV included and the PDV functions are publicly available. PDV
contains 2 main functions:

PhpDocSingle()
    Which is responsible for documenting a single PHP construct.
PhpDocRange()
    Which you can use to document a whole range of PHP constructs.

You can either choose to call these functions manually (which is quite
uncomfortable) by moving the cursor to a line which you desire to document, or
select a range of lines you'd like to document, and type ::

    :call PhpDocSingle()

or ::

    :call PhpDocRange()

in normal / visual mode.

More comfortably, you should add key mappings to your .vimrc for the PDV
functions. I am using <Ctrl>+p for this (short to remember P, for PDV): ::

    inoremap <C-P> :call PhpDocSingle()<CR>i
    nnoremap <C-P> :call PhpDocSingle()<CR>
    vnoremap <C-P> :call PhpDocRange()<CR>

With this, you simply need to press <Ctrl>+p anywhere (normal-, insert- or
visual-mode) to run PDV. It will document appropriate.

Configuring PDV
---------------

PDV fills in tags as it "thinks" is appropriate. Because development
environments are highly different, the values that are appropriate for me my be
completely inappropriate for you. Therefore, you can configure almost all of
them to suite your needs. The configuration values can be found at the very top
of php-doc.vim in the "Globals" section.

Please do yourself a favor and do not edit anything below this section, as long
as you are not absolutely sure, what you are doing!

The configuration is done through global VIM script variables. All variables
utilized by PDV start with g: (for global scope) and are prefixed by pdv_cfg_*
for namespacing reasons.

The first section configures the comment chars to use. You usually should not
have to edit those, because they are already appropriate for the phpDocumentor
standard. Nevertheless, you could configure the following settings here:


g:pdv_cfg_CommentHead
    The first line of a comment. The default value here is ``"/**"`` , which
    introduces a new phpDocumentor conform documentation block.
g:pdv_cfg_Comment1
    In phpDocumentor each comment line starts with a star. Actually, with a
    space and a char. This variable defines the prefix for the first actual
    comment line. The default value " * " should be appropriate here.
g:pdv_cfg_Commentn
    Because in some rare cases, you possible want to change the prefix of the
    first comment line independent of the rest of the comment lines. This can be
    done, using this variable. The default value again is " * ", as in the
    phpDocumentor standard.
g:pdv_cfg_CommentTail
    This variable defines the ending line of a phpDocumentor block. The default value
    is ``" */"``.
g:pdv_cfg_CommentSingle
    While PDV currently does not generate single line comments (mainly, because
    they are not part of the phpDocumentor standard), this variable could be
    used to configure their appearance. Default is "//".

Much more important than the settings above is the next section of options,
which takes care for default values:

g:pdv_cfg_Type
    This setting defines the default type of attributes and parameters. PDV
    tries to determine the type first, using several mechanisms, like type
    hints and default values. If it cannot guess the type, it uses this value,
    which is "mixed", per default.
g:pdv_cfg_Package
    The class level doc block should contain the @package tag, according to
    phpDocumentor standard. PDV will use the value of this configuration variable
    here. You just adjust this in each package, to suite your needs. NOTE:
    There can not be any sensible default value!
g:pdv_cfg_Version
    Every class level doc block should have a version number tag. The value for
    this variable is usually a placeholder again, which will be replaced by a
    3rd party program (like CVS or SVN) or a custom script. The default value
    here is "//autogen//", which can be used with a custom script.
g:pdv_cfg_Author
    The @author tag is present in every doc block, usually. It defines the name
    (and possibly mail address) of the programmer who has written the code. You
    should enter your name here. Per default, this setting is empty, which is
    usually not a sensible default.
g:pdv_cfg_Copyright
    The @copyright tag is also available at class level only. There is no
    sensible default value for this tag.
g:pdv_cfg_License
    As the @copyright tag, the @license tag is present at class level. It
    contains license information for your code. You need to customize this,
    since there is no sensible default value for this tag.
g:pdv_cfg_ReturnVal
    PHP does not provide much information to determine the return type of a
    method/function. Actually, PDV currently does not support any kind.
    Therefore you need to define a value for the @return tag, which seems
    appropriate for you. The default value here is "void".
g:pdv_cfg_Uses
    The @uses tag is commonly used to explicitly document inheritance of
    classes and interface implementation. If you want to get the @uses tag
    generated for class level doc block, change the value of this variable to
    1. The default value is 0.
g:pdv_cfg_paste
    If you use the file type plugin provided here, you have features like
    automatic-close-char-mapping (e.g. for braces) and auto indentation
    activated. In this case, PDV needs to switch on paste mode in VIM before
    documenting a code block, because else the doc blocks will be broken. This
    setting usually does not hurt, because PDV stores the original setting and
    switches back to it after documenting. You can nevertheless switch it off
    by setting this value to 0.  The default value is 1.
g:pdv_cfg_php4always
    In contrast to PHP 5, in PHP 4 no scope modifiers existed. Therefore it was
    necessary to document the program scope of attributes and methods
    appropriate. Switching this setting to 1 will make PDV document the
    visibility scope of a PHP construct even if you have PHP 5 code (using the
    @access tag). For PHP 4 code it will in general document the scope,
    independent from the value of this setting. For guessing the `PEAR coding
    standard`_ is taken, which defines a private/protected construct to start
    its name with a _. The default value here is 0, since phpDocumentor
    determines scope values in PHP 5 automatically and there is no need to
    explicitly document it.
g:pdv_cfg_php4guessval
    If you have to document PHP 4 code, you can define the value which is
    chosen for guessing the scope using this variable. PEAR defines that
    elements prefixed by an _ are protected or private. You should change this
    setting to the value you use most. The default is "protected".

The last section defines the regular expressions used by PDV to recognize
certain code constructs. You should never change this settings, unless you
really know what you are doing or want your PDV installation to get broken
easily. Therefore, this section is only documented in the code and not here.

.. _`PEAR coding standard`: http://pear.php.net/manual/en/standards.php

The PHP file type plugin
========================

The PHP file type plugin (short ftplugin) provided in this package configures
VIM quite comfortable for coding PHP (at least in my eyes). It provides several
key mappings, settings and integration for external programs.

Installation
------------

To install the file type plugin, simply download the file .vim/ftplugin/php.vim
and put it into the same directory on your HD. You probably have to add the
following config line ::

    filetype plugin on

to your .vimrc to make VIM source file type plugins correctly. After that, all
settings should be sourced automatically, as soon as you start editing a PHP
file (note that the filename must end with ".php").

Additionally you can make VIM also treat .phps files as PHP files (which is
quite logical), by adding the following line to your .vimrc: ::

    au BufRead,BufNewFile *.phps		set filetype=php

Additional tools
----------------

To activate some of the features, the PHP ftplugin provides, you need external
programs to be installed.

PHPM support
~~~~~~~~~~~~

PHPM_ is a command line client to access the PHP documentation. It allows you to
display the signature of PHP functions in the VIM status bar. It is a command
line tool, which has to be accessible through your $PATH variable to make it
usable for VIM. Just download and install PHPM_.

A nice documentation about PHPM can be found here http://wiki.cc/php/Epc_phpm .

.. _PHPM: http://eide.org/

Function list
~~~~~~~~~~~~~

The auto completion for PHP functions relies on an external function list, which
can be found in the PHP CVS. Simply download it and store it into your home dir
as funclist.txt.

http://cvs.php.net/viewvc.cgi/phpdoc/funclist.txt

Features
--------

This section lists all features provided by the PHP file type plugin. Like the
plugin source itself, they are ordered in several sections.

Beside all other, the plugin first of all includes PDV. ;)

Settings
~~~~~~~~

expandtab
    In most PHP coding guidelines indentation is required to be done with spaces
    instead of tabs. This setting makes VIM expand tabs to 4 spaces (which is
    the most common value).
autoindent and smartindent
    Originally meant for C sources, this setting makes VIM intelligently indent
    and outdent your code as you time. For example, after the header of an
    if-block, you get an additional indentation level. After the fitting
    closing brace, the indentation is removed again.
textwidth
    I personally prefer my code not to be wrapped at a specific line length.
    Therefore, this setting is set to 0, so that your code is kept in 1 piece
    and not wrapped automatically.
nowrap
    VIM visually (not physically!) wraps lines at the end of your screen. This
    setting switches the behaviour off, so you need to scroll right to see full
    lines, if they exceed the size of your screen.
formatoptions
    The indentation behaviour is customizebale. This setting sets the
    appropriate value for PHP code.
makeprg and errorformat
    Originally this setting was intended to have a shortcut to GNU make for C
    programs. I remapped this to call "php -l" on the current file, which
    performs a PHP syntax. If an error occurs, VIM automatically jumps to the
    line where the error was reported by the PHP parser. Note: In some cases,
    this is not the line, where the error actually occurred. This happens e.g.
    when a ; is missing somewhere or if you missed to close a brace. This is a
    problem with parsing PHP, not with VIM!
syntax
    VIM supports built in syntax highlighting for a lot of file formats. Since
    some installations switch this feature off by default, this setting is used
    to switch it on again.

Command mappings
~~~~~~~~~~~~~~~~

Missing semicolon
    It happens quite often, that you missed a ; at the end of a line. Usually
    you have to move the cursor to the end of the line, enter insert mode, type
    the semicolon, exit insert mode and go back to the start of the line. This
    shortcut enables you to hit the ; key in normal mode on any line and make
    VIM check if it has a ; at the end. If not, VIM will automatically add it
    and return to the start of the line (in normal mode) for you.
PDV
    As described in the `Installing PDV`_ section, I recommend key mappings for
    the PDV functions. This mappings allow you to use the combination <Ctrl>+p
    to document your PHP code. In insert and normal mode, it will make PDV
    document the line under your cursor. In visual mode, it will make PDV
    detect all known code constructs in the selected range and document them. 
PHPM
    If you have `PHPM support`_ installed, you can simply access the PHP manual
    by hitting <Ctrl>+h on any PHP function name (in insert mode). PHPM will be
    called and the signature of the desired function will be shown in the
    status bar.
Align arrays / assignements
    The `PhpAlign()`_ function is mapped to <Ctrl>-a in visual mode. Simple select
    the lines to assign and hit the shortcut.
Commenting / uncommenting
    The `PhpUnComment()`_ function is mapped to <Ctrl>-c in visual mode. Simply
    select the lines you want to comment/un-comment and hit the shortcut.:

Closing chars
~~~~~~~~~~~~~

Normally, if you type a brace or quotes, you want to have the corresponding
counterpart to be available, too. VIM takes this nasty work away from you,
using these mappings. As soon as you type 1 of the following characters:

- (
- [
- {
- "
- '

in insert mode, VIM will place the corresponding counterpart after the actual
one and go one step backwards for you. You don't have to care for closing
matching braces and quotes anymore and can simply go on typing your code.

In some rare cases, where you need only 1 part of these characters, simply type
<Ctrl>-v before the actual char and you will only get 1 piece. Beside that, in
paste mode (:set paste) the corresponding counterparts will not be added, too.

Note: The handling of ( and { varies from coding guide to coding guide. I'm
currently using the `eZ systems`_ coding guidelines, which regulate, that after
every opening and before every closing brace a space is mandatory. If your
coding guidelines do not regulate this and you dislike the additional 2 spaces,
an alternative mapping is commented in the source of the FT plugin.

For matching curly braces {} the mapping automatically adds 2 line breaks for
you and leaves your cursor in the middle of the braces.

.. _`eZ systems`: http://ez.no

Wrapping visuals
~~~~~~~~~~~~~~~~

Another often occurring case is, that you need to wrap text/code into braces or
quotes, after you typed it. Usually you need to add the matching chars at both
ends of the string you want to wrap and the trick of `Closing chars`_ makes it
even more work in this case.

Using visual wrapping you can simply select the desired string in visual mode
and hit the char you want to use for wrapping. VIM will automatically do the
job for you.

Dictionary completion
~~~~~~~~~~~~~~~~~~~~~

If you have the PHP `Function list`_ available, the dictionary completion
setting makes it available for you in PHP code. Start typing a PHP function
name and hit the auto completion key (see below). If only 1 function matches
your request, VIM will simply complete it. In other cases, the behaviour
depends on your VIM version:

VIM 6.x
    This version will cycle through all matches found by `PHPM`_ one step every
    time you hit the auto completion key. If you reach the end of the list of
    possible matches, your original string will be recovered. If you hit the
    specific key once again, cycling will start again.
VIM 7.x
    The newer version of VIM will display a nice popup below the cursor
    position, showing all matches found. You can either cycle through these
    matches as described above. Additionally you can navigate the list using
    the cursor keys and select an alternative by hitting <Return>.

Auto completion
~~~~~~~~~~~~~~~

VIM does not only support completion after a dictionary as described in
`Dictionary completion`_, but also to complete all strings occurring in one of
the currently open documents. This actually means, that most of your custom
class, method and function names are available for auto completion, too.

Usually VIM uses some weird character sequence for auto completion (while I
actually don't even remember which one). As a console junky (I assume you are
one, if you like VIM), you are mostly used to have the <Tab> key for
completion. The PHP file type plugin provides this functionality for you and
maps the <Tab> key in insert mode to perform auto completion, if the cursor
resides directly behind a word character. In all other cases (at the start of
a line or behind a space) you will get a normally expanded tab.

Note: VIM will always try to use auto completion if your cursor resides behind a
character string. In cases where you need a real tab here, simply type a normal
space first and then hit the <Tab> key!

PhpAlign()
~~~~~~~~~~
    
Often you have written down an array declaration or a set of variable
assignements. Usually things look somewhat ugly the, like ::
    
    $foo = array(
        "test" => "test",
        "foo" => "bar",
        "something" => "somewhat",
        "anything more" => "and more and more",
    );

Aligning this definition properly is an ugly, boring work. The PhpAlign()
function takes it from you and aligns the array declaration properly: ::
    
    $foo = array(
        "test"          => "test",
        "foo"           => "bar",
        "something"     => "somewhat",
        "anything more" => "and more and more",
    );

This also works with usual variable assignements: ::

    $foo = "bar";
    $someVariable = "some value";
    $aVar = 23;

becomes ::

    $foo          = "bar";
    $someVariable = "some value";
    $aVar         = 23;

PhpUnComment()
~~~~~~~~~~~~~~

Often you want to comment or un-comment a couple of lines, because you
currently change those and want to make a backup or simply want to bring
alternative code in place. For multiple reasons you may not want to use
multi-line commens for this (e.g. because you the closing sequence inside the
code or because they simply look ugly. PhpUnComment() simply comments a line
which is not commented and un-comments a line that is commented. ::

    function test()
    {
        return "test";
    }
    // function test()
    // {
        // return 23;
    // }

Selecting these lines (all of them) and running PhpUnComment() results in: ::

    // function test()
    // {
        // return "test";
    // }
    function test()
    {
        return 23;
    }
    
The .vimrc
==========

In addition to the main parts of the PDV package (described above), I'm
providing my personal .vimrc file here, which contains some configuration
tricks which are not only (but also) useful for editing PHP source code.

Features
--------

Grep without SVN
    The VIM internal grep feature (which enables you to grep through files and
    jump from result to result) has 1 major problem: If you use it recursively
    and also use SVN, it gets all matches from the SVN internal files (history
    and stuff), too. To avoid this, the grep command is remapped to an
    external script, which ignores the SVN directories for you.
    TODO: The script is not in SVN, yet. I need to add it. If you want to use
    this feature, you currently need to write your own script and place it into
    /usr/bin with the name vimgrep.
Spell checking
    From version 7.0 VIM has a built in spell checking facility, which works
    using ISpell. You normally have to activate spelling manually and have to
    set your preferred language. The .vimrc maps this to <F5>. Note: You will
    need to adjust the language setting, if you want something else then US
    English.
Seeing the cursor
    If you ow a large screen with a high resolution, you sometimes search for
    your cursor. To save this time amount, I added a mapping, which places a
    nice long line below your cursor in insert mode. Next time simply hit <i>
    and see instantly, where you are currently editing.
Skeleton
    Every time you start a PHP file, you have to process the same work: Add
    opening and closing PHP tags. Using the skeleton file from this
    repository VIM saves this work for you. As soon as you start a new file,
    VIM places open and closing tag for you and leaves you in between of these,
    so you can instantly start editing.
.phps files
    Apache and other web servers recognize so called .phps files, which get
    displayed as highlighted PHP code. The .vimrc maps .phps files to be
    treated like .php files, so you have the same features available here.
Ruler and status
    VIM (by default) has no ruler (indicating your position in the file) and
    displays only rare status information at the end of the file. These
    settings get sensible values.
Folding
    VIM allows you to fold text blocks so that you keep an overview in huge
    files. By default, you have to manually close a fold or folding does not
    work at all. The .vimrc contains the necessary settings to activate that.
    Additionally it configures VIM to automatically close a fold as soon as you
    leave its area.
Searching
    Some distributions (e.g. Gentoo) have the "highlight search" feature of VIM
    activated by default and incremental search deactivated. I want it the
    exact other way around. "highlight search" (hlsearch) is annoying IMO. It
    highlights all search results in a document and does not provide a useful
    way to switch the highlight off again automatically (you probably have to
    search for something that does not exist to switch it off again). In
    contrast to that, incremental search is quite useful. It jumps to the first
    result of your search as you type.
Scrolling
    Usually, if your cursor hits the top or bottom of the screen and goes
    beyond it, VIM scrolls just 1 line at each hit. The .vimrc makes it more
    comfortable to scroll beyond the screen ends: First it moves the marker to
    perform scrolling at to 3 lines away from the real end of the screen.
    Second it makes VIM jump 5 lines at once and not only one, so that you do
    not need to scroll that much.
Broken backspace
    In some terminals, the backspace key might not act as expected. Probably it
    might not delete indentation characters or at the start of the line do not
    delete the line ending of the previous line. The .vimrc provided here fixes
    this behaviour.
Modeline
    The status line that indicates what the current mode is (normal, insert,
    visual) is per default not present. Using the .vimrc of this package, makes
    it available.


..
   Local Variables:
   mode: rst
   fill-column: 79
   End:
   vim: et syn=rst tw=79
