# what is postfix
Postfix works like a router Now let's look more closely at the system. A real router usually accepts IP packets from multiple interfaces, routing them back out through the interfaces. The same is true for Postfix; it accepts messages from multiple sources and then passes the mail on to multiple destinations.A message's origin may be 
1- local sendmail binary
2- SMTP or QMQP connection. 

The destination can be a local mailbox, outgoing SMTP or LMTP, a pipe into a program, and more.

![logo](/images/piccy.png)

# Postfix lookup tables
In Postfix, lookup tables are called maps. Postfix uses maps not only to find out where to send mail, but also to impose restrictions on clients, senders, and recipients, and to check certain patterns in email content.

