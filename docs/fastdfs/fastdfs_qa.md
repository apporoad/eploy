## 错误

安装配置nginx与fastdfs模块

[root@fastdfs_1 fdfs]# cd ~/nginx-1.10.1
[root@fastdfs_1 nginx-1.10.1]# ./configure --prefix=/servers/nginx  --with-http_stub_status_module --with-http_ssl_module --with-http_realip_module  --add-module=/root/fastdfs-nginx-module/src/ --user=www --group=www
[root@fastdfs_1 nginx-1.10.1]# make && make install

 

\############################如果出现以下这个错误：############################

/root/fastdfs-nginx-module/src//common.c:21:25: 致命错误：fdfs_define.h：没有那个文件或目录
 \#include "fdfs_define.h"
                         ^
编译中断。
make[1]: *** [objs/addon/src/ngx_http_fastdfs_module.o] 错误 1
make[1]: 离开目录“/root/nginx-1.10.1”
make: *** [build] 错误 2

 

说明添加fastdfs模块出错，可以更改fastdfs-nginx-module/src/config这个文件

[root@fastdfs_1 nginx-1.10.1]# vim ~/fastdfs-nginx-module/src/config

更改后内容为：

ngx_addon_name=ngx_http_fastdfs_module
HTTP_MODULES="$HTTP_MODULES ngx_http_fastdfs_module"
NGX_ADDON_SRCS="$NGX_ADDON_SRCS $ngx_addon_dir/ngx_http_fastdfs_module.c"
CORE_INCS="$CORE_INCS /usr/include/fastdfs /usr/include/fastcommon/"
CORE_LIBS="$CORE_LIBS -L/usr/lib -lfastcommon -lfdfsclient"
CFLAGS="$CFLAGS -D_FILE_OFFSET_BITS=64 -DFDFS_OUTPUT_CHUNK_SIZE='256*1024' -DFDFS_MOD_CONF_FILENAME='\"/etc/fdfs/mod_fastdfs.conf\"'"

 

然后再进行上面的两个步骤：

[root@fastdfs_1 nginx-1.10.1]# ./configure --prefix=/servers/nginx  --with-http_stub_status_module --with-http_ssl_module --with-http_realip_module  --add-module=/root/fastdfs-nginx-module/src/ --user=www --group=www
[root@fastdfs_1 nginx-1.10.1]# make && make install





### 错误



c/http -I src/http/modules -I src/stream \
        -o objs/addon/src/ngx_http_fastdfs_module.o \
        /usr/local/src/fastdfs-nginx-module/src/ngx_http_fastdfs_module.c
In file included from /usr/local/src/fastdfs-nginx-module/src/ngx_http_fastdfs_module.c:6:0:
/usr/local/src/fastdfs-nginx-module/src/common.c: 在函数‘fdfs_http_request_handler’中:
/usr/local/src/fastdfs-nginx-module/src/common.c:1245:61: 错误：‘FDFSHTTPParams’没有名为‘support_multi_range’的成员
                 (pContext->range_count > 1 && !g_http_params.support_multi_range))



修改/root/fastdfs-nginx-module-master/src/common.c第1245行

把（pContext-> range_count> 1 &&！g_http_params.support_multi_range））最后面的条件

去掉了改成（pContext-> range_count > 1））





## 错误

ERROR - file: ini_file_reader.c, line: 631, include file "http.conf" not exists, line: "#include http.conf"

ERROR - file: /home/packages/fastdfs-nginx-module-master/src/common.c, line: 163, load conf file "/etc/fdfs/mod_fastdfs.conf" fail, ret code: 2



解决：# cp /home/fastdfs-5.11/conf/http.conf /etc/fdfs/

 ERROR - file: shared_func.c, line: 968, file /etc/fdfs/mime.types not exist

解决：# cp /home/fastdfs-5.11/conf/mime.types /etc/fdfs/



ps: 通过官方源代码下载方式解决