What
====
This is a maintenance script to ensure that your Hurricane Electric Tunnel Broker IPv6 tunnel is always listening for connections from your current external IPv4 address.  

This script is helpful if you have a dynamic IP address from your ISP.  The script is also logical and does not needlessly update the IPv6 address via HE's API.

This was written and tested with **Debian Squeeze (6.0)**, but it may work on other Debian-derived distributions.  It was also written with the assumption that your tunnel is configured in a similar fashion as the way it is explained in my blog post:

- [The Blog of Tim Heckman: Hurricane Electric Tunnel Broker IPv6 Gateway](http://blog.timheckman.net/2011/05/24/he-tunnelbroker-ipv6-gateway/ "http://blog.timheckman.net/2011/05/24/he-tunnelbroker-ipv6-gateway/")

Set up
======

To get this script up and running you will need to install some modules from the [Comprehensive Perl Archive Network (CPAN)](http://www.cpan.org/ "http://www.cpan.org/"):

* [YAML::Tiny](http://search.cpan.org/~adamk/YAML-Tiny-1.50/lib/YAML/Tiny.pm "http://search.cpan.org/~adamk/YAML-Tiny-1.50/lib/YAML/Tiny.pm")
* [LWP::Protocol::https](http://search.cpan.org/~gaas/LWP-Protocol-https-6.02/lib/LWP/Protocol/https.pm "http://search.cpan.org/~gaas/LWP-Protocol-https-6.02/lib/LWP/Protocol/https.pm")
* [WWW::Mechanize](http://search.cpan.org/~jesse/WWW-Mechanize-1.71/lib/WWW/Mechanize.pm "http://search.cpan.org/~jesse/WWW-Mechanize-1.71/lib/WWW/Mechanize.pm")
* [Logger::Syslog](http://search.cpan.org/~sukria/Logger-Syslog-1.1/lib/Logger/Syslog.pm "http://search.cpan.org/~sukria/Logger-Syslog-1.1/lib/Logger/Syslog.pm")

If you have [App::cpanminus (cpanm)](http://search.cpan.org/~miyagawa/App-cpanminus-1.5007/lib/App/cpanminus.pm "http://search.cpan.org/~miyagawa/App-cpanminus-1.5007/lib/App/cpanminus.pm") installed you can install the needed modules with this command: 

```cpanm YAML::Tiny LWP::Protocol::https WWW::Mechanize Logger::Syslog```

At this point you only need to download the script and schedule it to run with cron.

1. Download the script and save it to /usr/local/sbin/he-ipv4.pl using either curl or wget:
 * ```curl -o /usr/local/sbin/he-ipv4.pl "https://raw.github.com/theckman/he-ipv4-perl/master/he-ipv4.pl"```
 * ```wget -O /usr/local/sbin/he-ipv4.pl "https://raw.github.com/theckman/he-ipv4-perl/master/he-ipv4.pl"```
2. Make the file executable:
 * ```chmod +x /usr/local/sbin/he-ipv4.pl```
3. Edit the file using your preferred text editor.  Make sure all configuration items are set properly.
4. Edit the root user's crontab (```crontab -e```) to run this script post reboots and every 15 minutes:

> ```@reboot /usr/sbin/he-ipv4.pl >/dev/null 2>&1```
> 
> ```*/15 * * * * /usr/sbin/he-ipv4.pl >/dev/null 2>&1```

Configuration
=============

The configuration is all done within the script file itself.  There is a configuration section near the top of the file.

* **$userID**: this is your UserID value from the Main Page of HE's Tunnel Broker
* **$userPass**: an MD5 hash of your Tunnel Broker password:
 * ```echo -n YourPassword | md5sum```
* **$tunnelID**: the unique tunnel ID number from your tunnel's information page
* **$tunnelName**: this is the *local* name for the Tunnel Broker interface.  In my blog I use the name "he-ipv6"
* **@listURL**: this is a list of the URLs that will be used to obtain the external IP address.  This should not, in most cases, need to be touched