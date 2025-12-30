
### 脚本文件写法

以下是expect脚本文件

将上述文件vim保存成一个expect-example.exp

在shell中执行expect脚本文件

```sh
#!/usr/bin/bash 
expect expect-example.exp 
echo "exec exp success!"
```


### shell脚本嵌套expect脚本（常用）

以下是一个使用shell嵌套expect脚本生成ssh公私钥的过程
```sh
#!/usr/bin/bash 
expect<<EOF 
set timeout -1 
spawn ssh-keygen 
expect "Enter file in which to save the key" 
send /Users/wangtaiping/.ssh/test/bbb_rsa\r 
after 3000

expect "Enter passphrase" 
send \r 
after 3000 

expect -re "again" 
send \r 
after 3000 
expect eof 
EOF
```