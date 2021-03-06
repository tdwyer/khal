About
=====
*Khal* is a CLI (console), CalDAV_ based calendar program, `allowing syncing of
calendars with a variety of other programs on a host of different platforms`__.

*khal* is currently in a very early stage of development, has a very limited
feature set and is probably full of bugs. If you still want to take it out for a
spin, please know, that as long as you don't enable write support, there is no
chance at all that you might mess up your remote calendar. If you do try it out,
please report back any bugs you might encounter.

.. image:: http://lostpackets.de/images/khal.png

Features
--------
(or rather: limitations)

- khal can sync events from CalDAV calendar collections
- add simple new events to a calendar and upload them
- ikhal (interactive khal) can show and edit events in the current and next two months
- simple recurring events support (no exceptions just yet)
- you cannot edit the timezones of events
- is pretty Euro centric, weeks start on Mondays, khal uses a 24 hour clock and
  the default time zone is 'Europe/Berlin' (apart from the week start issue, it
  is all configurable now)
- khal should run on all major
  operating systems [1]_ (has been tested on FreeBSD and Debian GNU/Linux)


.. [1] except for Microsoft Windows

Feedback
--------
Please do provide feedback if *khal* works for you or even more importantly
if it doesn't. You can reach me by email at khal (at) lostpackets (dot) de
, by jabber/XMPP at geier (at) jabber (dot) ccc (dot) de or via github

.. __: http://en.wikipedia.org/wiki/Comparison_of_CalDAV_and_CardDAV_implementations
.. _CalDAV: http://en.wikipedia.org/wiki/CalDAV

Installation
------------
You can install *khal* from *pypi* via *pip install khal* or install it from
source by executing *python setup.py install*. 

Copy and edit the supplied khal.conf.sample file (default location is
~/.config/khal/khal.conf). If you don't want to store the password in clear
text in the config file, pyCardDAV will ask for it while syncing (and store it
in a keychain if keychain_ is installed).

Make sure you have sqlite3 (normally available by default), icalendar_, lxml(>2),
requests (>0.10), urwid (>0.9) and pyxdg installed. Users of python 2.6 will also
need to install argparse.

khal has so far been successfully tested on recent versions of FreeBSD,
NetBSD, Debian and Ubuntu with python 2.6 and 2.7 against davical, owncloud
and fruux.

.. _keychain: https://pypi.python.org/pypi/keyring
.. _icalendar: https://github.com/collective/icalendar

Usage
-----
**install**

 python setup.py install

**configure**

copy *khal.conf.sample* to ~/.khal/khal.conf or ~/.config/khal/khal.conf and
edit to your liking

**syncing**

 khal --sync

syncs all events in the last month and next 365 days


**basic usage**

 khal

will show all events today and tomorrow

 ikhal

opens and interactive calendar browser, showing all events on the selected day


**quick event adding**

  khal --new 18:00 Awesome Event

adds a new event starting today at 18:00 with summary 'awesome event' (lasting
for the default time of one hour, will be configurable soon) to the default
calendar

  khal --new 25.10. 16:00 18:00 Another Event

adds a new event on 25th of October lasting from 16:00 to 18:00


  khal --new 26.07. Great Event

adds a new all day event on 26.07.

khal --new should be able to understand quite a range of dates, have a look at
the tests for more examples.

In a more abstract form:

  khal --new startdatetime [enddatetime] description

where stard- and enddatetime are either datetimes or times in the format defined
in the config file. Start- and enddatetime can be one of the following:

  * datetime datetime
      start and end datetime specified, if no year is given, this year
      is used, if the second datetime has no year, the same year as for
      the first datetime object will be used, unless that would make
      the event end before it begins, in which case the next year is
      used
  * datetime time
      end date will be same as start date, unless that would make the
      event end before it has started, then the next day is used as
      end date
  * datetime
      event will last for defaulttime
  * time time
      event starting today at the first time and ending today at the
      second time, unless that would make the event end before it has
      started, then the next day is used as end date
  * time
      event starting today at time, lasting for the default length
  * date date
      all day event starting on the first and ending on the last event
  * date
      all day event starting at given date and lasting for default length

At the moment default length is either 1h or 1 day (should be configurable soon,
too).


Write Support
-------------
To enable uploading events on the server, you need to enable write support.
Please note, that write support is experimental and please make sure you either
*really do have a backup* or only use it on test calendars.

To enable write support you need to put 

 write_support: YesPleaseIDoHaveABackupOfMyData

into every *Account* section you want to enable write support on in your config
file.


Notes on Timezones
-------------------
Getting localized time right, seems to be the most difficult part about
calendaring (and messing it up ends in missing the one imported meeting of the
week). So I'll briefly describe here, how khal tries to handle timezone
information, which information it can handle and wich it can't.

All datetimes are saved to the local database as UTC Time. Datetimes that are
already UTC Time, e.g. '19980119T070000Z' are saved as such. Datetimes in local
time and with a time zone reference that khal can understand (Olson database) are
converted to UTC and than saved, e.g. 'TZID=America/New_York:19980119T020000'.
Floating times, e.g. '19980118T230000' (datetimes which are neither UTC nor have a
timezone specified) are treated as if the *default timezone* (specified in
khal's config file) was specified. Datetimes with a specified timezone that
khal does not understand are treated as if they were floating time.

khal expects you want *all* start and end dates displayed in *local time* (which
can be configured in the config file).

*VTIMEZONE* components of calendars are totally ignored at the moment, as are
daylight saving times.

To summarize: as long as it is not daylight saving time, you are always in the
same timezone and your calendar is, too, khal probably shows the right start and
end times. Otherwise: Good Luck!

Seriously: be careful when changing timezones and do check if khal shows the
correct times anyway (and please report back if it doesn't).


Miscellaneous
-------------
*khal* is written in python using among others requests_, lxml_, icalendar_,
dateutil_ and pysqlite_. *khal* is open source and free software, released under
the Expat/MIT license.

.. _lxml: http://lxml.de/
.. _pysqlite: http://code.google.com/p/pysqlite/
.. _requests: http://python-requests.org
.. _icalendar: https://github.com/collective/icalendar
.. _dateutil: http://labix.org/python-dateutil



License
-------
pyCardDAV is released under the Expat/MIT License:

Copyright (c) 2013 Christian Geier and contributors

Permission is hereby granted, free of charge, to any person obtaining a copy of
this software and associated documentation files (the "Software"), to deal in
the Software without restriction, including without limitation the rights to
use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of
the Software, and to permit persons to whom the Software is furnished to do so,
subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS
FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR
COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER
IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN
CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

