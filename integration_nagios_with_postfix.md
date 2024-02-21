
# reference link 
https://linuxhowtoguide.blogspot.com/2017/10/steps-how-to-configure-e-mail.html

## 
Step 1- setup PostFix SMTP relay.
Please follow my previous blog- How to setup PostFix SMTP relay.
https://linuxhowtoguide.blogspot.in/2017/10/how-to-configure-postfix-smtp-relay.html

Step 2- Configure Nagios Core to Send notification.

 Modify command.cfg file to enable notification service.  comment default configuration for both notify host by email and notify service by email as follow.

# note :- replace you email id insteand of From:Nagios Alerts<asingh@domain.com> at both notify host by email and notify-service-by-email
root@U16:~# vi /usr/local/nagios/etc/objects/commands.cfg
Please modify your email in the below configuraiton



# 'notify-host-by-email' command definition
#define command{
#       command_name    notify-host-by-email
#       command_line    /usr/bin/printf "%b" "***** Nagios *****\n\nNotification Type: $NOTIFICATIONTYPE$\nHost: $HOSTNAME$\nState: $HOSTSTATE$\nAddress: $HOSTADDRESS$\nInfo: $HOSTOUTPUT$\n\nDate/Time: $LONGDATETIME$\n" | /bin/mail -s "** $NOTIFICATIONTYPE$ Host Alert: $HOSTNAME$ is $HOSTSTATE$ **" $CONTACTEMAIL$
#       }


# 'notify-host-by-email' command definition
define command{
        command_name    notify-host-by-email
        command_line    /usr/bin/printf "%b" "Notification Type: $NOTIFICATIONTYPE$\nHost: $HOSTNAME$\nState: $HOSTSTATE$\nAddress: $HOSTADDRESS$\nInfo: $HOSTOUTPUT$\n\nDate/Time: $LONGDATETIME$\n" | /usr/bin/mail -a "From:Nagios Alerts<asingh@domain.com>" -s "** $NOTIFICATIONTYPE$ $HOSTNAME$ is $HOSTSTATE$ **" $CONTACTEMAIL$
        }

# 'notify-service-by-email' command definition
#define command{
#       command_name    notify-service-by-email
#       command_line    /usr/bin/printf "%b" "***** Nagios *****\n\nNotification Type: $NOTIFICATIONTYPE$\n\nService: $SERVICEDESC$\nHost: $HOSTALIAS$\nAddress: $HOSTADDRESS$\nState: $SERVICESTATE$\n\nDate/Time: $LONGDATETIME$\n\nAdditional Info:\n\n$SERVICEOUTPUT$\n" | /bin/mail -s "** $NOTIFICATIONTYPE$ Service Alert: $HOSTALIAS$/$SERVICEDESC$ is $SERVICESTATE$ **" $CONTACTEMAIL$
#       }




# 'notify-service-by-email' command definition
define command{
        command_name    notify-service-by-email
        command_line    /usr/bin/printf "%b" "Service: $SERVICEDESC$\nInfo: $SERVICEOUTPUT$\nHost: $HOSTALIAS$\nAddress: $HOSTADDRESS$\nState: $SERVICESTATE$\nDate/Time: $LONGDATETIME$\n" | /usr/bin/mail -a "From:Nagios Alerts<asingh@domain.com>" -s "** $NOTIFICATIONTYPE$ - $HOSTALIAS$ - $SERVICEDESC$ is $SERVICESTATE$ **" $CONTACTEMAIL$
        } 

Step 3- Modify contact.cfg - We need to add email address where all the notification will go.
root@U16:~# vi /usr/local/nagios/etc/objects/contacts.cfg

add email account where you want notification
define contact{
        contact_name                    nagiosadmin             ; Short name of user
        use                             generic-contact         ; Inherit default values from generic-contact template (defined above)
        alias                           Nagios Admin            ; Full name of user

        email                           amarsingh3d@gmail.com   ; <<***** CHANGE THIS TO YOUR EMAIL ADDRESS ******
        }
Save and exit from the file.

Note:- Nagios Default setting sends service notification email after 60 minutes and host notification after 30 minutes. We can modify setting as per our requirement. I will change it to 10 minutes.
root@U16:~# vi /usr/local/nagios/etc/objects/templates.cfg

For service, notification modify red highlighted entry
# Generic service definition template - This is NOT a real service, just a template!

define service{
        name                            generic-service         ; The 'name' of this service template
        active_checks_enabled           1                       ; Active service checks are enabled
        passive_checks_enabled          1                       ; Passive service checks are enabled/accepted
        parallelize_check               1                       ; Active service checks should be parallelized (disabling this can lead to major performance problems)
        obsess_over_service             1                       ; We should obsess over this service (if necessary)
        check_freshness                 0                       ; Default is to NOT check service 'freshness'
        notifications_enabled           1                       ; Service notifications are enabled
        event_handler_enabled           1                       ; Service event handler is enabled
        flap_detection_enabled          1                       ; Flap detection is enabled
        process_perf_data               1                       ; Process performance data
        retain_status_information       1                       ; Retain status information across program restarts
        retain_nonstatus_information    1                       ; Retain non-status information across program restarts
        is_volatile                     0                       ; The service is not volatile
        check_period                    24x7                    ; The service can be checked at any time of the day
        max_check_attempts              3                       ; Re-check the service up to 3 times in order to determine its final (hard) state
        check_interval           10                     ; Check the service every 10 minutes under normal conditions
        retry_interval            2                     ; Re-check the service every two minutes until a hard state can be determined
        contact_groups                  admins                  ; Notifications get sent out to everyone in the 'admins' group
        notification_options            w,u,c,r                 ; Send notifications about warning, unknown, critical, and recovery events
        notification_interval           10                      ; Re-notify about service problems every hour
        notification_period             24x7                    ; Notifications can be sent out at any time
         register                        0                      ; DONT REGISTER THIS DEFINITION - ITS NOT A REAL SERVICE, JUST A TEMPLATE!
        }
For Host, notification modify red highlighted entry

# note till here it is fine skip the host part 


# Define a template for switches that we can reuse
define host{
        name                    generic-switch  ; The name of this host template
        use                     generic-host    ; Inherit default values from the generic-host template
        check_period            24x7            ; By default, switches are monitored round the clock
        check_interval          5               ; Switches are checked every 5 minutes
        retry_interval          1               ; Schedule host check retries at 1 minute intervals
        max_check_attempts      10              ; Check each switch 10 times (max)
        check_command           check-host-alive        ; Default command to check if routers are "alive"
        notification_period     24x7            ; Send notifications at any time
        notification_interval   10              ; Resend notifications every 30 minutes
        notification_options    d,r             ; Only send notifications for specific host states
        contact_groups          admins          ; Notifications get sent to the admins by default
        register                0               ; DONT REGISTER THIS - ITS JUST A TEMPLATE
        }
Save and Exit from the file. Now, you will get a notification after 10 minutes of interval.

Nagios E-mail notification has been configured successfully.
