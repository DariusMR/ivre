This file is part of IVRE.

Copyright 2011 - 2014 [Pierre LALET](mailto:pierre.lalet@cea.fr)

# Introduction #

IVRE (Instrument de veille sur les réseaux extérieurs) or DRUNK
(Dynamic Recon of UNKnown networks) is a network recon framework,
including two modules for passive recon (one p0f-base and one
bro-based) and one module for active recon (mostly nmap-based, with a
bit of zmap).

This web interface presents results of (Nmap) active scans that can be
filtered with keywords (and, for some of them, shortcuts are available
in the menus).

Keep in mind that the information available in this interface highly
depends on the options used to run Nmap.

# The interface #

## The top navigation bar ##

It contains several elements; from left to right:

  - A shortcut to the start page, that cleans every keyword.
  - A button to display this help page.
  - Some menus with shortcuts to add filtering, sort or display
    commands.
  - Some links to "share" (export) the current page.

## The left side bar ##

The first part allows to navigate within the results. Be careful with
the last button that goes to the last result page, as it can be very
slow when a lot of results are available.

The progress bar shows where the currently displayed results are
within the whole results set.

The second part allows to add, modify or remove filter, sort or
display commands.

The second part allows to explore the results by generating graphs
displayed in the rightest part of the screen.

  - The first field displays a graph with the 15 most common values of
    a variable in the filtered results. This can be slow when the
    number of results to scan is important. Here is a list of
    (sometimes) interesting values to try here:
    - `category`, `source`
    - `country`, `city`, `asnum`
    - `domains`, `domains:[level]`
    - `hop`, `hop:[number]`
    - `port`, `port:[open/closed/filtered]`,
      `portlist:[open/closed/filtered]`
    - `service`, `service:[port]`, `probedservice`,
      `probedservice:[port]`, `product`, `product:[port]`
    - `devicetype`, `devicetype:[port]`
    - `ports.scripts.id`, `scripts.id`
    - `script:[scriptname]`, `portscript:[scriptname]` (like `port:`),
      `hostscript:[scriptname]`
    - `smb.os`, `smb.lanmanager`, `smb.domain`, `smb.dnsdomain`,
      `smb.forest`, `smb.workgroup`
    - `cert.issuer`, `cert.subject`
    - `modbus.deviceid`, `enip.vendor`, `enip.product`, `enip.serial`,
      `enip.devtype`, `enip.prodcode`, `enip.rev`, `enip.ip`
  - The *Address space* button displays a graphical representation of
    the filtered addresses. The abscissa axis represents the two high
    bytes (or the three when the results belong to the same /16
    network), and the ordinate axis represents the two low bytes (or
    the low byte).
  - The *Map* button displays the locations of the results on a world
    map.
  - The *Timeline* and *Timeline 24h* buttons display time-lines where
    the abscissa axis represents the time and the ordinate axis
    represents the IP addresses.

## Scan results ##

Ten results (maximum) are displayed per page by default.

Each result has its own frame. In the default display mode, it
displays a summary for the host. Long-clicking a result frame toggles
between the summary display and the full display for the result.

The pencil icon in the upper-right corner opens the notepad page for
the current host (see below) in the rightest part of the screen.

Each blue element in the results can be clicked to add a filter.

# Available commands #

## Command specification ##

The commands might require a parameter, provided after the colon sign
`:`. Some commands can be used negatively, by prefixing them with `!`
or `-`.

The commands can be entered in the input boxes in the second part of
the left side bar or added by clicking on a shortcut in the top bar
menus.

In the following list, a `[!]` before the command shows it can be used
negatively, and a `:` after the command indicates it requires a
parameter.

When a parameter is required the full value must be specified, or when
appropriate, a regular expression can be used, with the
`/[expression]/[flags]` syntax (e.g.:
`hostscript:smb-enum-shares:/WRITE/`).

If your command includes spaces, you need to protect it by using
single or double quotes.

## Commands list ##

### Filters ###

  - `[!]archives` show results from the archives database (and
    `!archives` has no effect since it is the default behavior).
  - `[!]host:[IP address]` filter a specific IP address. Using the IP
    address directly (without `host:`) is equivalent.
  - `[!]net:[IP address/netmask]` filter a specific network (CIDR
    notation). Using the CIDR notation directly (without net:`) is
    equivalent.
  - `[!]category:` filter a category.
  - `[!]country:[two letters code]` filter a country.
  - `[!]city:` filter a city (use with `country:`).
  - `[!]source:` filter a source (specify the source name).
  - `[!]timeago:` filter recent enough results; the value can be
    specified in seconds or with the appropriate suffix in minutes
    (`m`), hours (`h`), days (`d`) or years (`y`).
  - `service:[expression]`, `service:[expression]:[port number] look
    for an expression in the name, the version, the name of the
    product,... of a service.
  - `banner:` look for a specific banner of a service.
  - `script:[scriptid]`, `script:[scriptid]:[output]` look for a
    specific (port) script.
  - `hostscript:[scriptid]`, `hostscript:[scriptid]:[output]` look for
    a specific host script.
  - `os:` look for a specific value in the OS discovery results.
  - `anonftp` filter results with anonymous FTP allowed.
  - `authhttp` look for HTTP servers with authentication and a default
    (e.g., `admin`/`admin`) login/password working. The Nmap script
    seems to get a lot a false positives.
  - `nfs` look for NFS servers.
  - `nis`, `yp` look for NIS servers.
  - `mssqlemptypwd` look for MS-SQL servers with an empty password for
    the `sa` account.
  - `x11` look for X11 servers.
  - `x11open` look for open X11 servers.
  - `anonldap` look for LDAP servers with anonymous bind working.
  - `xp445` look for Windows XP machines with TCP/445 port open.
  - `sshkey:` look for a particular SSH key.
  - `file:` look for a pattern in the shared files (FTP, SMB, ...).
  - `webfiles` look for "typical" web files in the shared folders.
  - `httptitle:` look for a specific HTML title value of the homepage
    of a web site.
  - `owa` look for OWA (Outlook Web App) servers.
  - `phpmyadmin` look for phpMyAdmin servers.
  - `devtype:`, `devicetype:` look for a type of devices.
  - `netdev`, `networkdevice` look for network devices (firewalls,
    routers, ...).
  - `phonedev` look for telephony devices.
  - `geovision` look for GeoVision web-cams.
  - `torcert` look for Tor certificates.
  - `[!]hop:` look for a particular IP address in the traceroute
    results.
  - `[!]tcp/[port number]`, `[!]udp/[port number]`, `[!][port number]`
    look for an open TCP or UDP port.
  - `openport` look for hosts with at least one open port.
  - `notes` search results with an associated note.

### Sort ###

  - `skip:[count]` skip `count` first results.
  - `limit:[count]` only display `count` results.
  - `[!]sortby:[field name]` sort according to a field value. Be
    careful with this setting as consequences on the performances can
    be terrible.

### Display ###

  - `display:host` set the default display mode.
  - `display:script:[scriptid]` only display a particular script
    output.