
# nfs: Allegro NFS Server for Microsoft Windows in Common Lisp

# Table of contents

 * Description
 * Author
 * Author comments
 * Documentation
 * Platforms
 * Dependencies
 * Installation
 * [Configuration](#configuration)
 * Licence
 * Notes
 * Franz Inc. Open Source Info
 * Release Notes

# Description

Allegro NFS Server for Microsoft Windows in Common Lisp

# Authors

Ahmon Dancy, Franz Inc.
Elliott Johnson, Franz Inc.
Kevin Layer, Franz Inc.

# Author comments

Allegro® NFS Server for Windows® was inspired by our dissatisfaction
with current free and commercial NFS Servers available on the market
and the incredible technical difficulties we faced in configuring them
on Windows.

# Platforms

Microsoft Windows XP and newer (including Windows 8).

# Dependencies

Cygwin (GNU make).  Allegro Common Lisp 9.0 (32-bit, non-SMP).

# Installation

To build:

    make all

To install:

    make install

The install step expects that cygwin has C:\ mounted as /c.  It is
also possible to build an installer via:

    make installer

This will produce an _nsi_ file that can be used to install.

# Configuration

See the [nfs documentation](http://www.nfsforwindows.com/home) for
more information on how to configure nfs.

# Documentation

Once your exports are configured it's possible to mount them.  Please
consult your platforms documentation on how to mount remote nfs partitions.

# Debugging

Interactive debugging of server:

    :ld load.cl

Then, for debugging:

    (debugmain) ;; main.cl

Or, without debugging:

    (setf *configfile* "nfs.cfg")
    (read-nfs-cfg *configfile*)
    (startem)

*******************************************************************************

    :cd d:/src/nfs50/
    (load "load.cl")
    (setf *configfile* "nfs.cfg")
    (read-nfs-cfg *configfile*)
    (startem)

    (prof:start-profiler)

    (prof:stop-profiler)
    (defun doit (file)
       (with-open-file (*standard-output* file :direction :output
			:if-exists :supersede)
	 (prof:show-flat-profile)
	 (prof:show-call-graph)
	 #+ignore (prof:disassemble-profile 'excl::g-read-vector-2)))
    (doit "y:/nfs.82brc5")
    (doit "y:/nfs.81")

    (prof:show-flat-profile)
    (prof:show-call-graph)

# License

The nfs source code is licensed under the terms of the 
[Lisp Lesser GNU Public License](http://opensource.franz.com/preamble.html), 
known as the LLGPL. The LLGPL consists of a preamble and the LGPL. Where these 
conflict, the preamble takes precedence.  This project is referenced in the 
preamble as the LIBRARY.

# Notes

See the following files that are part of this project:

 * access-control.txt - info on controlling access to the nfs server
 * notes.txt - implementation notes
 * release-notes.txt - info about past and present nfs releases.
 * rfc1014.txt - XDR: External Data Representation Standard.
 * rfc1050.txt - rpc: Remote Procedure Call.
 * rfc1094.txt - NFS: Network File System Protocol Specification
 * rfc1813.txt - nfs version 3 protocol standard.
 * rfc1833.txt - Binding Protocols for ONC RPC Version 2
 * TODO.txt - old and new todo information.

# Franz Open Source Info

This project's homepage is <https://github.com/franzinc/nfs/>.

# Release Notes by version

## UNRELEASED CHANGES

Note, the changes in this section will appear in the upcoming version
6.0 release schedule for summer 2014.

* `user::*executable-types*` now in config file, allows FAQ to be
  written for experienced users to modify it

* Added support for persistent file handles for files on NTFS
  volumes. Non-NTFS files (or inaccessible files on NTFS volumes)
  still use non-persistent file handles.

* Allow control over how long file attribute are cached through the
  configuration GUI.

* Fixed some input validation bugs in configure program.

* Fix mounting a subdirectory of an export.

* File and directory changes on the server weren't see by some
  clients.

* Configuration for "Host lists" allows host names in the "New
  address" field.  When loading the configuration and resolving the
  host names, users will be warned if the conversion of saved host
  names fails, and the host name will be ignored.

* Some previously invisible files are now seen by clients, such as
  in the roots of filesystems, "System Volume Information" and
  "pagefile.sys".

* File attributes are cached for a max of 5 seconds. Prior to this
  change, the expiration for cached file attributes would be extended
  each time they were accessed.  This could be a problem in the
  following scenario:

  * an NFS client is repeatedly calling stat() on a file to see if its
    attributes had changed, and

  * the attributes of the file were altered outside of Allegro NFS's
    knowledge.

  In this case the new file attributes would never be returned... at
  least not until the client stopped probing for a sufficiently long
  time.

* Use higher precision timestamps in log messages, enabled via an
  option on the debug tab.

* Change default statfs blocksize from 8192 to 512.  This improves
  compatibility with some broken NFS clients which do not work
  properly if the blocksize is not 512.

* Minor change to showmount output.

## Version 5.1, 1 Aug, 2011

* performance improvements
* minor license changes
* new: toggling of response to showmount requests.
* new: configurable log rotation.  Defaults to previous behavior.
* new: directory and file caching duration is now configurable.
* fix: interoperability fixes with VMware ESXi.
* fix: hanging of the server and 100% cpu usage problems are removed.
* fix: moving the error log into the install directory.
* fix: proper locking around exports and logging operations.
* small modifications to icons.

## Version 5.0, 22 Feb, 2010

* performance improvements
* better support Windows Vista/7
* new: support for symbolic links
* new: support for international filenames (UTF-8)
* new: support for mount protocol version 2
* new console separate from service, that is shows log info
* added NLM and NSM debug options
* add date to timestamp logging
* New option `*nfs-set-mtime-on-write*` allows users to work around
  Windows' funny behavior with respect to file modification times.
  Normally Windows does not update the mtime on a file until the file
  is closed.  Since Allegro NFS keeps a file open while it is
  actively being written-to, a Windows program periodically checking
  the mtime of the file will not be able to tell that the file is
  actually being modified until the file is closed.  Updating the
  mtime during every write access suffers about a 10% penality so the
  option is disabled by default.
* improvements to hard link support
* better compatibility between UNIX and Windows-style locking
* improved interoperatibility with several UNIX clients
* many other small improvements and fixes

## Version 4.5, 20 June, 2007

* Windows Vista Support
* Many interoperability fixes and bug fixes
* Improved locking support
* NLM and NSM port numbers can now be set manually

## Version 4.4, 29 Aug, 2006

* NFS lock support
* Bug fixes
* Enhanced debugging

## Version 4.3, 27 Oct, 2005

* Installs on systems with Windows Data Execution Prevention (DEP) turned on
* allow specification of mountd port number for dealing with firewall issues
* made mountd subprocess more robust in the face of malformed RPC messages
* adjusted the logging of some "normal" error conditions so they look
  less alarming
* better performance
* bug fixes 

## Version 4.0, 10 Aug, 2005

* NFS protocol V3 support
* large file support
* better performance 
* Hard link creation support

## Version 3.0, 22 Apr, 2004

* handle UNC pathnames
* added "use system portmapper" option 
* improved performance

## Version 2.0, 24 Feb, 2004

* new configuration utility 

## Version 1.1.4, 3 Jul, 2003

* improved interoperability with some clients
* configuration option for altering mode bits 

## Version 1.1.3, 21 Mar, 2003

* bug fixes 

## Version 1.1.1, 23 Jan, 2003

* Improvements of handling the NFS services 

## Version 1.0.36, 23 Sep, 2002

* bug fixes 

## Version 1.0.33, 27 Feb, 2002

* initial release 
