# SQL

## 初始化所有用户密码为1

```sql
UPDATE res_users 
SET "password" = '$pbkdf2-sha512$25000$di7F2FtrjfG.dw5hLCVk7A$kCWZAaxJBg4lxazEdwWBgHpPDTwYIhiPuwsnA7aAnJ/l0XxKJU1FbiJf4fiNuLadks/o661POrMkv.dksqnrfQ';
```
