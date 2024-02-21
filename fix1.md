## fixing the below error at nagios gui while enable all notification for all service at host



Error: Could not open command file '/usr/local/nagios/var/rw/nagios.cmd' for update!
The permissions on the external command file and/or directory may be incorrect. Read the FAQs on how to setup proper permissions.
An error occurred while attempting to commit your command for processing.

### ubuntu steps to fix

Fix for Ubuntu Server
We must change the group ownership of the /usr/local/nagios/var/rw/ directory. First stop the Nagios service and then change the group ownership and start Nagios service again:
# service nagios stop
# sudo chown -R nagios:www-data /usr/local/nagios/var/rw/
# service nagios start
Now restart Apache web service.
# service apache2 restart

