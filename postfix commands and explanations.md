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
