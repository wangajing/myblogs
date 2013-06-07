如果你想从机器A登录到B，不用输入密码：
1) 在A机器上输入： ssh-keygen， 会生成密钥和公钥
2) 将生成的公钥文件copy到B机器上：ssh ssh-copy-id -i id_rsa.pub name@hostname
应该已经可以work 了。
