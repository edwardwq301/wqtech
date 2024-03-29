# Linux tricks
## dotfiles
经常重装系统/虚拟机的朋友们可能经常遇到安装 `vimrc` 等配置的苦恼，那么这个工具可以帮助你 

- [仓库链接](https://github.com/anishathalye/dotbot)
- [quick start](https://www.elliotdenolf.com/blog/bootstrap-your-dotfiles-with-dotbot)

## SSH
假设用户是 `wqq`

part1：生成并上传密钥
```bash
ssh-keygen -t rsa
# this use default loacation ~/.ssh/id_rsa.pub
ssh-copy-id wqq@server_ip 
# or use a specific public_key
# ssh-copy-id -i path/public_key user@server_ip
```

part2：确保密钥登录权限打开，大部分都是 `/etc/ssh/sshd_config`，（不确定可以用 `locate sshd_config
` 找一下）
```bash
PubkeyAuthentication yes
PasswordAuthentication yes
# if you want to allow password authentication
```

part3：重启 ssh 服务 `sudo systemctl restart ssh`

另外我发现还有些**注意**

- `authorized_keys` 要能被 wqq 自己读
- `/home/wqq` 别人不能有写的权限，就是 user group other 中的 other 不能有 w 权限

出错了还可以翻日志

Ubuntu, Debian:

- SSH server logs: `/var/log/auth.log`
- SSH client logs: `/var/log/syslog`

eg: `Mar 17 14:06:36 ub sshd[15869]: Authentication refused: bad ownership or modes for directory /home/wqq`

## IO
同步（拷贝）IO：假如是同步的 read，直到读完（内核空间拷到用户空间） read 才返回，

异步（拷贝）IO：read 直接返回，真正拷贝完会使用回调函数通知用户

eg： 烧水同步，一直看水开了再干别的；烧水异步，水开了会响铃，告诉我开了

[参考](https://www.cyhone.com/articles/reunderstanding-of-non-blocking-io/)


## cheatsheet
- `adduser` is more convenient than `useradd`