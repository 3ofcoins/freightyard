freightyard
===========

Package builder script for a freight repository.

[Freight](https://github.com/rcrowley/freight) is a program that
creates and maintains Debian apt-get repositories. This script works
with Freight to build updated packages and add them to repository. It
is intended for a continuous integration system to rebuild only the
packages that haven't been yet built and pushed to the apt repository.

Requirements
------------

 - Perl 5 interpreter (tested on 5.10.1)
 - Freight
 - Rake build tool for the packages
 - You probably also want to use GPG to sign your distribution

Usage
-----

1. Prepare config file for freight by running `freight init`.
   Configure the paths, GPG key, etc as you see fit. If you use the
   script in a CI setup, you probably don't want a password on your
   GPG keychain.

2. Set up your packages repository as described below.

3. Run `freightyard freight.conf apt/distro [apt/distro [...]]` to
   build packages that are not yet in repo and add them to the
   repository.

Package repository setup
------------------------

Every package that you want to build should live in a separate
subdirectory of your repository and be built using Rake (a Ruby build
tool). Currently only Rake is supported. Tasks that build packages
should be named as the package file and be properly described, so that
the tasks show in `rake -T` output. When you go to the packages
subdirectory, you should see something like this:

    [packages/java]$ rake -T
     [...]
    rake ia32-sun-java6-bin_6.32-1_amd64.deb  # Build ia32-sun-java6-bin_6.32-1_amd64.deb
    rake sun-java6-bin_6.32-1_amd64.deb       # Build sun-java6-bin_6.32-1_amd64.deb
    rake sun-java6-fonts_6.32-1_all.deb       # Build sun-java6-fonts_6.32-1_all.deb
    rake sun-java6-javadb_6.32-1_all.deb      # Build sun-java6-javadb_6.32-1_all.deb
    rake sun-java6-jdk_6.32-1_amd64.deb       # Build sun-java6-jdk_6.32-1_amd64.deb
    rake sun-java6-jre_6.32-1_all.deb         # Build sun-java6-jre_6.32-1_all.deb
    rake sun-java6-plugin_6.32-1_amd64.deb    # Build sun-java6-plugin_6.32-1_amd64.deb
    rake sun-java6-source_6.32-1_all.deb      # Build sun-java6-source_6.32-1_all.deb
    [packages/jetty]$ rake -T
     [...]
    rake jetty_8.1.3.v20120416-local5_all.deb  # Build package.

There can be different tasks in the descriptions.  Freightyard will
look for `*/Rakefile` in current directory, run `rake -T` in every
subdirectory that contains Rakefile, collect all the tasks that end
with `.deb`, and use these tasks' names as packages available for
building.

This list will be compared with contents of the freight repository,
and all the available packages that are not in the freight repository
will be built and added there.

TODO
----

 - Make the script more configurable - not Rake-dependent.
 - Support for maintaining various packages
