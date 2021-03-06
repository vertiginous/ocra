= ocra

* http://rubyforge.org/projects/ocra/
* http://github.com/larsch/ocra/

== DESCRIPTION:

OCRA (One-Click Ruby Application) builds Windows executables from Ruby
source code. The executable is a self-extracting, self-running
executable that contains the Ruby interpreter, your source code and
any additionally needed ruby libraries or DLL.

== FEATURES/PROBLEMS:

* LZMA Compression (optional, default on)
* Windows support only
* Ruby 1.9 support
* Both console programs and desktop programs supported (no console will
  pop up with .rbw files).

If you experience problems with OCRA or have found a bug, please use
the tracker on the RubyForge project page
http://rubyforge.org/projects/ocra/. You are welcome to ask questions
in the forums there aswell.

== TODO:

* Clean up using manual recursive deletion (not SHop).

== SYNOPSIS:

ocra [option] your/script.rb

* OCRA will load your script (using Kernel#load) and build the
  executable when it exits.

* Your program should 'require' all necessary files when invoked without
  arguments, so ocra can detect all dependencies.

* OCRA executables clear the RUBYLIB environment variable but set
  RUBYOPT to whatever value it had when you invoked OCRA.

* OCRA does not set up the include path. Use "$:.unshift
  File.dirname(__FILE__)" at the start of your script if you need to
  'require' additional source files in the same directory no matter
  what the user's current working directory is.

* Loaded DLLs are detected automatically but only those located in
  your Ruby installation are included. Automatic detection can be
  disabled using --no-autodll. DLLs can be manually added using the
  --dll option.

* The current working directory is left untouched by OCRA (Details
  below).

* When the script is running, the OCRA_EXECUTABLE environment variable
  points to the .exe (with full path).

== REQUIREMENTS:

* Windows
* Working Ruby installation
* MinGW Installation (for building the stub)

== INSTALL:

=== Gem

* gem install ocra

Can also be downloaded from http://rubyforge.org/frs/?group_id=8185

=== Stand-alone

Get ocrasa.rb from http://rubyforge.org/frs/?group_id=8185. Requires
nothing but a working Ruby installation on Windows.

== TECHNICAL DETAILS

The OCRA stub extracts the Ruby interpreter and your scripts into a
temporary directory. The directory will contains the same directory
layout as your Ruby installlation. The source files for your
application will be put in the 'src' subdirectory.

=== Libraries

Rubygems will be automatically included in the OCRA executable.

Libraries found in non-standard path (for example, if you invoke OCRA
with "ruby -I some/path") will be placed into the site dir
(lib/ruby/site_ruby). Avoid changing $LOAD_PATH / $: from your script
to include paths outside your source tree.

Autoloaded libraries will be attempted loaded when building the
executable. Modules that doesn't exist will be ignore (but a warning
will be logged).

=== Environment variables

OCRA executables clear the RUBYLIB environment variable before your
script is launched. This is done to ensure that your script does not
use load paths from the end user's Ruby installation.

OCRA executables set the RUBYOPT environment variable to the value it
had when you invoked OCRA. For example, if you had "RUBYOPT=rubygems"
on your build PC, OCRA ensures that it is also set on PC's running the
executables.

OCRA executables set OCRA_EXECUTABLE to the full path of the
executable, fx. "C:\Program Files\MyApp\MyApp.exe".

=== Working directory

You should not assume that the current working directory when invoking
an executable built with .exe is the location of the source script. It
can be the directory where the executable is placed (when invoked
through the Windows Explorer), the users' current working directory
(when invoking from the Command Prompt), or even C:\WINDOWS\SYSTEM32
when the executable is invoked through a file association. You can
optionally change the directory yourself:

   Dir.chdir File.dirname(__FILE__)

If you wish to maintain the user's working directory, but need to
'require' additional Ruby scripts from the source directory, you can
add the following line to your script:

   $LOAD_PATH.unshift File.dirname(__FILE__)

=== $LOAD_PATH/$: mangling

Adding paths to $LOAD_PATH or $: at runtime is not recommended. Adding
relative load paths depends on the working directory being the same as
where the script is located (See above). If you have additional
library files in directories below the directory containing your
source script you can use this idiom:

   $LOAD_PATH.unshift File.join(File.dirname(__FILE__), 'path/to/script')

=== Detecting OCRA

You can detect whether OCRA is currently building your script by
looking for the 'OCRA' constant. If it is defined, OCRA is currenly
building the executable from your script. For example, you can use
this to avoid opening a GUI window when compiling executables:

   app = MyApp.new
   if not defined?(OCRA)
     app.main_loop
   end

=== Additional files and resources

You can add additional files to the OCRA executable (for example
images) by appending them to the command line. They should be placed
in the source directory with your main script (or a subdirectory).

   ocra.rb mainscript.rb someimage.jpeg docs/document.txt

This will create the following layout in the temporary directory when
your program is executed:

   src/mainscript.rb
   src/someimage.jpeg
   src/docs/document.txt

Paths on the command line can include ** globs to include a hierachy
of files, for example: "ocra script.rb assets/**/*.png".

== CREDITS:

Thanks for Igor Pavlov for the LZMA compressor and decompressor. The
source code used was place into Public Domain by Igor Pavlov.

Erik Veenstra for rubyscript2exe which provided inspiration.

Dice for the default .exe icon (vit-ruby.ico,
http://ruby.morphball.net/vit-ruby-ico_en.html)

== LICENSE:

(The MIT License)

Copyright (c) 2009 Lars Christensen

Permission is hereby granted, free of charge, to any person obtaining
a copy of this software and associated documentation files (the
'Software'), to deal in the Software without restriction, including
without limitation the rights to use, copy, modify, merge, publish,
distribute, sublicense, and/or sell copies of the Software, and to
permit persons to whom the Software is furnished to do so, subject to
the following conditions:

The above copyright notice and this permission notice shall be
included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED 'AS IS', WITHOUT WARRANTY OF ANY KIND,
EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF
MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT.
IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY
CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT,
TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE
SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
