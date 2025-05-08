# glog
参考链接：
https://blog.csdn.net/qq_37805392/article/details/139756586

使用新版本会报错，使用0.6.0版本没问题
https://github.com/google/glog/releases/tag/v0.6.0

记录一下安装位置
```
taihe@thpc:~/wang/glog/glog_install/glog-0.6.0/build$ sudo make install
[sudo] password for taihe: 
[ 22%] Built target glogbase
[ 25%] Built target glogtest
[ 32%] Built target cleanup_with_absolute_prefix_unittest
[ 38%] Built target cleanup_immediately_unittest
[ 45%] Built target signalhandler_unittest
[ 51%] Built target utilities_unittest
[ 58%] Built target demangle_unittest
[ 64%] Built target symbolize_unittest
[ 70%] Built target logging_custom_prefix_unittest
[ 77%] Built target logging_unittest
[ 83%] Built target stacktrace_unittest
[ 90%] Built target cleanup_with_relative_prefix_unittest
[ 96%] Built target stl_logging_unittest
[100%] Built target glog
Install the project...
-- Install configuration: ""
-- Installing: /usr/local/lib/libglog.so.0.6.0
-- Installing: /usr/local/lib/libglog.so.1
-- Installing: /usr/local/lib/libglog.so
-- Installing: /usr/local/include/glog/export.h
-- Installing: /usr/local/include/glog/logging.h
-- Installing: /usr/local/include/glog/raw_logging.h
-- Installing: /usr/local/include/glog/stl_logging.h
-- Installing: /usr/local/include/glog/vlog_is_on.h
-- Installing: /usr/local/include/glog/log_severity.h
-- Installing: /usr/local/include/glog/platform.h
-- Installing: /usr/local/lib/pkgconfig/libglog.pc
-- Installing: /usr/local/lib/cmake/glog/glog-modules.cmake
-- Installing: /usr/local/lib/cmake/glog/glog-config.cmake
-- Installing: /usr/local/lib/cmake/glog/glog-config-version.cmake
-- Installing: /usr/local/lib/cmake/glog/glog-targets.cmake
-- Installing: /usr/local/lib/cmake/glog/glog-targets-noconfig.cmake

```

执行sudo make install之后记得执行
```
sudo ldconfig
```

使用参考代码，产生的日志文件每1m存储为一个文件
```
//初始化
   void InitLog()
    {
        // Start google log system:
        FLAGS_log_dir = "/root/taihe/runtime_dir/log/";
        google::InitGoogleLogging("image_detect");
        google::SetStderrLogging(google::GLOG_INFO);
        FLAGS_max_log_size = 1;   // 每个日志文件最大为 1 MB
        FLAGS_logbufsecs = 0;  // 禁用日志缓冲
        FLAGS_stop_logging_if_full_disk = true;  // 磁盘满时停止写日志
        FLAGS_colorlogtostderr = true;
        google::InstallFailureSignalHandler();
        // // 默认捕捉 SIGSEGV 信号信息输出会输出到 stderr，可以通过下面的方法自定义输出方式：
        // google::InstallFailureWriter(&SignalHandle); // 这里应该使用正确的函数签名
    }

//使用
LOG(INFO) << "warning!!!!  Occupied";
```

cmake,在原代码基础上添加两行即可（注意直接在相应代码行添加，不用修改代码）
```
find_package(glog REQUIRED)
target_link_libraries(image_detect glog::glog)
```



## 如何管理日志，定期删除？
使用Linux的cron服务来定期执行清理脚本

### 1.创建清理脚本
创建一个脚本文件（例如clean_logs.sh），并在其中编写清理日志文件的逻辑。以下是脚本内容：
```
#!/bin/bash

# 定义日志目录
LOG_DIR="/root/taihe/runtime_dir/log"

# 定义要保留的日志文件数量（例如保留最新的10个文件）
KEEP_FILES=10

# 查找并删除旧的日志文件
find "$LOG_DIR" -type f -name "*.log" -exec ls -t {} + | tail -n +$(($KEEP_FILES + 1)) | xargs rm -f

# 或者，按文件修改时间删除超过一定天数的日志文件（例如保留最近7天的日志）
# find "$LOG_DIR" -type f -name "*.log" -mtime +7 -exec rm -f {} \;
```

### 2.赋予脚本执行权限
```
chmod +x /root/taihe/launch/sh/clean_logs.sh
```

### 3.设置定时任务
使用cron来定期执行这个脚本。以下是设置方法：
安装cron
```
sudo apt install cron
```

编辑
```
crontab -e
```
```
taihe@thpc:~$ crontab -e
no crontab for taihe - using an empty one

Select an editor.  To change later, run 'select-editor'.
  1. /bin/nano        <---- easiest
  2. /usr/bin/vim.basic
  3. /usr/bin/vim.tiny
  4. /usr/bin/code
  5. /bin/ed

Choose 1-5 [1]: 
```
输入1选择nano（推荐新手使用，因为它简单易用）。

编辑脚本任务
```
# 每天凌晨2点执行清理脚本
0 2 * * * /root/taihe/launch/sh/clean_logs.sh
```
0：表示分钟，这里设置为0，即每小时的第0分钟（也就是整点）。  
13：表示小时，这里设置为13点。* * *：分别表示每天、每月和每年，三个*表示每天执行。

### 4. 查看定时任务
```
crontab -l
```