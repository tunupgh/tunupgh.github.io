---
layout: post
title: SSH Without Password
date: 2015-02-07 13:01
comments: true
categories:
- ssh
---
It is possible to ssh from one Linux or Mac machine to another Linux server by using only cryptographic keys instead of using a password.

### Create the private key on the source computer

On the source system `192.168.100.1` create the private key with the following command:

```bash
cd ~/.ssh
ssh-keygen -t rsa
```

Choose no passphrase when asked and accept the default filename of id_rsa. This creates both the `id_rsa` private key file and a `id_rsa.pub` public key file. Keep the private key on the source system and copy the public key to the destination system.

### Move the public key to the destination computer

Now we need the move that public key from the source computer to the destination.

From the source `192.168.100.1` use the following command:

```bash
scp id_rsa.pub <user>@<yourhost>:.ssh/authorized_keys
```

This is the username you normally use to ssh into that system. So if your username was `darcy` and the destination IP is `192.168.200.1` then the command becomes:

```bash
scp id_rsa.pub darcy@192.168.200.1:.ssh/authorized_keys
```

### Testing

Congrats you've set up the ssh keys so that you no longer need to use a password when ssh'ing into that system. To test this simply do ssh like normal and it should automatically connect you.

`ssh darcy@192.168.200.1`

##### Add an alias

You can add an alias for this command to make it even quicker to get to that system. If you're using a bash shell simply add this to your .bashrc file on your source computer.

```bash
alias s1='ssh darcy@192.168.200.1'
```

Now you simply need to type `s1` and you'll automatically be logged into that system.

### Troubleshooting

Verify you have spelled the `.ssh` and `authorized_keys` file correctly.

Sometimes permissions needs to be set on the directory and file. Here are the permissions needed to be set.

```bash
chmod 700 .ssh
chmod 600 .ssh/authorized_keys
```

## Related Articles

* [How to ssh like a pro](/how-to-ssh-like-a-pro/)
* [How to create ssh tunnels](/how-to-create-ssh-tunnels/)


