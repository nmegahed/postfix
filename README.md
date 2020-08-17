# How it works
Postfix works like a router Now let's look more closely at the system. A real router usually accepts IP packets from multiple interfaces, routing them back out through the interfaces. The same is true for Postfix; it accepts messages from multiple sources and then passes the mail on to multiple destinations.A message's origin may be 
1- local sendmail binary
2- SMTP or QMQP connection. 

The destination can be a local mailbox, outgoing SMTP or LMTP, a pipe into a program, and more.

![logo](/images/piccy.png)
