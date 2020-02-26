# oneindex-auto
简介
OneDriveUploader是一个基于GoLang编写的OneDrive上传工具，支持在Unix及Windows下使用。支持使用命令一键上传文件到OneDrive网盘中，配合Aria2能够实现把OneDrive当成大容量极速离线网盘的效果。甚至还不限制文件内容，不会出现X度网盘教育片及X雷违规的情况:laughing:。

特点
支持 国际版, 个人版(家庭版), 中国版(世纪互联).
支持上传文件和文件夹到指定目录,并保持上传前的目录结构.
支持命令参数使用, 方便外部程序调用.
支持自定义上传分块大小.
支持多线程上传(多文件同时上传).
支持根据文件大小动态调整重试次数.
支持跳过网盘中已存在的同名文件.
安装
这里一Linux系统为例
通过SSH终端输入以下命令

#64位系统下载
wget https://raw.githubusercontent.com/MoeClub/OneList/master/OneDriveUploader/amd64/linux/OneDriveUploader -P /usr/local/bin/
#32位系统下载
wget https://raw.githubusercontent.com/MoeClub/OneList/master/OneDriveUploader/i386/linux/OneDriveUploader -P /usr/local/bin/
#arm架构下载
wget https://raw.githubusercontent.com/MoeClub/OneList/master/OneDriveUploader/arm/linux/OneDriveUploader -P /usr/local/bin/
增加运行权限
chmod +x /usr/local/bin/OneDriveUploader

初始化
获取OneDrive授权token
根据网盘版本的不同，请分别用浏览器访问以下两个网址登录获取授权

国际版, 个人版(家庭版)
https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=78d4dc35-7e46-42c6-9023-2d39314433a5&response_type=code&redirect_uri=http://localhost/onedrive-login&response_mode=query&scope=offline_access%20User.Read%20Files.ReadWrite.All


中国版(世纪互联)
https://login.chinacloudapi.cn/common/oauth2/v2.0/authorize?client_id=dfe36e60-6133-48cf-869f-4d15b8354769&response_type=code&redirect_uri=http://localhost/OneDrive-login&response_mode=query&scope=offline_access%20User.Read%20Files.ReadWrite.All
登录帐号后会在浏览器地址框里返回一个以localhost开头的地址，需要把整个链接复制下来。

配置OneDriveUploader
# 国际版
OneDriveUploader -a "url"
# 个人版(家庭版)
OneDriveUploader -ms -a "url"
# 中国版(世纪互联)
OneDriveUploader -cn -a "url"

# 在浏览器地址栏中获取以 http://loaclhost 开头的整个url内容
# 将获取的完整url内容替换命令中的 url 三个字母
# 每次产生的 url 只能用一次, 重试请重新获取 url
# 此操作将会自动初始化的配置文件
# 提示 Init config file: '/path/to/auth.json' 则成功
使用
这里列出了常用的一些命令

Usage of OneDriveUploader:
  -a string
        // 初始化授权
        Setup and Init auth.json.
  -b string
        // 自定义上传分块大小, 可以提高网络吞吐量, 受限于磁盘性能和网络速度.
        Set block size. [Unit: M; 5<=b<=60;] (default "10")
  -c string
        // 配置文件路径
        Config file. (default "auth.json")
  -n string
        // 上传单个文件时,在网盘中重命名
        Rename file on upload to remote.
  -r string
        // 上传到网盘中的某个目录, 默认: 根目录
        Upload to reomte path.
  -s string
        // *必要参数, 要上传的文件或文件夹
        Upload item.
  -t string
        // 线程数, 同时上传文件的个数. 默认: 2
        Set thread num. (default "2")
  -f
        // 开关(推荐)
        // 加上 -f 参数，强制读取 auth.json 中的块大小配置和多线程配置.
        // 不加 -f 参数, 每次覆盖保存当前使用参数到 auth.json 配置文件中.
        Force Read config form config file. [BlockSize, ThreadNum]
  -skip
        // 开关
        // 跳过上传网盘中已存在的同名文件. (默认不跳过)
        Skip exist file on remote.
  -cn
        // 开关
        // 授权中国版(世纪互联), 需要此参数.
        OneDrive by 21Vianet.
  -ms
        // 开关
        // 授权个人版(家庭版), 需要此参数.
        OneDrive by Microsoft.
配置
{
    // 授权令牌
    "RefreshToken": "1234564567890ABCDEF",
    // 最大线程数.(同时上传文件的数量)
    "ThreadNum": "2",
    // 最大上传分块大小.(每次上传文件的最大分块大小,网络不好建议调低. 单位:MB)
    "BlockSize": "10",
    // 最大单文件大小.(目前: 个人版(家庭版)单文件限制为100GB; 其他版本单文件限制为15GB,微软将逐步更新为100GB. 单位:GB)
    "SigleFile": "100",
    // 缓存刷新间隔.
    "RefreshInterval": 1500,
    // 如果是中国版(世纪互联), 此项应为 true.
    "MainLand": false,
    // 如果是家庭版或者个人免费版, 此项应为 true.
    "MSAccount": true
}
使用示例
# 一些示例:
# 将同目录下的 mm00.jpg 文件上传到 OneDrive 网盘根目录
OneDriveUploader -s "mm00.jpg"

# 将同目录下的 mm00.jpg 文件上传到 OneDrive 网盘根目录,并改名为 mm01.jpg
OneDriveUploader -s "mm00.jpg" -n "mm01.jpg"

# 将同目录下的 Download 文件夹上传到 OneDrive 网盘根目录
OneDriveUploader -s "Download" 

# 将同目录下的 Download 文件夹上传到 OneDrive 网盘Test目录中
OneDriveUploader -s "Download" -r "Test"

# 将同目录下的 Download 文件夹上传到 OneDrive 网盘Test目录中, 使用 10 线程
OneDriveUploader -t 10 -s "Download" -r "Test"

# 将同目录下的 Download 文件夹上传到 OneDrive 网盘Test目录中, 使用 15 线程, 并设置分块大小为 20M
OneDriveUploader -t 15 -b 20 -s "Download" -r "Test"

# 将同目录下的 Download 文件夹上传到 OneDrive 网盘Test目录中, 使用配置文件中的线程参数和分块大小参数
OneDriveUploader -f -c "/urs/local/auth.json" -s "Download" -r "Test"

# 将同目录下的 Download 文件夹上传到 OneDrive 网盘Test目录中, 使用配置文件中的线程参数和分块大小参数，并跳过上传网盘中已存在的同名文件
OneDriveUploader -f -c "/urs/local/auth.json" -skip -s "Download" -r "Test"
注意事项
多次尝试后, 无失败的上传文件. 退出码为 0 .
最终还有失败的上传文件会详细列出上传失败项. 退出码为 1.
更多信息请访问Github项目

https://github.com/MoeClub/OneList/tree/master/OneDriveUploader
Windows及mac OS
使用方法近乎一致

#下载地址
https://github.com/MoeClub/OneList/tree/master/OneDriveUploader/amd64
总结
OneDriveUploader配合OneIndex及Aria2是实现将OneDrive当成离线网盘的一大利器之一，具体的操作方法我会在以后的文章详细介绍。

