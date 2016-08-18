# Develop Machine Setup

## Tools

- PuTTY or SecureCRT or Cygwin (for Windows)
- terminal (for Linux / macOS)
- ssh
- scp
- screen
- vim

## Login

### Terminal

```
$ ssh username@hostname
-or-
$ ssh username@xxx.xxx.xxx.xxx
```

### PuTTY

> http://www.zzbaike.com/wiki/PuTTY/利用PuTTY登陆SSH主机

> http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/putty.html

### SSH免密码登录

#### Using Terminal (Linux or Cygwin)

**In client machine**

```
# Create id_rsa and id_rsa.pub in ~/.ssh
$ ssh-keygen -t rsa
$ scp id_rsa.pub username@xxx.xxx.xxx.xxx:~/.ssh/id_rsa.pub.remote
```

**In remote server**

```
$ touch ~/.ssh/authorized_keys
$ cat ~/.ssh/id_rsa.pub.remote >> ~/.ssh/authorized_keys
```

#### PuTTY

> http://www.putty.ws/putty-ssh-gey


## Basic Setup

### `bash`

Set bash config in `.bashrc`. A bashrc config as below for reference.

> https://github.com/robbie-cao/dotfiles.robc/blob/master/bashrc

### `screen`

Set `screen` config in `.screenrc`.

> https://github.com/robbie-cao/dotfiles.robc/blob/master/screenrc

References for how use `screen`.

http://www.ibm.com/developerworks/cn/linux/l-cn-screen/

http://www.linuxidc.com/Linux/2014-04/100040.htm

https://www.rackaid.com/blog/linux-screen-tutorial-and-how-to/

https://www.howtoforge.com/linux_screen

## Copy Files from/to Remote Server

### `scp`

```
# from remote server to local
$ scp username@remote_ip_addr:~/path/to/file.xxx local/path/
$ scp -r username@remote_ip_addr:~/path/to/folder local/path/

# from local to remote server
$ scp local/path/file.xxx username@remote_ip_addr:~/path/to/
$ scp -r local/path/folder username@remote_ip_addr:~/path/to/
```

### samba

> TODO

## Git Account Setup

### Generating SSH Key

> https://help.github.com/articles/generating-an-ssh-key/

### Adding SSH Key to GitHub Account

> https://help.github.com/articles/adding-a-new-ssh-key-to-your-github-account/

### Git Config

> https://git-scm.com/book/en/v2/Getting-Started-First-Time-Git-Setup

Here's a config template, you can just simple copy it as .gitconfig and change username/email and other user info to your own.

> https://github.com/robbie-cao/dotfiles.robc/blob/master/gitconfig

## Vim

The most important weapon for programmer in Linux.

Learn to use it!

## Reference

