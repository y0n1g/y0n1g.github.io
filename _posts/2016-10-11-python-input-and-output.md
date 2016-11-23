---
layout: post
title: Python 输入输出
category: python
---
{% include JB/setup %}
# 输入
## 命令行输入: getopt

**先引用**

    import getopt

**获取参数列表**

    try:
        #Any option with value, should be like: c:  t:
        #Any option without value, should be like  h d
        opts, args = getopt.getopt(argv,"hdc:t:",["cycle=","team=","debug="])
    except getopt.GetoptError:
        print 'getopt.GetoptError' # 当参数输入个数不对时，直接异常跳到这里

其中，opts是根据支持的参数列表(hdct)返回的参数对。其中，h/d是不带参数值的开关；c/t是需要指定参数的，这种情况需要后跟':'


**解析参数列表**

    for opt, arg in opts:
        if opt == '-h':
            print 'help message'
            sys.exit()
        elif opt in ("-c", "--cycle"):
            if arg in cycle_list:
                cycle = arg
                continue
            else:
                print "-c/--cycle ONLY support" + str(cycle_list)
                sys.exit()
        ...



## 交互式输入： raw_input()

    user = raw_input('Enter login name: ')

# 输出：
## print()
使用方法跟C的print类似.

    print '%f miles is the same as %f km' % (miles, kilometers)
    1000.000000 miles is the same as 1609.000000 km


