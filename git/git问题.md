### git问题记录

 # Git SSL 证书报错解决方案
错误提示：`SSL certificate OpenSSL verify result: unable to get local issuer certificate (20)`

该报错表示 Git 通过 HTTPS 连接 GitHub 时，**无法验证服务器 SSL 证书**。一般是本地缺少根证书，或是公司/校园网等环境存在流量拦截、代理导致。

建议按顺序尝试以下解决方案：

## 方案一：配置 Git 使用系统证书后端（推荐）
最安全有效的方案，优先推荐 Windows 用户使用，让 Git 调用系统自带证书库。

打开终端 / Git Bash，执行命令：
```bash
git config --global http.sslBackend schannel

执行完成后，重新尝试提交、推送代码即可。

## 方案二：临时关闭 SSL 验证（仅用于测试）
紧急场景下临时使用，会降低安全性，不建议长期开启。

关闭 SSL 验证
```bash
git config --global http.sslVerify false

恢复 SSL 验证（操作完成后务必开启）
```bash
git config --global http.sslVerify true

## 方案三：更新 / 指定 Git CA 证书包
若 Git 自带证书过期、缺失，手动指定证书文件路径。
假设 Git 安装在 `C:\Program Files\Git`，执行以下命令：
```bash
git config --global http.sslCAInfo "C:/Program Files/Git/mingw64/ssl/certs/ca-bundle.crt"

可根据自身 Git 实际安装路径修改地址。

## 方案四：检查网络代理设置
代理工具（Clash、V2Ray 等）会替换证书链，引发验证失败。

    临时关闭代理软件，重试连接；
    如需持续使用代理，配置 Git 代理（替换为你实际代理端口）：
```bash
# 配置 HTTP/HTTPS 代理
git config --global http.proxy http://127.0.0.1:7890
git config --global https.proxy http://127.0.0.1:7890

以上即为解决该 Git SSL 报错的四种常用方案。