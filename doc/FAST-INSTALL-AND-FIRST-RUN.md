This file is part of IVRE.

Copyright 2011 - 2014 [Pierre LALET](mailto:pierre.lalet@cea.fr)

This file describes the steps to install IVRE, run the first scans and
add the results to the database with all components (scanner, web
server, database server) on the same (Debian or Ubuntu) machine.

Adapt it to your needs, architecture, etc.


# Install #

    $ sudo apt-get -y install mongodb python-pymongo apache2 dokuwiki
    $ git clone https://github.com/cea-sec/ivre
    $ cd ivre
    $ python setup.py build
    $ sudo python setup.py install


# Setup #

    $ sudo -s
    # cd /var/www/html ## or depending on your version /var/www
    # ln -s /usr/local/share/ivre/web/static/* .
    # cd /usr/lib/cgi-bin
    # ln -s /usr/local/share/ivre/web/cgi-bin/scanjson.py
    # cd /var/lib/dokuwiki/data/pages
    # ln -s /usr/local/share/ivre/dokuwiki/doc
    # cd /var/lib/dokuwiki/data/media/wiki
    # ln -s /usr/local/share/ivre/dokuwiki/media/logo.png
    # cd /usr/share/dokuwiki
    # patch -p0 < /usr/local/share/ivre/dokuwiki/backlinks.patch
    # cd /etc/apache2/mods-enabled
    # for m in cgi rewrite ; do [ -L $m.load ] || ln -s ../mods-available/$m.load ; done
    # cd /usr/local/share/ivre/web/cgi-bin
    # cp scanjsonconfig-sample.py scanjsonconfig.py
    # sed -i 's/^\(\s*\)#Rewrite/\1Rewrite/' /etc/dokuwiki/apache.conf
    # service apache2 reload
    # exit

Open a web browser and visit [http://localhost/](http://localhost/).
IVRE Web UI should show up, with no result of course. Click the HELP
button to check if everything works.


# Database init, data download & importation #

    $ scancli --init
    This will remove any scan result in your database. Process ? [y/N] y
    $ ipinfo --init
    This will remove any passive information in your database. Process ? [y/N] y
    $ ipdata --init
    This will remove any country/AS information in your database. Process ? [y/N] y
    $ sudo ipdata --download
    $ ipdata --import-all

The two latest steps take a long time to run, nothing to worry about.


# Run a first scan #

Against 1k (routable) IP addresses, with a single nmap process:

    $ sudo runscans --routable --limit 1000

Go have some coffees and/or beers (remember that according to the
traveler's theorem, for any time of the day, there exists a time zone
in which it is OK to drink).

When the command has terminated, import the results:

    $ nmap2db -c ROUTABLE,ROUTABLE-CAMPAIGN-001 -s MySource -r \
    >         scans/ROUTABLE/up

The `-c` argument adds categories to the scan results. Categories are
arbitrary names used to filter results. In this example, the values
are `ROUTABLE`, meaning the results came out while scanning the entire
reachable address space (as opposed to while scanning a specific
network, AS or country, for example), and `ROUTABLE-CAMPAIGN-001`,
which is the name I have chosen to mark this particular scan campaign.

The `-s` argument adds a name for the source of the scan. Here again,
it is an arbitrary name you can use to unambiguously specify the
network access used to run the scan. This can be used later to
highlight result differences depending on where the scans are run
from.

Go back to the Web UI and browse your first scan results!


# Some remarks #

There is no tool (for now) to automatically import scan results to the
database. It is your job to do so, according to your settings.

If you run very large scans (particularly against random hosts on the
Internet), do NOT use the default `--output=XML` option. Rather, go
for the `--output=XMLFork`. This will fork one nmap process per IP to
scan, and is (sadly) much more reliable.

Another way to run scans efficiently is to use an [agent](AGENT.md)
and the `runscans-agent` command.