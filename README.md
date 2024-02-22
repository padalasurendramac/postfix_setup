# postfix_setup

postfix_setup and integrations with nagios notification alerts

pre requirement aws ses or gmail ses account and dns name to be set 
command example
hostname test.com

## aws 
create smtp user at aws ses service 

save access and secret key 

and note the smtp server details from aws ses service 





## os ubuntu

### install the below packages mailx and postfix
 sudo apt install mailutils postfix

 popups > choose > internet > rest all in default and click ok

## note for re configure postfix configure use this below command 
sudo dpkg-reconfigure postfix

#### popup's
Choose No to synchronous updates

Enter 127.0.0.0/8 [::ffff:127.0.0.0]/104 [::1]/128 for local network

0 for the mailbox limit

+ for the local inbox extension character

Select all for the Internet protocols

 ### 587 port tls setup /etc/postfix/main.cf

propertes that are needs to be update at main.cf

smtpd_banner = $myhostname ESMTP $mail_name (Ubuntu)

biff = no

compatibility_level = 3.6

readme_directory = no

# TLS parameters

smtpd_tls_cert_file=/etc/ssl/certs/ssl-cert-snakeoil.pem

smtpd_tls_key_file=/etc/ssl/private/ssl-cert-snakeoil.key

smtpd_tls_security_level=may


smtp_tls_CApath=/etc/ssl/certs

smtp_tls_security_level=may

smtp_tls_session_cache_database = btree:${data_directory}/smtp_scache



smtpd_relay_restrictions = permit_mynetworks permit_sasl_authenticated defer_unauth_destination

myhostname = example.com

alias_maps = hash:/etc/aliases

alias_database = hash:/etc/aliases

mydestination = ip-172-31-84-112.ec2.internal, ip-172-31-84-112.ec2.internal, localhost.ec2.internal, localhost, localhost.localdomain

#mydestination = localhost, localhost.localdomain


relayhost = [email-smtp.us-east-1.amazonaws.com]:587

mynetworks = 127.0.0.0/8 [::ffff:127.0.0.0]/104 [::1]/128

mailbox_size_limit = 0

recipient_delimiter = +

inet_interfaces = all

inet_protocols = all


smtp_sasl_password_maps = hash:/etc/postfix/sasl_passwd

smtp_sasl_auth_enable = yes

smtp_sasl_security_options = noanonymous

myorigin = /etc/mailname

smtp_generic_maps = hash:/etc/postfix/generic



### create or updated /etc/postfix/sasl_passwd and /etc/postfix/generic and /etc/aliases files 

## create or update /etc/aliases

root@vanna:/etc/postfix# cat /etc/aliases

# See man 5 aliases for format

postmaster:    root

root: surendra.padala@gmail.com

root@vanna:/etc/postfix#


after created above file ran the below command 

sudo newaliases

## create or update /etc/postfix/sasl_passwd


root@vanna:/etc/postfix# cat /etc/postfix/sasl_passwd

[email-smtp.us-east-1.amazonaws.com]:587 AK*************7Z:B**************************b

root@vanna:/etc/postfix#

after created above file ran the below command 

sudo postmap /etc/postfix/sasl_passwd


chmod -v 0600 /etc/postfix/sasl_passwd

chmod -v 0600 /etc/postfix/sasl_passwd.db

## create or update /etc/postfix/generic

root@vanna:/etc/postfix# cat /etc/postfix/generic

test@gmail.com surendra.padala@gmail.com

root@vanna:/etc/postfix#


after created above file ran the below command

sudo postmap /etc/postfix/generic


chmod -v 0600 /etc/postfix/generic

chmod -v 0600 /etc/postfix/generic.db

####then restart the postfix service 

systemctl restart postfix 

### testing the email 
with from mail 

echo "test" | mail -s "test" -a "From: surendra.padala@gmail.com" surendra.padala@gmail.com


without from mail

echo "test" | mail -s "test"   test@gmail.com


 
#### after completed testing 

## integration with Nagios 


 
 
