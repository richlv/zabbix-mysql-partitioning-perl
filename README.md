## Important notes/bugs: 

#1 The script is FIXED for MySQL 8. It works for both MySQL and MariaDB latest versions and should still work with older versions as well.

#2 The weekly partitioning is FIXED. Thanks @beinvisible

Make sure to uncomment the correct lines (see blog post), the default is setup for MySQL 5.6 or MariaDB.

Also, see common issue at the bottom of the blog post.

# Zabbix MySQL partitioning perl script

Disclaimer: This script isn't made by us, but the current author is unknown. We've added it to Github for ease of access. If you are the original creator of this script, please send us a private message. With that out of the way, let's move on.



Welcome to the Opensource ICT Solutions GitHub, where you'll find all kinds of usefull Zabbix resources. This script is a script written in Perl to partition the Zabbix database tables in time based chunks. We can use this script to replace the Zabbix housekeeper process which tends to get too slow once you hit a certain database size.

With the use of MySQL partitioing using fixed History and Trend storage periods for all items we can mitigate this issue and grow our Zabbix database even further.

## How to use the script
Make sure to partition the database first, if you do not know how. Check out this blog post:
https://blog.zabbix.com/partitioning-a-zabbix-mysql-database/13531/

Or check out our Zabbix book for a detailed description:
https://www.amazon.com/Zabbix-Infrastructure-Monitoring-Cookbook-maintaining/dp/1800202237


Place the script in:
```
/usr/share/zabbix/
```

Then make it executable with:
```
chmod +x /usr/share/zabbix/mysql_zbx_part.pl
```

Now add a cronjob with:
```
crontab -e
```

Add the following line:
```
0 23 * * * /usr/share/zabbix/mysql_zbx_part.pl >/dev/null 2>&1
```

We also need to install some Perl dependencies with:

```
yum install perl-DateTime perl-Sys-Syslog perl-DBI perl-DBD-mysql

```

If perl-DataTime isn't available on your Centos8 installation make sure to install the powertools repo with:
```
yum config-manager --set-enabled powertools
```

On a Debian based systems run:
```
apt-get install libdatetime-perl liblogger-syslog-perl
```

MAKE SURE TO UNCOMMENT THE CORRECT LINES FOR THE VERSION YOU NEED. Check the blog post for more information.
```
# MySQL 5.5
# MySQL 5.6 + MariaDB
# MySQL 8.x (NOT MariaDB!)
```

That's it! You are now done and you have setup MySQL partitioing. We could execute the script manually with:
```
perl /usr/share/zabbix/mysql_zbx_part.pl
```

Then we can check and see if it worked with:
```
journalctl -t mysql_zbx_part
```

### Partitioning by week
NOTE: See version 2.1 for older (before 20-09-2021) partitioned databases. Otherwise use 3.0+ upwards (recommended to use current).

By default history tables are partitioned by day and trends are partitioned by month. It is also possible to partition both types of tables by **week**.

To do so change the **period** value to **week** under **my $tables =**. Also make sure to use a different naming convention for your partition names (2021_w36) and while partitioning make sure to use the correct UNIXTIMESTAMP.

The weekly partitioning setup IS NOT described in the Zabbix blog.

