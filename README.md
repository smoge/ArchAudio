ArchAudio
==========

This project contains a tree of ArchLinux PKGBUILDs that's used to compile a
binary repository of audio and music related packages. If you'd like to use that repository,
then append the following lines at the end of  `/etc/pacman.conf` (replacing $arch with i686 or x86_64):

      [archaudio-stable]
      Server = http://repos.archaudio.org/stable/$arch
 
      [archaudio-testing]
      Server = http://repos.archaudio.org/testing/$arch
 
      [archaudio-experimental]
      Server = http://repos.archaudio.org/experimental/$arch


The ArchAudio project
=====================

Our main goal is to maintain a binary repository of up-to-date packages for professional use on ArchLinux systems. 

PKGBUILDs
----------

You can download our buildscripts from the development tree by running:


      svn co https://svn.archaudio.org/trunk/buildscripts archaudio-sources


There is also a git mirror hosted here:

      git clone git://github.com/smoge/ArchAudio.git archaudio-sources
    

These are working copies of ongoing development, so the scripts may not reflect the contents of existing binary packages.


Packages
==========

Suggestions for adding individual packages to the set are always welcome. Also warning us about a package that needs update. Just send the request to the general and development discussion mailinglist. 

Please register for our mailing list at http://archaudio.org.

