# Linux




# Temp Record
```bash
echo emh1amluZ2RpMTk5OEBnbWFpbC5jb20= | base64 -d
```

- ps 命令就是通过遍历 `/proc/<pid>` 

修改ssh登录配置
```sh
sudo vi /etc/ssh/sshd_config 
```
- `PasswordAuthentication` 是否启用密码
- `PermitRootLogin` 是否允许root用户登录
```sh
# 改用户密码
sudo passwd root
```