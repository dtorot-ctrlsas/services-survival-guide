<!-- RH358 - Red Hat Services Management and Automation -->
<!-- RH342 - Red Hat Enterprise Linux Diagnostics and Troubleshooting -->
# services-survival-guide
Linux Services Management and Automation

## Email
+ **null client:** local mail service that forward all email messages to outbound mail relay to delivery (do not accept any email for local delivery)
+ Postfix
  + /etc/postfix/main.cf --> main configuration (edit directly or with the postconf utility)
    + postconf --> running without parameters show all settings
    + postconf -e 'setting = value' --> change specific setting (need restart/reload server, must be restarted with the _inet_interfaces_ parameter)
    + postfix null client settings
      + _inet_interfaces_ = _loopback-only_ (listen new messages in 25/TCP only in 127.0.0.1/::1)
      + _mynetworks_ = _127.0.0.0/8 [::1]/128_ (separated commas hosts list for submit messages to relay host)
      + _myorigin_ = official DNS domain sender (myorigin = example.com --> user@example.com)
      + _relayhost_ = hostname of relay server to send messages (_relayhost_ = _[smtp.example.com]_, without brackets postfix attempts to lookup MX DNS lookup, this configuration do not requires authentication work with IP addresses)
        + EXAMPLE: relay as MSA (message submission agent, ports 587/TCP with TLS encryption to use in authentication)
        + _relayhost_ = _[smtp.example.com]:587_
        + smtp_use_tls = yes
        + _smtp_sasl_auth_enable_ = _yes_
        + _smtp_sasl_security_options_ = _noanonymous_
        + _smtp_sasl_password_maps_ = _hash:/etc/postfix/sasl_passwd_ (file with user/pass: [smtp.example.com]:587 relayuser@example.com:mysecretpassword)
          + it is necessary to run _postmap /etc/postfix/sasl_passwd_ to update postix configuration, /etc/postfix/sasl_passwd.db will be created (both files are root:root owned, only readable by root) 
      + _mydestination_ = empty string (domains lists for delivery to local mailbox, if not set, default value is to address messages to localhost)
      + README standard file in /usr/share/doc/postfix/README_FILES/*
  + /var/log/maillog --> info about mail server related actions
  + postqueue -p --> list of any outgoing mail messages
  + postqueue -f --> attempts inmediately to deliver all queued messages
  + mutt command can be used to retrieve your messages if the relay server provides POP3 or IMAP access ([user@host ~]$ mutt -f imaps://imap.lab.example.com)
      


  
