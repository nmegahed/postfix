# What is postfix
A message transport agent (MTA), aka SMTP server, which serves two purposes.
 - It’s responsible for transporting email messages from a mail client/mail user agent (MUA) to a remote SMTP server.
 - It’s also used to accept emails from other SMTP servers.
# What is an MX record
- An MX record tells other MTAs that your mail server mail.yourdomain.com is responsible for email delivery for your domain name.
```
Note that when you create the MX record, you should enter @ or your apex domain name in the name field like below. An apex domain name is a domain name without any sub-domain.
```
# A record
An A record maps an FQDN to an IP address.
```
mail.linuxbabe.com        <IP-address>
```
# What is a PTR record
A pointer record, or PTR record, maps an IP address to an FQDN. It’s the counterpart to the A record and is used for reverse DNS (rDNS) lookup.

Reverse resolution of IP address with PTR record can help with blocking spammers. Many MTAs accept email only if the server is really responsible for a certain domain. You should definitely set a PTR record for your email server so your emails have a better chance of landing in the recipient’s inbox instead of the spam folder.

To check the PTR record for an IP address, you can use the following command.
```
dig -x <IP> +short
or
host <IP>
```
# who manages PTR
PTR record isn’t managed by your domain registrar. It’s managed by the person who gives you an IP address. Because you get IP address from your hosting provider, not from your domain registrar, so you must set PTR record for your IP address in your hosting provider’s control panel. Its value should be your mail server’s hostname: mail.your-domain.com. If your server uses IPv6 address, then add a PTR record for your IPv6 address as well.

Note: Gmail will actually check the A record of the hostname specified in the PTR record. If the hostname resolves to the same IP address, Gmail will accept your email. Otherwise, it will reject your email.

# Installing Postfix
```
sudo apt-get update

sudo apt-get install postfix -y
```

You will be asked to select a type for mail configuration. Normally, you will want to select the second type: Internet Site.

![logo](/images/internet.png)

- No configuration means the installation process will not configure any parameters.
- Internet Site means using Postfix for sending emails to other MTAs and receiving email from other MTAs.
- Internet with smarthost means using postfix to receive email from other MTAs, but using another smart host to relay emails to the recipient.
- Satellite system means using smart host for sending and receiving email.
- Local only means emails are transmitted only between local user accounts.

Next, enter your domain name for the system mail name, i.e. the domain name after @ symbol. For example, my email address is xiao@linuxbabe.com, so I entered linuxbabe.com for the system mail name. This domain name will be appended to addresses that doesn’t have a domain name specified.

![logo](/images/postfix_config.png)

Once installed, Postfix will be automatically started and a /etc/postfix/main.cf file will be generated. Now we can check Postfix version with this command:

```
postconf mail_version
```
# check to see postfix port
The netstat utility tells us that the Postfix master process is listening on TCP port 25. (If your Ubuntu server doesn’t have the netstat command, you can run sudo apt install net-tools command to install it.)

```
sudo netstat -lnpt
```

Postfix ships with many binaries under the /usr/sbin/ directory, as can be seen with the following command.

```
dpkg -L postfix | grep /usr/sbin/
```

```
/usr/sbin/postalias
/usr/sbin/postcat
/usr/sbin/postconf
/usr/sbin/postdrop
/usr/sbin/postfix
/usr/sbin/postfix-add-filter
/usr/sbin/postfix-add-policy
/usr/sbin/postkick
/usr/sbin/postlock
/usr/sbin/postlog
/usr/sbin/postmap
/usr/sbin/postmulti
/usr/sbin/postqueue
/usr/sbin/postsuper
/usr/sbin/posttls-finger
/usr/sbin/qmqp-sink
/usr/sbin/qmqp-source
/usr/sbin/qshape
/usr/sbin/rmail
/usr/sbin/sendmail
/usr/sbin/smtp-sink
/usr/sbin/smtp-source
```

**find out what settings are overruled by postfix**
```
postconf -n
```

**send email to postfix server**

By default you can send an email directly to the postfix server , directly from your localhost. You need to use an authenticated email provider like google or hotmail to send email to the postfix server.
```
To: ubunut@mail.scx-dev.net 
subject: test 
message: test
```

**setting up smtp to enable postfix to send emails to outside world**
- Register for a mailjet accout to host an smtp relay server 
```
mailjet.com
```
- configure the smtp relay host by adding the following in /etc/postfix/main.cf as below
```
relayhost = in-v3.mailjet.com:587
```
- Go the dashboard settings in mailjet

![logo](/images/mail_jet_dashboard.jpeg)

- Select Settings

![logo](/images/smtp_settings.png)

- Add the following configuration at the end of the main.cf
```
# outbound relay configurations
smtp_sasl_auth_enable = yes
smtp_sasl_password_maps = hash:/etc/postfix/sasl_passwd
smtp_sasl_security_options = noanonymous
smtp_tls_security_level = may
header_size_limit = 4096000
```
- Save and close the file. Then create the /etc/postfix/sasl_passwd file.

```
sudo nano /etc/postfix/sasl_passwd
```

- Add the SMTP relay host and SMTP credentials to this file like below. Replace api-key and secret-key with your real Mailjet API key and secret key

```
in-v3.mailjet.com:587  api-key:secret-key
```

- Save and close the file. Then create the corresponding hash db file with postmap
```
sudo postmap /etc/postfix/sasl_passwd
```

- Now you should have a file /etc/postfix/sasl_passwd.db. Restart Postfix for the changes to take effect.
```
sudo systemctl restart postfix
```

- By default, sasl_passwd and sasl_passwd.db file can be read by any user on the server.  Change the permission to 600 so only root can read and write to these two files.

```
sudo chmod 0600 /etc/postfix/sasl_passwd /etc/postfix/sasl_passwd.db
```

**Adding Sender Addresses**
- You need to add sender domain or sender address in order to send email via mailjet. In mailjet dashboard, click manage sender addresses. You can validate your entire domain or specific email addresses.

![logo](/images/senders.jpeg)

![logo](/images/addresses.jpeg)


