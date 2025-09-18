# <center>Apache安装及虚拟主机设置</center>

## 一. 下载 Apache  

1. 访问官网:打开 [Apache 服务器官网]( http://httpd.apache.org)。  

2. 选择下载:在官网首页找到 "Download" 部分并点击进入。  
![1.1.Download.png](https://github.com/Huang-823/Apache-installation-and-virtual-hosts-setup/blob/main/PicFiles/1.1.Download.png)

1. 选择版本:  
    - 在下载页面，找到 "`Files for Microsoft Windows`" 选项并点击。  

    - Apache 官网本身不直接提供预编译的 Windows 二进制文件，而是会列出一些第三方提供的站点（如 `Apache Lounge` 或 `Apache Haus`）。 请从这些推荐的第三方站点下载。  
    ![1.2.Download.png](https://github.com/Huang-823/Apache-installation-and-virtual-hosts-setup/blob/main/PicFiles/1.2.Download.png)  

    - 选择适合你 Windows 系统位数（通常是 64位，x64）的版本进行下载。 注意查看版本所需的 Visual Studio 运行时（如 VC9, VC11, VC14等），这关系到系统是否有所需的运行库。  
    ![1.3.Download.png](https://github.com/Huang-823/Apache-installation-and-virtual-hosts-setup/blob/main/PicFiles/1.3.Download.png)  

2. 解压文件：下载完成后，将压缩包解压到你希望的安装目录，例如 `C:\Apache24`  

## 二. 安装Apache  

### 修改配置文件  

1. 进入解压后的目录（例如 `C:\Apache24\conf`），用文本编辑器打开 `httpd.conf` 文件。  

2. 找到 `Define SRVROOT` 这一行，将其值修改为你的 Apache 解压目录（例如 `Define SRVROOT "C:/Apache24"`）。这是关键一步，否则启动会报错。  
![2.1.install.png](https://github.com/Huang-823/Apache-installation-and-virtual-hosts-setup/blob/main/PicFiles/2.1.install.png)
3. 如果默认的 80 端口被其他程序（如 IIS）占用，你可以修改 Listen 指令，例如改为 Listen 8080。（可选）  
![2.2.install.png](https://github.com/Huang-823/Apache-installation-and-virtual-hosts-setup/blob/main/PicFiles/2.2.install.png)
4. 找到 ``#ServerName www.example.com:80``，去掉前面的注释 ``#``，并修改为 ``ServerName localhost:80``（如果你改了端口，这里也要同步）。  
![2.3.install.png](https://github.com/Huang-823/Apache-installation-and-virtual-hosts-setup/blob/main/PicFiles/2.3.install.png)

### 安装 Apache 服务  

1. 以管理员身份打开命令提示符 `Cmd` 或 `PowerShell`。  
![2.4.install.png](https://github.com/Huang-823/Apache-installation-and-virtual-hosts-setup/blob/main/PicFiles/2.4.install.png)
2. 切换到 Apache 的 `bin` 目录（例如 `cd C:\Apache24\bin`）。  

3. 输入命令 ``httpd.exe -k install`` 并回车来安装 Apache 服务。  
![2.5.install.png](https://github.com/Huang-823/Apache-installation-and-virtual-hosts-setup/blob/main/PicFiles/2.5.install.png)  

### 启动 Apache  

1. 安装成功后，在刚才的 bin 目录下输入命令 ``net start Apache2.4``（服务名可能因版本略有不同，请按提示操作）来启动服务。  

2. 或者，你可以找到并运行 bin 目录下的 ``ApacheMonitor.exe``，然后在系统托盘的监控器中启动服务。  

### 验证安装  

1. 打开浏览器，访问 ``http://localhost``（如果你修改了端口，记得加上端口号，如 ``http://localhost:8080``）。

2. 如果看到 Apache 的默认页面（如 ``"It works!"``或有关 ``Apache Haus`` 的提示），说明安装成功。  

### Apache 服务管理  

- 安装成功后，你可以通过以下命令管理 Apache 服务（均需在 管理员身份 的命令提示符下，进入 bin 目录后操作）：

|功能|命令|
|:---|:---|
|启动 Apache|``httpd.exe -k start``|
|停止 Apache|``httpd.exe -k stop``|
|重启 Apache|``httpd.exe -k restart``|
|卸载服务|``httpd.exe -k uninstall``|

## 三. Apache虚拟主机  

### 启用虚拟主机功能  

首先需要编辑主配置文件 ``httpd.conf``：  

- 找到并取消以下行的注释（删除前面的 #）：
![3.1.virtual-host.png](https://github.com/Huang-823/Apache-installation-and-virtual-hosts-setup/blob/main/PicFiles/3.1.virtual-host.png)

- 确保以下模块也已启用：
![3.2.virtual-host.png](https://github.com/Huang-823/Apache-installation-and-virtual-hosts-setup/blob/main/PicFiles/3.2.virtual-host.png)

### 配置虚拟主机文件  

主要编辑 ``conf/extra/httpd-vhosts.conf`` 文件：  

*基本虚拟主机配置模板*  

``` apache
# 必须的默认虚拟主机，捕获所有未匹配的请求
<VirtualHost *:8080>
    ServerName localhost
    DocumentRoot "D:/learning/apache24/htdocs"
    <Directory "D:/learning/apache24/htdocs">
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>
</VirtualHost>

# 第一个虚拟主机
<VirtualHost *:8080>
    ServerAdmin webmaster@vc.local

    # 指定网站文件的实际路径
    DocumentRoot "D:/learning/apache24/docs/vc.local"

    # 指定域名（将在hosts文件中映射）
    ServerName vc.local
    ServerAlias www.vc.local

    # 错误日志和访问日志（可选但推荐）
    ErrorLog "logs/vc.local-error.log"
    CustomLog "logs/vc.local-access.log" common

    # 目录权限设置
    <Directory "D:/learning/apache24/docs/vc.local">
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>
</VirtualHost>

# 第二个虚拟主机
<VirtualHost *:8080>
    ServerAdmin webmaster@dummy-host2.example.com
    DocumentRoot "D:/learning/apache24/docs/dummy-host2.example.com"
    ServerName dummy-host2.example.com
    ErrorLog "logs/dummy-host2.example.com-error.log"
    CustomLog "logs/dummy-host2.example.com-access.log" common
    
    <Directory "D:/learning/apache24/docs/dummy-host2.example.com">
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>
</VirtualHost>
```

其中，`vc.local` 和 `dummy-host2.example.com`分别为两个虚拟主机的名称，配置虚拟主机时更改其便可，不需改动模板。  

### 配置本地 hosts 文件  

为了让系统能够识别 project1.test 这样的自定义域名，需要修改 hosts 文件：

1. 用管理员身份打开记事本  

2. 通过记事本打开文件：``C:\Windows\System32\drivers\etc\hosts``  

3. 在文件末尾添加以下行：  
![3.3.virtual-host.png](https://github.com/Huang-823/Apache-installation-and-virtual-hosts-setup/blob/main/PicFiles/3.3.virtual-host.png)  

### 重启 Apache 并测试  

在`bin`目录测试

|功能|cmd语法|
|:---|:---|
|测试配置文件语法|`httpd -t`|
|重启 Apache 服务|`httpd -k restart`|

## 四. 常见不能正常配置错误

1. 端口冲突  
   确认您使用的端口，并确认确认没有其他程序占用此端口
   `netstat -ano | findstr ":8080"`
2. 防火墙阻止  
   Windows防火墙可能仍然阻止对8080端口的访问。确保已为Apache创建正确的入站规则。
   ![4.1.error-check.png](https://github.com/Huang-823/Apache-installation-and-virtual-hosts-setup/blob/main/PicFiles/4.1.error-check.png)
   ![4.2.error-check.png](https://github.com/Huang-823/Apache-installation-and-virtual-hosts-setup/blob/main/PicFiles/4.2.error-check.png)
   ![4.3.error-check.png](https://github.com/Huang-823/Apache-installation-and-virtual-hosts-setup/blob/main/PicFiles/4.3.error-check.png)
   后续操作默认即可  
3. 浏览器缓存问题  
   浏览器可能会缓存DNS查询结果。尝试：

    - 使用浏览器隐私/无痕模式访问

    - 清除浏览器缓存和DNS缓存

    - 在命令提示符中刷新DNS缓存：`ipconfig /flushdns`

