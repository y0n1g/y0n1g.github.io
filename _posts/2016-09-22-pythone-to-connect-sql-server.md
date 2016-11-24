---
layout: post
title: 使用python访问SQL Server数据库
category: [python, mssql]
---
{% include JB/setup %}


# 工具
需要使用pypyodbc，安装很简单：
pip install pypyodbc

# 连接
import pypyodbc
connection = pypyodbc.connect(
    '''
    Server=db.yoursite.com;
    Database=your_database_name;
    Driver={SQL Server};
    uid=user_name_to_login;
    pwd=password_to_login;
    ''')
cursor = connection.cursor()

注意，pypyodbc提到了与pyodbc兼容，pyodbc的连接字符串是：https://code.google.com/p/pyodbc/wiki/ConnectionStrings


# 获取数据 SQL语句就是一个长的字符串
SQLCommand = (
    '''
    SELECT *
    FROM Your_DB.Schame_name.Table_name
    WHERE some_column_a = "y0n1g"
    order by some_column_b
    ''')
cursor.execute(SQLCommand)

# 处理数据
results = cursor.fetchone()
while results:
    process_function(results)
    results = cursor.fetchone()
