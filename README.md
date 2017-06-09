# samba-tdb
An init.d script to automate the back up and restore of Samba .tdb files.

The idea was just to implement a fairly minor following of a best-practice recommendation. Inspired by https://serverfault.com/questions/19485/samba-server-implementation-details-and-best-practices.

Written for a Debian 8.7 (Jessie) environment, running a fairly simply-configured standalone-server instance of Samba (4.2.14-Debian at time of writing).

Once installed the script will execute with system startup (run-levels 2, 3, 4, and 5) and on shutdown (run-levels 0, 1, and 6).
On startup the script validates Samba's .tdb files, and if necessary will recover corrupted .tdb files from the most recent backup available. On shutdown, the current .tdb files are backed up and backup files older than two two weeks (configurable) are deleted.

Essentially it's just a nice, automated way of using the tdbbackup utility.

To install it:
(assuming your Debian\[whatever] and Samba are up and running)
- copy the samba-tdb script file to /etc/init.d/
- remember to set execute permissions: 
-- $ sudo chmod +x samba-tdb
- inform init.d of the new script so it runs on startup and shutdown:
-- $ sudo update-rc.d samba-tdb defaults
- done.

Usage:
- call it with a valid option (if invalid it will tell you): 
-- $ sudo /etc/init.d/samba-tdb OPTION
- valid options are:
-- start OR validate
--- The script will call "tdbbackup -v" to check your current .tdb files. If necessary a backup will be restored (if the file's corrupted, and if a backup is available).
-- stop OR backup
--- The script calls a plain "tdbbackup" to back up the current .tdb files with the default .bak suffix. It then copies the new .bak files and adds a ".YYYYMMDDHHMM" suffix so a history can be kept. Finally it checks for backups older than 14 days (configurable) and deletes them.

Bugs, Known Issues, and Limitations:
- The script does not deal with outright missing .tdb files. If a file used to be and now no longer is, this script will not pick that up for you. Hopefully this convenience will be added in future (maybe through a comparison of existing .bak files with the current .tdb files or something...). 

Credits:
- StackOverflow is life, love, etc.. Thank you to the kind folk who provided answers to these questions:
-- https://unix.stackexchange.com/questions/48973/execute-a-command-before-shutdown
-- https://stackoverflow.com/questions/4787413/rename-files-and-directories-add-prefix

- Also raspberrywebserver.com: 
-- http://raspberrywebserver.com/serveradmin/run-a-script-on-start-up.html

- The lovely online man-page resource of linux.die.net:
-- https://linux.die.net (specifically: https://linux.die.net/man/8/tdbbackup)

- Samba.org's documentation:
-- https://www.samba.org/samba/docs/man/Samba-HOWTO-Collection/tdb.html
-- https://www.samba.org/samba/docs/man/manpages-3/tdbbackup.8.html

- Special mention to the Debian Wiki:
-- https://wiki.debian.org/

- Finally, authored by me:
-- r4mzy [r4mzy.co.za]

---
... hopefully this is useful ;)
