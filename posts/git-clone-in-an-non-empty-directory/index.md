# Git 克隆仓库到非空目录


在开发过程中，偶尔需要克隆一个项目到非空目录，这时 git 会报错：

```
fatal: destination path '.' already exists and is not an empty directory.
```

提示目标目录是一个非空目录，解决办法也很简单，进入该目录执行：

```bash
# 克隆目标
$ git clone --no-checkout https://github.com/username/repository.git repository
# 移动 .git 目录到非空目录
$ mv repository/.git .
# 删除克隆的目录
$ rm -rf repository
# 恢复文件
$ git reset --hard HEAD
```


