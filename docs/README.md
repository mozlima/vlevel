## About VLevel
VLevel keeps your music from making you jump out of your seat, and it keeps you from having to fiddle with the volume constantly. It's different from other dynamic compressors because it looks ahead. You can think of VLevel as someone who knows your music by heart, and turns the volume up during quiet passages, but smoothly turns it back down when he knows a loud part is coming. It's great for making CDs to listen to in your car, or to play background music on your computer.

VLevel is currently a [LADSPA](http://www.ladspa.org/) plugin and a command-line filter. You can use it with many Linux audio programs including XMMS, GStreamer, GLAME, GDAM, and others, and you can use it in shell scripts to level your music before burning a CD. In the future, it may be ported to other audio programs like SOX and WinAMP, and it will be made generally easier to use.


## Installing VLevel
VLevel is written on Linux, but it should work on any similar system. Soon I will make Win32 binaries and a cross-platform GUI drag-n-drop interface.

The latest version of VLevel (0.5) can be downloaded from SourceForge.net. Installation is simple:

tar -xzf vlevel-0.5.tar.gz
cd vlevel-0.5
make
su
make install

This installs both the command line filter and the [LADSPA](http://www.ladspa.org/) plugin. You can change the default paths by editing Makefile. For information on how to use the filter, and what to plug the plugin into, see the Using section.

# Using VLevel
VLevel currently works in two ways: as a filter (similar to cat or sort), and as a [LADSPA](http://www.ladspa.org/) plugin. The filter is installed as /usr/local/bin/vlevel-bin, and the plugin is installed as /usr/local/lib/ladspa/vlevel-ladspa.so.

## Filter
The command line filter is called vlevel-bin. It currently only accepts 16-bit little-endian data, but you can specify the number of channels with the --channels option. You can find out more with the --help option. There are some useful scripts in the docs/ folder. Here are some examples:

See a summary of arguments
vlevel-bin --help

VLevel and play infile.ogg
ogg123 -d raw -f - infile.ogg | vlevel-bin | artscat

VLevel infile.wav and save as outfile.wav
sox infile.wav -t raw - | vlevel-bin | sox -t raw -r 44100 -c 2 -ws - outfile.wav

VLevel all .ogg files in current directory
docs/vlevel-dir

## Plugin
[LADSPA](http://www.ladspa.org/) is a plugin architecture, and a LADSPA plugin like VLevel works with any program that can load LADSPA plugins. For a partial list of such programs, see the LADSPA site. Though not on that list, XMMS can use VLevel and other LADSPA plugins via XMMS LADSPA. Many programs don't need this, but if the plugin isn't found, add /usr/local/lib/ladspa to the LADSPA_PATH environment variable.

## Settings
### Look-Ahead
This controls how much audio VLevel will buffer, and long before a volume change VLevel will start compensating. If the look-ahead is 2 seconds, VLevel will begin ramping the volume down 2 seconds before a loud part is played. In the LADSPA plugin, the units are seconds. In the filter, the units are samples, so for for a 2 second buffer of 44.1 kHz audio, the argument would be "--length 88200".
### Strength
This controls how much VLevel changes the data. If it is 0, the volume will not be changed at all. If it is 1, every sound will be the same volume. I use .8, but it's a matter of preference. This corresponds to the filter's --strength argument.
### Maximum multiplier
VLevel doesn't make loud parts quieter, it makes quiet parts louder. When audio is output, it is multiplied by the multiplier, and the multiplier changes so that the output sample is never clipped. To keep static from becoming audible, you can limit this with maximum multiplier. Settings between 10 and 20 work well. When using the plugin, make sure you set Use Maximum Multiplier. This corresponds to the filter's --max-multiplier setting, and negative values disable it.
### Undo
Although the results aren't perfect, it's possible to undo the effects of VLevel by setting this. Use the same strength as you first used to VLevel. To undo with the filter, you must set strength to s / (s - 1), where s is the strength you originally used. For more information, see the Technical section.
### Current Multiplier
This plugin output simply shows you how much the samples are currently being multiplied by. It's useful for finding the right strength and multiplier limit. It always changes gradually, especially with large look-ahead settings.
