---
layout: post
title: Troubleshooting Dovecot Error Messages
date: 2015-02-04 19:15
comments: true
categories:
- misc
- dovecot
- postfix
---
If you receive either of the following errors:

`dovecot: imap: Error: user testuser: Relative home directory paths not supported: /var/mail/1/`
`dovecot: imap(testuser): Error: user testuser: Initialization failed: Namespace '': Ambiguous mail location setting, don't know what to do with it: /var/mail/1 (try prefixing it with mbox: or maildir:)`


Contents of `/etc/dovecot/conf.d/auth-sql.conf.ext`:

```
userdb {
  driver = static
  args = uid=vmail gid=vmail home=/var/mail/1/
}
```

Change it to:

```
userdb {
  driver = static
  args = uid=vmail gid=www-pub mail_home = /var/mail/ mail_location = maildir:~/1
}
```












