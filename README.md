### Hi! Here is a quick recap on what we did last sunday in the memory forensics workshop.

First things first, what's memory forensics? 
To keep it short and sweet, Memory forensics is forensic analysis of a computer's **memory dump**.
In our workshop we used a framework called **Volatility**. It is an open source memory forensics framework for incident response and malware analysis.

You can find the memory dump we work with in this [link](https://mega.nz/#!sh8wmCIL!b4tpech4wzc3QQ6YgQ2uZnOmctRZ2duQxDqxbkWYipQ "link").
#### It contains a real ransomware, so please do not execute it on your host system.
After getting that out of the way, lets get started:

## FINDING EVIL

- Detecting the OS:
	```` bash
	volatility -f OtterCTF.vmem imageinfo 
	````

- Listing all the running process:
	```` bash
	volatility -f OtterCTF.vmem --profile=Win7SP1x64 pstree
	````

```	
Name                                                  Pid   PPid   Thds   Hnds Time
-------------------------------------------------- ------ ------ ------ ------ ----
 0xfffffa801b27e060:explorer.exe                     2728   2696     33    854 2018-08-04 19:27:04 UTC+0000
. 0xfffffa801b486b30:Rick And Morty                  3820   2728      4    185 2018-08-04 19:32:55 UTC+0000
.. 0xfffffa801a4c5b30:vmware-tray.ex                 3720   3820      8    147 2018-08-04 19:33:02 UTC+0000
. 0xfffffa801b2f02e0:WebCompanion.e                  2844   2728      0 ------ 2018-08-04 19:27:07 UTC+0000
. 0xfffffa801a4e3870:chrome.exe                      4076   2728     44   1160 2018-08-04 19:29:30 UTC+0000
.. 0xfffffa801a4eab30:chrome.exe                     4084   4076      8     86 2018-08-04 19:29:30 UTC+0000
.. 0xfffffa801a5ef1f0:chrome.exe                     1796   4076     15    170 2018-08-04 19:33:41 UTC+0000
.. 0xfffffa801aa00a90:chrome.exe                     3924   4076     16    228 2018-08-04 19:29:51 UTC+0000
.. 0xfffffa801a635240:chrome.exe                     3648   4076     16    207 2018-08-04 19:33:38 UTC+0000
.. 0xfffffa801a502b30:chrome.exe                      576   4076      2     58 2018-08-04 19:29:31 UTC+0000
.. 0xfffffa801a4f7b30:chrome.exe                     1808   4076     13    229 2018-08-04 19:29:32 UTC+0000
.. 0xfffffa801a7f98f0:chrome.exe                     2748   4076     15    181 2018-08-04 19:31:15 UTC+0000
. 0xfffffa801b5cb740:LunarMS.exe                      708   2728     18    346 2018-08-04 19:27:39 UTC+0000
. 0xfffffa801b1cdb30:vmtoolsd.exe                    2804   2728      6    190 2018-08-04 19:27:06 UTC+0000
. 0xfffffa801b290b30:BitTorrent.exe                  2836   2728     24    471 2018-08-04 19:27:07 UTC+0000
.. 0xfffffa801b4c9b30:bittorrentie.e                 2624   2836     13    316 2018-08-04 19:27:21 UTC+0000
.. 0xfffffa801b4a7b30:bittorrentie.e                 2308   2836     15    337 2018-08-04 19:27:19 UTC+0000
 0xfffffa8018d44740:System                              4      0     95    411 2018-08-04 19:26:03 UTC+0000
. 0xfffffa801947e4d0:smss.exe                         260      4      2     30 2018-08-04 19:26:03 UTC+0000
 0xfffffa801a2ed060:wininit.exe                       396    336      3     78 2018-08-04 19:26:11 UTC+0000
. 0xfffffa801ab377c0:services.exe                     492    396     11    242 2018-08-04 19:26:12 UTC+0000
.. 0xfffffa801afe7800:svchost.exe                    1948    492      6     96 2018-08-04 19:26:42 UTC+0000
.. 0xfffffa801ae92920:vmtoolsd.exe                   1428    492      9    313 2018-08-04 19:26:27 UTC+0000
... 0xfffffa801a572b30:cmd.exe                       3916   1428      0 ------ 2018-08-04 19:34:22 UTC+0000
.. 0xfffffa801ae0f630:VGAuthService.                 1356    492      3     85 2018-08-04 19:26:25 UTC+0000
.. 0xfffffa801abbdb30:vmacthlp.exe                    668    492      3     56 2018-08-04 19:26:16 UTC+0000
.. 0xfffffa801aad1060:Lavasoft.WCAss                 3496    492     14    473 2018-08-04 19:33:49 UTC+0000
.. 0xfffffa801a6af9f0:svchost.exe                     164    492     12    147 2018-08-04 19:28:42 UTC+0000
.. 0xfffffa801ac2e9e0:svchost.exe                     808    492     22    508 2018-08-04 19:26:18 UTC+0000
... 0xfffffa801ac753a0:audiodg.exe                    960    808      7    151 2018-08-04 19:26:19 UTC+0000
.. 0xfffffa801ae7f630:dllhost.exe                    1324    492     15    207 2018-08-04 19:26:42 UTC+0000
.. 0xfffffa801a6c2700:mscorsvw.exe                   3124    492      7     77 2018-08-04 19:28:43 UTC+0000
.. 0xfffffa801b232060:sppsvc.exe                     2500    492      4    149 2018-08-04 19:26:58 UTC+0000
.. 0xfffffa801abebb30:svchost.exe                     712    492      8    301 2018-08-04 19:26:17 UTC+0000
.. 0xfffffa801ad718a0:svchost.exe                    1164    492     18    312 2018-08-04 19:26:23 UTC+0000
.. 0xfffffa801ac31b30:svchost.exe                     844    492     17    396 2018-08-04 19:26:18 UTC+0000
... 0xfffffa801b1fab30:dwm.exe                       2704    844      4     97 2018-08-04 19:27:04 UTC+0000
.. 0xfffffa801988c2d0:PresentationFo                  724    492      6    148 2018-08-04 19:27:52 UTC+0000
.. 0xfffffa801b603610:mscorsvw.exe                    412    492      7     86 2018-08-04 19:28:42 UTC+0000
.. 0xfffffa8018e3c890:svchost.exe                     604    492     11    376 2018-08-04 19:26:16 UTC+0000
... 0xfffffa8019124b30:WmiPrvSE.exe                  1800    604      9    222 2018-08-04 19:26:39 UTC+0000
... 0xfffffa801b112060:WmiPrvSE.exe                  2136    604     12    324 2018-08-04 19:26:51 UTC+0000
.. 0xfffffa801ad5ab30:spoolsv.exe                    1120    492     14    346 2018-08-04 19:26:22 UTC+0000
.. 0xfffffa801ac4db30:svchost.exe                     868    492     45   1114 2018-08-04 19:26:18 UTC+0000
.. 0xfffffa801a6e4b30:svchost.exe                    3196    492     14    352 2018-08-04 19:28:44 UTC+0000
.. 0xfffffa801acd37e0:svchost.exe                     620    492     19    415 2018-08-04 19:26:21 UTC+0000
.. 0xfffffa801b1e9b30:taskhost.exe                   2344    492      8    193 2018-08-04 19:26:57 UTC+0000
.. 0xfffffa801ac97060:svchost.exe                    1012    492     12    554 2018-08-04 19:26:20 UTC+0000
.. 0xfffffa801b3aab30:SearchIndexer.                 3064    492     11    610 2018-08-04 19:27:14 UTC+0000
.. 0xfffffa801aff3b30:msdtc.exe                      1436    492     14    155 2018-08-04 19:26:43 UTC+0000
. 0xfffffa801ab3f060:lsass.exe                        500    396      7    610 2018-08-04 19:26:12 UTC+0000
. 0xfffffa801ab461a0:lsm.exe                          508    396     10    148 2018-08-04 19:26:12 UTC+0000
 0xfffffa801a0c8380:csrss.exe                         348    336      9    563 2018-08-04 19:26:10 UTC+0000
. 0xfffffa801a6643d0:conhost.exe                     2420    348      0     30 2018-08-04 19:34:22 UTC+0000
 0xfffffa80198d3b30:csrss.exe                         388    380     11    460 2018-08-04 19:26:11 UTC+0000
 0xfffffa801aaf4060:winlogon.exe                      432    380      3    113 2018-08-04 19:26:11 UTC+0000
 0xfffffa801b18f060:WebCompanionIn                   3880   1484     15    522 2018-08-04 19:33:07 UTC+0000
. 0xfffffa801aa72b30:sc.exe                          3504   3880      0 ------ 2018-08-04 19:33:48 UTC+0000
. 0xfffffa801aeb6890:sc.exe                           452   3880      0 ------ 2018-08-04 19:33:48 UTC+0000
. 0xfffffa801a6268b0:WebCompanion.e                  3856   3880     15    386 2018-08-04 19:34:05 UTC+0000
. 0xfffffa801b08f060:sc.exe                          3208   3880      0 ------ 2018-08-04 19:33:47 UTC+0000
. 0xfffffa801ac01060:sc.exe                          2028   3880      0 ------ 2018-08-04 19:33:49 UTC+0000
 0xfffffa801b1fd960:notepad.exe                      3304   3132      2     79 2018-08-04 19:34:10 UTC+0000


```
The first thing that caught our attention is a process called Rick And Morty with pid 3820,its runed from the GUI (by doubleclicking).
This process is the parent of a more suspicious process called vm-tray.ex.
But wait! Vm-tray.exe or VMware Tray Process belongs to VMware Workstation by VMware. But here we have it runing from Rick and Morty not from VMware. INTERESTING!

- Dumping 'Rick And Morty' and 'Vm-tray.ex'

```shell
volatility -f OtterCTF.vmem --profile=Win7SP1x64 procdump -p 3820 --dump-dir=.
```
```shell
volatility -f OtterCTF.vmem --profile=Win7SP1x64 procdump -p 3720 --dump-dir=.
```
One way we can analyze these executables is by runing them in [Virustotal](https://www.virustotal.com/gui/home).

- Here are the results:

> vm-tray:

![vm-tary](https://i.postimg.cc/3NBtM2ZG/Screenshot-4.png)

> Rick And Morty:

![Rick And Morty](https://i.postimg.cc/wvq4TWbv/Screenshot-3.png)

#### From these results, we can say that our initial thoughts were right. These files are indeed malicious.

After a deeper look into these executables, we found out that vm-tray is actually a ransomware.

## HOW THE MACHINE GOT INFECTED

After finding the ransomware, lets find its path:

```shell
volatility -f OtterCTF.vmem --profile=Win7SP1x64 cmdline -p 3820
```
Here is our output:
```
Volatility Foundation Volatility Framework 2.6
************************************************************************
Rick And Morty pid:   3820
Command line : "C:\Torrents\Rick And Morty season 1 download.exe" 
```
```bash
volatility -f OtterCTF.vmem --profile=Win7SP1x64 filescan | grep -i "rick and morty"
```
```
Volatility Foundation Volatility Framework 2.6
0x000000007d63dbc0     10      0 R--r-d \Device\HarddiskVolume1\Torrents\Rick And Morty season 1 download.exe
0x000000007d6b3a10     11      1 R--rw- \Device\HarddiskVolume1\Torrents\Rick and Morty - Season 3 (2017) [1080p]\Rick.and.Morty.S03E07.The.Ricklantis.Mixup.1080p.Amazon.WEB-DL.x264-Rapta.mkv
0x000000007d7adb50     17      1 R--rw- \Device\HarddiskVolume1\Torrents\Rick and Morty - Season 3 (2017) [1080p]\Rick.and.Morty.S03E06.Rest.and.Ricklaxation.1080p.Amazon.WEB-DL.x264-Rapta.mkv
0x000000007d8813c0      2      0 RW-rwd \Device\HarddiskVolume1\Users\Rick\Downloads\Rick And Morty season 1 download.exe.torrent
0x000000007da56240      2      0 RW-rwd \Device\HarddiskVolume1\Torrents\Rick And Morty season 1 download.exe
0x000000007dae9350      2      0 RWD--- \Device\HarddiskVolume1\Users\Rick\AppData\Roaming\BitTorrent\Rick And Morty season 1 download.exe.1.torrent
0x000000007dcbf6f0      2      0 RW-rwd \Device\HarddiskVolume1\Users\Rick\AppData\Roaming\BitTorrent\Rick And Morty season 1 download.exe.1.torrent
```
Looking through the output, we find a torrent file called "Rick And Morty season 1 download.exe.torrent" thats interesting.
Our victim probably downloaded the torrent file thinking its the season 1 of rick and morty. 
Lets check the Chrome history for more infos. here is the path: ```\Users\Rick\AppData\Local\Google\Chrome\User Data\Default\History```

```
0x000000007d45dcc0     18      1 RW-rw- \Device\HarddiskVolume1\Users\Rick\AppData\Local\Google\Chrome\User Data\Default\History
```

Now, lets dump the file and view its content.

```shell
volatility -f OtterCTF.vmem --profile=Win7SP1x64 dumpfiles -Q 0x000000007d45dcc0 --dump-dir=lol
```

We can use sqlitebrowser to look through the chrome history.

```shell
sqlitebrowser file.None.0xfffffa801a5193d0.dat
```
![chrome history](https://i.postimg.cc/m2NQTJf0/Screenshot-2.png)

Now, we can deduce that the malware was recieved from an email in the format of a torrent file.

### THATS ALL FELLAS, HOPE YOU ENJOYED THE WRITEUP
