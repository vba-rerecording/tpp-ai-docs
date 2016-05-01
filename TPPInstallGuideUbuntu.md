# TPP Anniversary Crystal AI guide for Ubuntu

This guide is a step-by-step guide to installing vba-rerecording and running the
anniversary crystal AI on Ubuntu linux
and other debian derivatives. This guide assumes that you know what a terminal
is and how you can use it to navigate to specific directories. However in most
cases you can simply copy paste the commands here into the terminal and it will
work. If you have no idea what I am talking about, please read this helpful guide
created by much more qualified individuals up to file and directory commands:
https://help.ubuntu.com/community/UsingTheTerminal

This guide was created on linux 14.04 LTS. The packages mentioned here however
are also present on later versions and on most if not all other debian derivatives.

Now that we got that out of the way, let's begin the "real work".

## Getting VBA Re-recording

Please download the source code (tar.gz) (or tarball) from
https://github.com/vba-rerecording/vba-rerecording/releases. The latest
release is advised. Place this in a convenient location where we can extract
it without difficulty. You may also extract it somewhere, but you will
have to ignore some of these instructions. Do it at your own risk.

## Installing dependencies

Please open up a terminal and execute the following commands so that we have
all the libraries and tools required for the installation.

First make sure we have all the latest package information. This will prevent
errors in the next command

```
$ sudo apt-get update
```

Next do this

```
$ sudo apt-get install build-essentials cmake liblua5.1-dev libsdl1.2-dev
flex bison libpng12-dev zlib1g-dev
```

Now, if you intend to run the AI, also run the following:

```
$ sudo apt-get install lua-socket
```

That's all for the dependencies!

## Building vba-rerecording

Open a terminal (You can just reuse the one used in the previous step) and
navigate to the directory containing the source vba-rerecording tarball.

Extract it by using the following command (Replace `<tarball name>` with the
actual name of the tarball. This is common notation for a place holder for
information that depends on the users choices):

```
$ tar xf <tarball name>
```

This will extract the tarball and create a directory named after the
tarball minus the tar.gz extension. Navigate into this directory
like this:

```
$ cd <directory>
```

Now prepare for a release build:

```
$ mkdir build
$ cd build
$ cmake -DCMAKE_BUILD_TYPE=Release ..
```

If all went well you should see something like this:
```
<Lots of stuff you can ignore>
-- Configuring done
-- Generating done
-- Build files have been written to:  <directory>/build
```
See a fatal error im the stuff you can ignore? It is
completely safe to ignore that. That is a fatal error in a non-essential
part of the build. As long as you see the "Build files have been written
to:" line you should be fine. If you don't and you can't figure out what is
wrong please report your output in the appropriate subreddit thread.

Now we can actually start compiling

```
$ make
```

This will generate a lot of technical output including a whole bunch of warnings,
but eventually, if all goes well, you should see this:

```
[100%] Built target vba-rr
```

Congratulations you just compiled something! If you ever need an excuse
to slack off... ;)

Now that we have a compiled program we can install it. But first we
will pack it into a nice package so that installing and especially removing it will be much easier:

```
cpack -G DEB
```

This will generate a .deb file in the build
directory. Let's install it!

```
$ sudo dpkg -i <.deb file>
```

If everything went according to plan this should now generate the CLI help
output:

```
$ vba-rr --help
```

## Running Anniversary Crystal (No AI)

I am only going to explain the soft patch method because I think it's both safer and easier.

Download the Standalone, non AI version from [here](http://twitchplayspokemon.github.io/tppcrystal251/).

Place this in a location that is convenient for you. I reccommend placing it in an isolated
directory together with the ROM file (remember the md5 hash must be 9f2922b235a5eeb78d65594e82ef5dde)
You can verify this using the `md5sum <ROM file>` command). Now run vba-rr like this:

```
$ vba-rr -i <ips file> <ROM File>
```

## Running Anniversary Crystal with the AI

I am only going to explain the soft patch method because I think it's both safer and easier.

Download the AI build + dependencies from [here](http://twitchplayspokemon.github.io/tppcrystal251/).
Since this is zip archive place it in a further empty directory (The best you can do is
create a new directory). Open a terminal and navigate to this `<AI Build directory>`.

Now unzip the archive:

```
$ unzip <AI build + dependencies zip>
```

This will have created some new files in the directory. What I want you to look
for is the VBA-RR-V243/lua directory. Go into this directory and *remove the socket
directory and the socket.lua file*. Now execute the `pwd` command. Remember
that output. I will refer to it as the `<lua directory>`.

Now start a new terminal. We will use this terminal to run the AI in.
Navigate to the `<AI Build directory>` and find your way into the AI
subdirectory. Start the AI server by running:

```
$ python3 AIServerStandalone.py
```

Now go back to the other terminal and run vba-rr like this (The ips file is
also somewhere in the `<AI Build directory` and you should know where the ROM is):

```
$ vba-rr -l <lua directory>/pokecrystal_ai.lua -i <ips file> <ROM file>
```

You MUST provide the full `<lua directory>`.

Enjoy playing Anniversary Crystal with the advanced AI!

## Changing settings

The emulator comes with a well documented config file (/etc/vba-rr.cfg).
Copy this into your home directory (`/home/<username>`) and make any
changes you like. The most complicated thing in there is the joypad
configuration which is horrid. All numbers are in hex, but most options
give a list of sensible values to use. Do NOT turn the sound of unless
you have horrible (dreadful) performance. This will basically disable
throttling. Some (but not all) options can be overridden using the
command line. You may want to set the BatteryDir, saveDir and captureDir.
These directories must be absolute (begin at the root). Using a ~ for home
will not work!


## Uninstalling vba-rerecording

If you ever want to get rid of vba-rerecording for some reason use the
following command:

```
$ sudo dpkg --purge vba-rerecording
```
