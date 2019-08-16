![Bitfit logo, because, logo](https://raw.githubusercontent.com/joswr1ght/bitfit/master/bitfit.png)

# Bitfit
Recursively validate a starting directory of file contents to identify changes, corrupt data. Similar to md5deep/hashdeep, but simpler.

## Usage

```
$ ./bitfit.py
Bitfit 1.1.0
Usage: bitfit.py [OPTIONS] [STARTING DIRECTORY]
     - With no arguments, recursively calculate hashes for all files
-v   - Search for a VERSION verification file and validate hashes
-s   - Strict mode (with -v): Don't ignore validation of filesystem detritus
       (Strict mode is always enabled for hash file creation.)
-l   - Reduce memory consumption for hashing on low memory systems
-t   - Print timing and media speed information on STDERR

In verification mode, + indicates a file not present in the VERSION file, -
indicates a missing file in the directory tree, and ! indicates content
mismatch.
```

### Calculation Mode

Bitfit never writes to anything other than STDOUT/STDERR.  To produce a hash verification file for use by Bitfit, redirect the output to the root of the starting directory in a file called `VERSION-*.txt` (where * can be any version/date syntax you want).

```
$ bitfit /Volumes/STARTDIR
$ bitfit 1.0.0 output generated on 2015-05-19 14:58:13.747511 by jwright
$ bitfit /Volumes/STARTDIR
$ filename,MD5,SHA1
BinaryCookieReader.py,1cc05d0aa1c51c49e6ff934146d163a3,3743b6038cb464655ef747fa3bf913d5f5322598
class-dump-z,583bc57668d21788ff27b9b45c278738,d03d17fc9b03c9ea79f002ad186a16aa41e3f7eb
issh,3b0f9166e0d6f0737a2e057dce662ca5,46bd78c8fedad58ac8b2a61f2a8724bad7999590
josh,d41d8cd98f00b204e9800998ecf8427e,da39a3ee5e6b4b0d3255bfef95601890afd80709
$ bitfit /Volumes/STARTDIR >/Volumes/STARTDIR/VERSION-575.15.1.txt
```

### Verification Mode

Bitfit uses the "`-v`" argument to indicate that it should verify the contents of the directory tree.  Bitfit will identify the VERSION-*.txt file in the starting directory to use as the verification data source.

```
$ bitfit -v /Volumes/STARTDIR
Validation complete, no errors.
```

Bitfit differentiates three types of changes in the target directory tree:

+ `!` An exclamation mark indicates a hash mismatch between the VERSION file and the observed file
+ `+` A plus sign indicates that a file is present in the directory tree, but missing from the VERSION file
+ `-` A minus sign indicates that the file is missing from the directory tree, but recorded in the VERSION file

Here is an example of Bitfit output where changes were applied to the starting directory and detected:
```
$ touch /Volumes/SEC617/added-file
$ rm /Volumes/SEC617/sec617/eap.pcap
$ echo corrupt >/Volumes/SEC617/sec617/aircrack-ng.pcap
$ bitfit -v /Volumes/SEC617
-  sec617/eap.pcap
!  sec617/aircrack-ng.pcap
+  added-file
Validation failed.
```

In Verification Mode, testing is done in non-strict mode, in which common filesystem detritus files are ignored.  These files are created upon inserting the USB and mounting the filesystem in read-write mode.  By running in non-strict mode, a verification run can still be successful even if such detritus files are present.  Strict mode can be enforced during a verification run with the `-s` flag.

## Timing and media speed calculation

When the "`-t`" argument is supplied, Bitfit will perform rough volume-over-time calculation during the validation process.  This is not scientific testing of the media's speed, but a general means of calculating the validation process.

``` bitfit -t -v /Volumes/STARTDIR
Validation complete, no errors.
Took 69.32 seconds to hash 12852 MB (185.40 MB/sec)
```

## Platforms

Tested on Windows 8.1, OS X 10.10, and Debian-based Linux.  Windows binary included in the `bin/` directory, built with `C:\Python27\scripts\pyinstaller --onefile bitfit.py`.  

If you are running Bitfit from cmd.exe, you can create the VERSION file using standard redirection:
```
C:\Tools> bitfit.exe E:\ >E:\VERSION-575.15.1.txt
```

If you are running Bitfit from PowerShell (where everything is Unicode), you need to use the Out-Host cmdlet:
```
PS C:\Tools> bitfit.exe E:\ | Out-File -Encoding ascii VERSION-575.15.1.txt
```

## Questions, Comments, Concerns?

Open a ticket, or drop me a note: jwright@hasborg.com.

-Josh
