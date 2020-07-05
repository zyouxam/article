# 简介

支持数据生成程序模式, 只要有RE或者WA的数据点, 就会停止

支持数据文件模式, 使用通配符指定输入文件, 将会对拍所有文件

结束后将会打印统计信息

第一次在某目录执行,将会通过交互方式获取配置, 生成配置文件, 以后读取配置文件运行

支持Linux和Windows系统

# 代码

```python
#!/usr/bin/python3
import os
import json
import platform
import time
import glob
import shutil


def confirm(msg, helpi=''):
    while True:
        cmd = input("{} [yes]/no{} : ".format(msg, "/help" if helpi else ''))
        if cmd == "no":
            return False
        elif cmd == "help" and helpi:
            print(helpi)
        else:
            return True


def getinput(msg, helpi=''):
    while True:
        cmd = input("{} {} : ".format(msg, "<input>/help" if helpi else ''))
        if cmd == "help" and helpi:
            print()
            print(helpi)
            print()
        elif cmd:
            return cmd


settings = {}
if not os.path.isfile("checkx.json"):
    try:
        print("未找到checkx.json配置文件\n")
        settings['std'] = getinput(
            "标准程序源代码路径", "1. 不需要使用freopen\n2. 可以使用绝对路径或相对路径")
        settings['target'] = getinput(
            "测试程序源代码路径", "1. 不需要使用freopen\n2. 可以使用绝对路径或相对路径")
        settings['ifpro'] = confirm(
            "是否使用数据生成程序?", "若输入yes, 将会使用数据生成程序生成数据\n若输入no,  将会使用数据文件")
        if settings['ifpro']:
            settings['pro'] = getinput("数据生成程序路径", "源程序应该直接输出数据")
        else:
            settings['file'] = getinput(
                "数据文件路径", "1. 可以使用绝对路径或相对路径\n2. 支持通配符")
        with open("checkx.json", "w") as f:
            f.write(json.dumps(settings))
    except KeyboardInterrupt:
        exit(0)
else:
    with open("checkx.json", "r") as f:
        settings = json.loads(f.read())
print("正在编译标准程序...", end='')
os.system("g++ {} -DDEBUG -o {}.out".format(settings['std'], settings['std']))
print("编译完成\n正在编译测试程序...", end='')
os.system(
    "g++ {} -DDEBUG -o {}.out".format(settings['target'], settings['target']))
print("编译完成")
if "Windows" in platform.platform():
    diffcmd = "fc /w target.out std.out > diff.out"
else:
    diffcmd = "diff -ZE target.out std.out > diff.out"
count = 0
times = []
stdtimes = []
account = 0
if settings['ifpro']:
    try:
        while True:
            count += 1
            os.system(".{}{} > in.in".format(os.sep, settings['pro']))
            zerotime = time.time()
            os.system(".{}{} < in.in > std.out".format(os.sep, settings['std']+".out"))
            begin = time.time()
            stdtimes.append(begin-zerotime)
            if not os.system("{} < in.in > target.out".format(settings['target']+".out")):
                print()
                print("# {} Runtime Error".format(count))
                print("\n输入:\n")
                with open("in.in", "r") as f:
                    print(f.read())
                break
            end = time.time()
            times.append(end-begin)
            if os.system(diffcmd):
                print()
                print("Wrong Answer")
                print("\n输入:\n")
                with open("in.in", "r") as f:
                    print(f.read())
                print("\n标准程序输出:\n")
                with open("std.out", "r") as f:
                    print(f.read())
                print("\n测试程序输出:\n")
                with open("target.out", "r") as f:
                    print(f.read())
                print("\n差异比较:\n")
                with open("diff.out", "r") as f:
                    print(f.read())
                break
            else:
                account += 1
                print("\r# {} Accepted {}s".format(
                    count, round(end-begin, 2)), end='')
    except KeyboardInterrupt:
        pass
else:
    try:
        for i in glob.glob(settings['file']):
            count += 1
            zerotime = time.time()
            os.system(".{}{} < {} > std.out".format(os.sep, settings['std']+".out", i))
            begin = time.time()
            stdtimes.append(begin-zerotime)
            if not os.system(".{}{} < {} > target.out".format(os.sep, settings['target']+".out", i)):
                print("# {} Runtime Error".format(count))
                print("\n输入保存在文件 result{}.in\n".format(count))
                shutil.copy(i, "result{}.in".format(count))
                continue
            end = time.time()
            times.append(end-begin)
            if os.system(diffcmd):
                print("# {} Wrong Answer".format(count))
                print("\n输入保存在文件result{}.in\n".format(count))
                shutil.copy(i, "result{}.in".format(count))
                print("\n标准程序输出保存在文件 result{}.ans\n".format(count))
                shutil.copy("std.out", "result{}.out".format(count))
                print("\n测试程序输出保存在文件 result{}.out\n".format(count))
                shutil.copy("target.out", "result{}.ans".format(count))
                print("\n差异比较保存在文件 result{}.diff\n".format(count))
                shutil.copy("diff.out", "result{}.diff".format(count))
            else:
                account += 1
                print("# {} Accepted {}s".format(count, round(end-begin, 2)))
    except KeyboardInterrupt:
        pass
print("\n共测试 {} 个数据点".format(count))
if count:
    print("{} 个数据点正确".format(account))
    print("正确率 {} %".format(round(account/count*100, 2)))
    if count:
        print("标准程序在正确的数据点中:\n\t平均时间 {} s".format(round(sum(stdtimes)/count, 2)))
    print("\t最长时间 {} s".format(round(max(stdtimes), 2)))
    print("\t最短时间 {} s".format(round(min(stdtimes), 2)))
    if account:
        print("测试程序在正确的数据点中:\n\t平均时间 {} s".format(round(sum(times)/account, 2)))
        print("\t最长时间 {} s".format(round(max(times), 2)))
        print("\t最短时间 {} s".format(round(min(times), 2)))
os.remove("diff.out")
os.remove("std.out")
os.remove("target.out")
os.remove(settings['std']+".out")
os.remove(settings['target']+".out")
if settings['ifpro']:
    os.remove("in.in")
```
