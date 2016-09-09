# Network Radio on OpenWrt

## Steps

Tested on MediaTek LinkIt Smart 7688 board.

1. Installation

   Option 1 - build your own sysupgrade bin with `mpd-full`, `mpc`, `ffmpeg` and programm it to board. (**RECOMMEND as option 2 may fail because of STORAGE NOT ENOUGH**)

   Option 2 - install `mpd-full`, `mpc`, `ffmpeg` by `opkg` with the following steps:

   ```
   -> Add opkg source
   root@mylinkit:/# vi /etc/opkg.conf

   src/gz chaos_calmer https://downloads.openwrt.org/chaos_calmer/15.05/ramips/mt7628/packages/packages
   dest root /
   dest ram /tmp
   lists_dir ext /var/opkg-lists
   option overlay_root /overlay
   option check_signature 1

   -> Update opkg source
   root@mylinkit:/# opkg update

   -> Install package
   root@mylinkit:/# opkg install mpd-full mpc ffmpeg
   ```

2. Config `mpd`

   Edit /etc/mpd.conf as the following.

   ```
   music_directory "/home/music"
   playlist_directory "/home/.mpd/music_playlist"
   db_file "/home/.mpd/mpd.db"
   log_file "/home/.mpd/mpd.log"
   pid_file "/home/.mpd/mpd.pid"
   state_file "/home/.mpd/mpd.state"
   user "root"
   group "users"
   bind_to_address "0.0.0.0"
   port "6600"
   input {
       plugin "curl"
   }
   audio_output {
       type "alsa"
       name "My ALAS Device"
   }

   filesystem_charset "UTF-8"
   ```

   NOTE: you are required to create `/home/music` and `/home/.mpd` and `/home/.mpd/music_playlist` by yourself, using the following command:

   ```
   mkdir -p /home/music
   mkdir -p /home/.mpd/music_playlist
   ```

3. Start `mpd`

   ```
   /etc/init.d/mpd start
   ```

3. Add playlist

   ```
   mpc add mms://media.fm1036.com/liveaudio
   mpc add mms://live.fm993.com.cn/musicfm
   mpc add mms://live.hitfm.cn/fm887
   ```

   More network radio station resource:

   - http://blog.renren.com/share/333390391/14357648754
   - http://blog.csdn.net/aminfo/article/details/7646883
   - http://blog.csdn.net/aminfo/article/details/7646887

4. Play

   ```
   mpc play [1/2/3]
   ```

## Usage of  `mpc`

```
Usage: mpc [options] <command> [<arguments>]
mpc version: 0.26

Options:
  -v, --verbose              Give verbose output
  -q, --quiet                Suppress status message
  -q, --no-status            synonym for --quiet
  -h, --host=<host>          Connect to server on <host>
  -P, --password=<password>  Connect to server using password <password>
  -p, --port=<port>          Connect to server port <port>
  -f, --format=<format>      Print status with format <format>
  -w, --wait                 Wait for operation to finish (e.g. database update)

Commands:
  mpc                                                   Display status
  mpc add <uri>                                         Add a song to the current playlist
  mpc crop                                              Remove all but the currently playing song
  mpc current                                           Show the currently playing song
  mpc del <position>                                    Remove a song from the current playlist
  mpc play [<position>]                                 Start playing at <position> (default: 1)
  mpc next                                              Play the next song in the current playlist
  mpc prev                                              Play the previous song in the current playlist
  mpc pause                                             Pauses the currently playing song
  mpc toggle                                            Toggles Play/Pause, plays if stopped
  mpc cdprev                                            Compact disk player-like previous command
  mpc stop                                              Stop the currently playing playlists
  mpc seek [+-][HH:MM:SS]|<0-100>%                      Seeks to the specified position
  mpc clear                                             Clear the current playlist
  mpc outputs                                           Show the current outputs
  mpc enable [only] <output # or name> [...]            Enable output(s)
  mpc disable [only] <output # or name> [...]           Disable output(s)
  mpc toggleoutput <output # or name> [...]             Toggle output(s)
  mpc shuffle                                           Shuffle the current playlist
  mpc move <from> <to>                                  Move song in playlist
  mpc playlist [<playlist>]                             Print <playlist>
  mpc listall [<file>]                                  List all songs in the music dir
  mpc ls [<directory>]                                  List the contents of <directory>
  mpc lsplaylists                                       List currently available playlists
  mpc load <file>                                       Load <file> as a playlist
  mpc insert <uri>                                      Insert a song to the current playlist after the current track
  mpc save <file>                                       Save a playlist as <file>
  mpc rm <file>                                         Remove a playlist
  mpc volume [+-]<num>                                  Set volume to <num> or adjusts by [+-]<num>
  mpc repeat <on|off>                                   Toggle repeat mode, or specify state
  mpc random <on|off>                                   Toggle random mode, or specify state
  mpc single <on|off>                                   Toggle single mode, or specify state
  mpc consume <on|off>                                  Toggle consume mode, or specify state
  mpc search <type> <query>                             Search for a song
  mpc find <type> <query>                               Find a song (exact match)
  mpc findadd <type> <query>                            Find songs and add them to the current playlist
  mpc list <type> [<type> <query>]                      Show all tags of <type>
  mpc crossfade [<seconds>]                             Set and display crossfade settings
  mpc clearerror                                        Clear the current error
  mpc mixrampdb [<dB>]                                  Set and display mixrampdb settings
  mpc mixrampdelay [<seconds>]                          Set and display mixrampdelay settings
  mpc update [<path>]                                   Scan music directory for updates
  mpc sticker <uri> <get|set|list|delete|find> [args..] Sticker management
  mpc stats                                             Display statistics about MPD
  mpc version                                           Report version of MPD
  mpc idle [events]                                     Idle until an event occurs
  mpc idleloop [events]                                 Continuously idle until an event occurs
  mpc replaygain [off|track|album]                      Set or display the replay gain mode
  mpc channels                                          List the channels that other clients have subscribed to.
  mpc sendmessage <channel> <message>                   Send a message to the specified channel.
  mpc waitmessage <channel>                             Wait for at least one message on the specified channel.
  mpc subscribe <channel>                               Subscribe to the specified channel and continuously receive messages.

See man 1 mpc for more information about mpc commands and options
```

## Reference

- http://bbs.widora.org/t/mpd-full/58
- http://www.tristancollins.me/computing/bbc-streaming-radio-script-for-mpd/
- http://cagewebdev.com/raspberry-pi-playing-internet-radio/
- https://silkemeyer.net/wifihifi-how-to-run-music-player-daemon-on-an-openwrt-wifi-router
- https://forum.openwrt.org/viewtopic.php?id=49013
- https://www.musicpd.org
