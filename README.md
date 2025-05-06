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

如何管理日志，限制日志文件大小，定期删除？
参考：https://cloud.tencent.com/developer/article/2438756