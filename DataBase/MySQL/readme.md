- `MYSQL_ROOT_PASSWORD` ：这个不用解释，root 用户的密码。
- `MYSQL_USER`，`MYSQL_PASSWORD` ：这两个变量为可选，创建一个新用户，这个用户在 `MYSQL_DATABASE` 变量指定的数据库上拥有超级用户权限。
- `MYSQL_DATABASE` ：指定一个数据库，在容器启动时创建。
- `MYSQL_ALLOW_EMPTY_PASSWORD` ：设置为 yes 允许 root 用户的密码为空。（不推荐）
- `MYSQL_RANDOM_ROOT_PASSWORD` ：设置为 yes 将在容器启动时为 root 用户生成一个随机的密码，密码会显示到标准输出流（`GENERATED ROOT PASSWORD:......`）。
- `MYSQL_ONETIME_PASSWORD` ：字面意思就是一次性密码，为 root 用户设置，第一次登录后必须修改密码（仅支持 5.6 以上的版本）。

此镜像自带环境

```
MYSQL_ROOT_PASSWORD=root
MYSQL_ROOT_HOST=%

MYSQL_DIR=./
```

