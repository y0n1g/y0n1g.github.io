---
title: 在linux上配置python访问MS SQL Server
category: [linux, python, database]
layout: post
---
{% include JB/setup %}

## 问题:

# 安装unixODBC
unixODBC下载后,可以直接.configure配置编译.但是按照mssqldriver的说明文档,应该用以下参数行编译:

    ./configure --prefix=/usr --libdir=/usr/lib64 --sysconfdir=/etc --enable-gui=no --enable-drivers=no --enable-iconv --with-iconv-char-enc=UTF8 --with-iconv-ucode-enc=UTF16LE 


# [安装驱动](https://msdn.microsoft.com/en-us/library/hh568454(v=sql.110).aspx) msodbcsql-11.0.2260.0.tar.gz
这个包从微软官网下载,用于SLES11 SP2. 由于是编译好的,安装过程就是检测unixODBC的环境,将程序拷贝到相应位置并且创建链接.
安装完毕后,可以执行sqlcmd连接sql server服务器测试是否可以正常使用.

# 在unixodbc中配置驱动,[参考](http://www.unixodbc.org/odbcinst.html)
系统文件:odbcinst.ini - 包含了对所有用户可用的ODBC驱动信息. 我的配置是:

    [MSSQL]
    Description = Microsoft ODBC Driver 11 for SQL Server
    Driver = /opt/microsoft/msodbcsql/lib64/libmsodbcsql-11.0.so
    Threading = 1
    UsageCount = 1
    
系统文件:odbc.ini - 包含了对所有用户可用的DSN(Database Source Name)信息

    [my_data_base]
    Description = Microsoft ODBC Driver 11 for SQL Server
    Driver = MSSQL
    Port = 1433
    


# 为什么odbc.ini和odbcinst.ini都配置了iql还是无法找到驱动?
如果iql和odbcconfig的版本不同,有可能在编译时配置的路径不同.会导致错误.SLES默认安装的可能是unixodbc是2.3.0 如果升级到unixodbc2.3.1,并且编译时使用了不同的安装路径,并且链接时没有把旧版本彻底清除,就可能导致这个问题.

# 为什么使用pypyodbc还是提示找不到驱动?
替换成pyodbc后找到了. 但是这不能说明问题. 因为在替换过程中,连接的字符串也变了.
原来是

    connection = pypyodbc.connect(
    '''
    Driver={MSSQL};
    Server=serverip;
    Database=dbname;
    pwd=password;
    uid=username;
    '''
    )

后来替换成能工作的:

    connection = pyodbc.connect('DRIVER={MSSQL};SERVER=serverip;PORT=1433;DATABASE=dbname;UID=username;PWD=password')

注意,上述能工作的版本中, 如果加入空格,都会产生错误.比如,这样的也不工作:

    connection = pyodbc.connect('DRIVER={MSSQL}; SERVER=serverip; PORT=1433; DATABASE=dbname; UID=username; PWD=password')

(http://richbs.org/post/43142767072/connecting-to-microsoft-sql-server-from-unix)
