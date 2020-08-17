# what is postfix
Postfix works like a router Now let's look more closely at the system. A real router usually accepts IP packets from multiple interfaces, routing them back out through the interfaces. The same is true for Postfix; it accepts messages from multiple sources and then passes the mail on to multiple destinations.A message's origin may be 
1- local sendmail binary
2- SMTP or QMQP connection. 

The destination can be a local mailbox, outgoing SMTP or LMTP, a pipe into a program, and more.

![logo](/images/piccy.png)

# What is a bounced email
A bounced email is any email message rejected by a recipient's mail server and returned to the original sender. A bounced email means that your message has not been delivered, and when this happens the mailer receives a notification of non-delivery.


# Postfix lookup tables
In Postfix, lookup tables are called maps. Postfix uses maps not only to find out where to send mail, but also to impose restrictions on clients, senders, and recipients, and to check certain patterns in email content.

![logo](/images/piccy2.png)


# postix components

# master
The master daemon is the supervisor of Postfix, and it oversees all other Postfix daemons. The master waits for incoming jobs to be delegated to subordinate daemons. If there is a lot of work to do, the master can invoke multiple instances of a daemon. You can configure the number of simultaneous daemon instances, how often Postfix can reuse them, and a period of inactivity that should elapse before stopping an instance.

# bounce defer 
A mail transfer agent must notify the sender about undeliverable mail. In Postfix, the bounce and defer daemons handle this task, which is triggered by the queue manager (qmgr).

# error  ( blacklisting domains )
The error daemon is a mail delivery agent like local or smtp. It is a delivery agent that always causes mail to be bounced. Usually you don't use it unless you configure a domain as undeliverable by directing mail to the error delivery agent. If a mail is sent to the error daemon it will inform the bounce daemon to record that a recipient was undeliverable.

# trivial-rewrite ( corrects email mnaming formats )
The trivial-rewrite daemon acts upon request by the cleanup daemon in order to rewrite nonstandard addresses into the standard user@fqdn form.

This daemon also resolves destinations upon request from the queue manager (qmgr). By default, trivial-rewrite distinguishes only between local and remote destinations.

# showq
The showq daemon lists the Postfix mail queue, and it is the program behind the mailq (sendmail -bp) command. This daemon is necessary because the Postfix queue is not world-readable; a non-setuid user program cannot list the queue (and Postfix binaries are not setuid).

# flush
The flush daemon attempts to clear the mail queue of pending and deferred messages. By using a per-destination list of queued mail, it improves the performance of the SMTP Extended Turn (ETRN) request and its command-line equivalent, sendmail -qR destination. You can maintain the list of destinations with the fast_flush_domains parameter in the main.cf file.

# qmgr
The qmgr daemon manages the Postfix queues; it is the heart of the Postfix mail system. It distributes delivery tasks to the local, smtp, lmtp, and pipe daemons. After delegating a job, it submits queue file path-name information, the message sender address, the target host (if the destination is remote), and one or more message-recipient addresses to the daemon it delegated the delivery task to.

    - qmgr maintains a small active queue, with just a few messages pending for delivery. This queue effectively acts as a limited window for the potentially larger incoming and deferred queues, and it prevents qmgr from running out of memory under a heavy load.

    - If Postfix cannot immediately deliver a message, qmgr moves the message to the deferred queue. Keeping temporarily undeliverable messages in a separate queue ensures that a large mail backlog does not slow down normal queue access.