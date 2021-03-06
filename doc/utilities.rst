.. _utilities:

Utilities
=========


Overdue
-------

Checking for Overdue Backups from the Server
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

*Emborg* contains an additional executable, *emborg-overdue*, that can be run on 
the destination server to determine whether the backups have been performed 
recently.  It reads its own settings file in ~/.config/emborg/overdue.conf that 
is also a Python file and may contain the following settings::

    default_maintainer (email address -- mail is sent to this person upon failure)
    default_max_age (hours)
    dumper (email address -- mail is sent from this person)
    root (default directory for repositories)
    repositories (string or array of dicts)

Here is an example config file::

    default_maintainer = 'root@continuum.com'
    dumper = 'dumper@continuum.com'
    default_max_age = 12  # hours
    root = '/mnt/borg-backups/repositories'
    repositories = [
        dict(host='mercury (/)', path='mercury-root-root'),
        dict(host='venus (/)', path='venus-root-root'),
        dict(host='earth (/)', path='earth-root-root'),
        dict(host='mars (/)', path='mars-root-root'),
        dict(host='jupiter (/)', path='jupiter-root-root'),
        dict(host='saturn (/)', path='saturn-root-root'),
        dict(host='uranus (/)', path='uranus-root-root'),
        dict(host='neptune (/)', path='neptune-root-root'),
        dict(host='pluto (/)', path='pluto-root-root'),
    ]

The dictionaries in *repositories* can contain the following fields: *host*, 
*path*, *maintainer*, *max_age*. *host* is an arbitrary string that is used as 
description of the repository.  It is included in the email that is sent when 
problems occur to identify the backup and so should be unique.  It is a good 
idea for it to contain both the host name and the source directory being backed 
up.  *path* is either the archive name or a full absolute path to the archive.  
If *path* is an absolute path, it is used, otherwise it is added to the end of 
*root*.  *maintainer* is an email address, an email is sent to this address if 
there is an issue.  *max_age* is the number of hours that may pass before an 
archive is considered overdue.

*repositories* can also be specified as multi-line string::

    repositories = """
        # HOST      | NAME or PATH      | MAINTAINER           | MAXIMUM AGE (hours)
        mercury (/) | mercury-root-root |                      |
        venus (/)   | venus-root-root   |                      |
        earth (/)   | earth-root-root   |                      |
        mars (/)    | mars-root-root    |                      |
        jupiter (/) | jupiter-root-root |                      |
        saturn (/)  | saturn-root-root  |                      |
        uranus (/)  | uranus-root-root  |                      |
        neptune (/) | neptune-root-root |                      |
        pluto (/)   | pluto-root-root   |                      |
    """

If *repositories* is a string, it is first split on newlines, anything beyond 
a # is considered a comment and is ignored, and the finally the lines are split 
on '|' and the 4 values are expected to be given in order.  If the *maintainer* 
is not given, the *default_maintainer* is used. If *max_age* is not given, the 
*default_max_age* is used.

To run the program interactively, just make sure *emborg-overdue* has been 
installed and is on your path. Then type::

    emborg-overdue

It is also common to run *emborg-overdue* on a fixed schedule from cron. To do 
so, run::

    crontab -e

and add something like the following::

    34 5 * * * ~/.local/bin/emborg-overdue --mail > ~/.local/share/emborg/emborg-overdue.out 2>&

or::

    34 5 * * * ~/.local/bin/emborg-overdue --quiet --mail

to your crontab.

The first example runs emborg-overdue at 5:04 AM every day while saving the 
output into a file.  The use of the ``--mail`` option causes *emborg-overdue* to 
send mail to the maintainer when backups are found to be overdue.

The second example is similar except the output is suppressed rather than being 
saved to a file.


Checking for Overdue Backups from the Client
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

*emborg-overdue* can also be configured to run on the client.  This can be used 
when you do not control the server and so cannot run *emborg-overdue* there.  
The configuration is identical, except you give the path to the *lastbackup* 
file.  For example::

    default_maintainer = 'me@continuum.com'
    dumper = 'me@continuum.com'
    default_max_age = 12  # hours
    root = '~/.local/share/emborg'
    repositories = [
        dict(host='earth (cache)', path='cache.lastbackup', max_age=0.2),
        dict(host='earth (home)', path='home.lastbackup'),
    ]

Again, you can 

