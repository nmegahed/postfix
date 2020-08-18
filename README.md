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

# bounce defer ( notifies sender on undeliverable emails)
A mail transfer agent must notify the sender about undeliverable mail. In Postfix, the bounce and defer daemons handle this task, which is triggered by the queue manager (qmgr).

# bounce and error daemons ( contains a list of longer existing users and domains )
bounce mail for recipients listed in the relocated table that contains contact information for users or domains that no longer exist on the system.


# error  ( blacklisting domains )
The error daemon is a mail delivery agent like local or smtp. It is a delivery agent that always causes mail to be bounced. Usually you don't use it unless you configure a domain as undeliverable by directing mail to the error delivery agent. If a mail is sent to the error daemon it will inform the bounce daemon to record that a recipient was undeliverable.

# trivial-rewrite ( corrects email naming formats )
The trivial-rewrite daemon acts upon request by the cleanup daemon in order to rewrite nonstandard addresses into the standard user@fqdn form.

This daemon also resolves destinations upon request from the queue manager (qmgr). By default, trivial-rewrite distinguishes only between local and remote destinations.

# showq ( list the email messages in the postfix queues )
The showq daemon lists the Postfix mail queue, and it is the program behind the mailq (sendmail -bp) command. This daemon is necessary because the Postfix queue is not world-readable; a non-setuid user program cannot list the queue (and Postfix binaries are not setuid).

# flush ( flushes the queue from pending and deferred messages)
The flush daemon attempts to clear the mail queue of pending and deferred messages. By using a per-destination list of queued mail, it improves the performance of the SMTP Extended Turn (ETRN) request and its command-line equivalent, sendmail -qR destination. You can maintain the list of destinations with the fast_flush_domains parameter in the main.cf file.

# qmgr ( manages the mail queues )
The qmgr daemon manages the Postfix queues; it is the heart of the Postfix mail system. It distributes delivery tasks to the local, smtp, lmtp, and pipe daemons. After delegating a job, it submits queue file path-name information, the message sender address, the target host (if the destination is remote), and one or more message-recipient addresses to the daemon it delegated the delivery task to.

    - qmgr maintains a small active queue, with just a few messages pending for delivery. This queue effectively acts as a limited window for the potentially larger incoming and deferred queues, and it prevents qmgr from running out of memory under a heavy load.

# deferred Queues ( queue containing undeliverable mails )
If Postfix cannot immediately deliver a message, qmgr moves the message to the deferred queue. Keeping temporarily undeliverable messages in a separate queue ensures that a large mail backlog does not slow down normal queue access.

# proxymap
Provides read-only access to maps.

# spawn
The spawn process creates non-Postfix processes on request. 

# local ( handels local mail delivery )
As the name suggests, the local daemon is responsible for local mailbox delivery. The Postfix local daemon can write to mailboxes in the mbox and Maildir formats. In addition, local can access data in Sendmail-style alias databases and user .forward files.

# smtp ( service containing addresses of outgoing mail servers )
The smtp client is the Postfix client program that transports outbound messages to remote destinations. It looks up destination mail exchangers, sorts the list by preference, and tries each address until it finds a server that responds. A busy Postfix system typically has several smtp daemons running at once.

# pickup (pickups mail messages and puts it in maildrop )
The pickup daemon picks up messages put into the maildrop queue by the local sendmail user client program. After performing a few sanity checks, pickup passes messages to the cleanup daemon.

# cleanup ( add missing headers , performs rewriting and extract recipients and insets results in incoming queue )
The cleanup daemon is the final processing stage for new messages. It adds any required missing headers, arranges for address rewriting, and (optionally) extracts recipient addresses from message headers. The cleanup daemon inserts the result into the incoming queue and then notifies the queue manager that new mail has arrived.

# sendmail 
It interacts with the postdrop binary to put mail into the maildrop queue for pickup.

# anvil ( protects agains SMTP DOS )
The Postfix anvil is a preliminary defense against SMTP clients and denial-of-service attacks that swamp the SMTP server with too many simultaneous or successive connection attempts. It comes with a whitelist capability for disabling restrictions for authorized clients. 

# Postfix Queues ()
Postfix polls (puls messages from queues ) all queues in the directory specified by the queue_directory parameter in your main.cf file. The queue directory is usually /var/spool/postfix. Each queue has its own subdirectory with a name identifying the queue. All messages that Postfix handles stay in these directories until Postfix delivers them. You can determine the status of a message by its queue: incoming, maildrop, deferred, active, hold, or corrupt.

# incoming ( message placed in the incoming queue )
        All new messages entering the Postfix queue system get sent to the incoming queue by the cleanup service. New queue files are created with the postfix user as the owner and an access mode of 0600. As soon as a queue file is ready for further processing, the cleanup service changes the queue file mode to 0700 and notifies the queue manager that new mail has arrived. The queue manager ignores incomplete queue files whose mode is 0600.
# queue manager ( manages queues and queue messages )
        The queue manager scans the incoming queue when moving new messages into the active queue and makes sure that the active queue resource limits have not been exceeded. By default, the active queue has a maximum of 20,000 messages.
# Caution ( sends warning notifications when queue reaches near limit thresholds )
        Once the active queue message limit is reached, the queue manager stops scanning the incoming and deferred queues
#  maildrop ( messages received but not yet processed by posfix queues)
        Messages submitted with the sendmail command that have not been sent to the primary Postfix queues by the pickup service await processing in the maildrop queue. You can add messages to the maildrop queue even when Postfix is not running; Postfix will look at them once it is started.
# deferred
        If a message still has recipients for which delivery failed for some transient reason, and the message has been delivered to all the recipients possible, Postfix places the message into the deferred queue.
        The queue manager scans the deferred queue periodically to put deferred messages back into the active queue. The scan interval is specified with the queue_run_delay configuration parameter. If the deferred and incoming queue scans happen to take place at the same time, the queue manager alternates between the two queues on a per-message basis.
# active
        The active queue is somewhat analogous to an operating system's process run queue. Messages in the active queue are ready to be sent, but are not necessarily in the process of being sent.
# hold
         Messages placed in the hold queue stay there until the administrator intervenes. No periodic delivery attempts are made for messages in the hold queue. You can run the postsuper command to manually put messages on hold or to release messages from the hold queue into the deferred queue.
# corrupt
        The corrupt directory contains damaged queue files. Rather than discarding these, Postfix stores them here so that the (human) postmaster can inspect them using postcat.
# Maps
        Maps are files and databases that Postfix uses to look up information. Maps have many different purposes, but they all have one thing in common--a left-hand side (LHS, or key) and a right-hand side (RHS, or value).

![logo](/images/maps.png)

# Indexed Maps (hash, btree, dbm)
Indexed maps are binary databases built from regular text files with commands such as newaliases, postalias, and postmap. The binary maps have an indexed format so that Postfix can quickly retrieve the value associated with a key. As a further performance improvement, the Postfix daemons open these maps when starting up, and they do not re-read them unless they notice a change in the map files in the filesystem. To reload a map, a daemon exits and a new one is started by the master daemon.