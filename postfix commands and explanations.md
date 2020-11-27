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
