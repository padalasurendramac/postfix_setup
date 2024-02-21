# postfix_setup
postfix_setup and integrations with nagios notification alerts

## os ubuntu

### install the below packages mailx and postfix
 sudo apt install mailutils postfix

 popups > choose > internet > reset all in default and click ok

## note for re configure postfix configure use this below command 
sudo dpkg-reconfigure postfix

#### popup's
Choose No to synchronous updates
Enter 127.0.0.0/8 [::ffff:127.0.0.0]/104 [::1]/128 for local network
0 for the mailbox limit
+ for the local inbox extension character
Select all for the Internet protocols

 ### 587 port tls setup /etc/postfix/main.cf

 

 
 
