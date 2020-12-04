# MySQL创建用户和授权

## 创建用户

```sql
-- 指定ip：192.118.1.1的mjj用户登录
create user 'alex'@'192.118.1.1' identified by '123';
-- 指定ip：192.118.1.开头的mjj用户登录
create user 'alex'@'192.118.1.%' identified by '123';
-- 指定任何ip的mjj用户登录
create user 'alex'@'%' identified by '123';
```

## 删除用户

```sql
drop user '用户名'@'IP地址';
```

## 修改用户

```sql
rename user '用户名'@'IP地址' to '新用户名'@'IP地址';
```

## 修改密码

```sql
set password for '用户名'@'IP地址'=Password('新密码');
```

## 查看权限

```sql
show grants for '用户'@'IP地址'
```

## 授权

```sql
-- mjj用户仅对db1.t1文件有查询、插入和更新的操作
grant select ,insert,update on db1.t1 to "alex"@'%';

-- 表示有所有的权限，除了grant这个命令，这个命令是root才有的。mjj用户对db1下的t1文件有任意操作
grant all privileges  on db1.t1 to "alex"@'%';

-- mjj用户对db1数据库中的文件执行任何操作
grant all privileges  on db1.* to "alex"@'%'; 

-- mjj用户对所有数据库中文件有任何操作
grant all privileges  on *.*  to "alex"@'%';
```

## 取消权限

```sql
-- 取消mjj用户对db1的t1文件的任意操作
revoke all on db1.t1 from 'alex'@"%";  

-- 取消来自远程服务器的mjj用户对数据库db1的所有表的所有权限
revoke all on db1.* from 'alex'@"%";  

-- 取消来自远程服务器的mjj用户所有数据库的所有的表的权限
revoke all privileges on *.* from 'alex'@'%';
```
