---
title: Custom Gentoo Overlays - The Stupid Simple Guide
author: Stephen Demos
published: January 16, 2016
---

Gentoo makes it really easy for anybody to install custom packages using
Portage. However, they (obviously) keep the official Portage tree under tight
controls. So, how do you add custom packages to the list of packages that
Portage knows about? The solution is overlays!

There is a huge variety of overlays that you can add. Some of these have
patched version of packages in the main Portage tree, some of them have updated
version, and some have packages that aren't there at all. A large chunk of
these overlays are hosted at
[overlays.gentoo.org](https://overlays.gentoo.org), although there are plenty
of custom overlays that people just keep in git repositories on Github or
elsewhere. Adding all of these overlays is easily managed using a tool called
[Layman](https://wiki.gentoo.org/wiki/Layman).

But if one of these many overlays doesn't have the package that you want to
install, or you don't want to add an overlay that has a bunch of extraneous
packages when you only want one, you can create your own custom overlay that
has exactly what you want.

An overlay is a git repository that is hosted publicly somewhere, with a
directory structure that reflects the package categories. Within the directory
for each package has the ebuild for that package and a manifest containing
digests and file size information for all of the files in the directory to
verify file integrity.

The overlay also contains a few files that contain information about the
overlay itself that you will need to write.

For example, if I was packaging the Simple OpenGL Image
Library, or SOIL, I would have a directory structure like this - 

```
.
├── media-libs
│   └── soil
│       ├── Manifest
│       └── soil-20080707.ebuild
├── metadata
│   └── layout.conf
├── overlay.xml
└── profiles
    └── repo_name
```

If you want more information on how to write ebuild files, [Gentoo's Developer
Guide](https://devmanual.gentoo.org/ebuild-writing/index.html) naturally has a
very detailed guide. They also have a page on [how to generate Manifest
files](https://wiki.gentoo.org/wiki/Repository_format/package/Manifest).

The inside the `metadata` folder, we have the `layout.conf` file, which for our
little overlay simply looks like this - 

```
masters = gentoo
```

This line says that our overlay requires the Gentoo repository. You could also
require other overlays be installed before this one, if your package requires
it.

Inside the `profiles` folder, we have the `repo_name` file. This very simple
file is pretty self-explanatory, it just contains the name of the overlay on
one line.

The last file to go over is arguably the one that makes this repository into an
overlay. This xml file, commonly called `overlay.xml` or `repositories.xml`,
contains the information about the overlay necessary for layman to
automatically add it to your personal Portage tree. This is the file that you
tell layman about when adding it, and it takes care of the rest.

An example of the xml file for our overlay is

```xml
<?xml version="1.0"?>

<repositories version="1.0">
    <repo priority="50" quality="experimental" status="unofficial">
        <name>sdemos-overlay</name>
        <description>sdemos's personal gentoo overlay</description>
        <homepage>https://github.com/sdemos/sdemos-overlay</homepage>
        <owner>
            <name>Stephen Demos</name>
            <email>stphndemos@gmail.com</email>
        </owner>
        <source type="git">https://github.com/sdemos/sdemos-overlay.git</source>
    </repo>
</repositories>
```

All that needs to be done is to replace the info in this example with the
information for your overlay, like it's location, name, and a description. Then
you can host this somewhere, like Github.

The last step is to add the overlay to your Portage tree. This can be done with

```
layman -o https://url.of/overlay.xml -f -a overlay-name
```

This command is described in more detail on the [Layman Gentoo wiki
page](https://wiki.gentoo.org/wiki/Layman) and `man layman`.

A good reference and some more detail on the components of the repository
structure for Gentoo (sans `overlay.xml`) can be seen
[here](https://wiki.gentoo.org/wiki/Repository_format).

Disclaimer: I am nowhere near an expert in Gentoo packaging, I just had a hard
time finding resources for making my own custom Gentoo overlay, and so I
consolidated all of the information I found here.
