
### `目录操作`

```sh
cd /path/to/dir         # 进入目录
cd                      # 回到当前用户主目录（~）
cd ~username            # 进入其他用户的主目录
cd -                    # 快速返回上一次所在目录
cd ..                   # 返回上一级目录
cd ../..                # 返回上上级目录
ls                       # 查看当前目录下的文件
ls -l                    # 以长格式显示（详细信息）
ls -a                    # 显示隐藏文件（以.开头）
ls -lh                   # 文件大小友好显示（K/M/G）
ls -lt                   # 按修改时间排序
ls /etc                  # 查看指定目录内容
mkdir newdir                         # 创建目录
mkdir -p /path/to/new/subdir         # 创建带多级路径的目录（必要时递归创建）
rmdir emptydir                        # 删除空目录
rm -r dir                             # 删除目录及其内容
rm -rf dir                            # 强制删除目录及内容（⚠️ 注意危险！）
cp -r source_dir target_dir
cp -r /home/user/docs /backup/docs_copy
mv olddir newdir
mv dir1 /tmp/dir2                     # 移动到新路径
```

### `文件操作`

```sh
find / -name file.txt                 # 按名称查找
find . -type f -name "*.log"          # 查找当前目录下所有 .log 文件
find /var -size +100M                 # 查找大于100MB的文件
find /etc -mtime -1                   # 最近一天修改的文件
find /tmp -user root -perm 644        # 带权限条件
grep "keyword" file.txt
grep -r "error" /var/log/
grep -i "warning" file.log     # 忽略大小写
grep -n "test" file.txt        # 显示行号
grep -v "DEBUG" file.log       # 排除包含 DEBUG 的行



```


### [[expect脚本用法]]

### [[netstat]]

### [[vim常用命令]]
