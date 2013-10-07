---
author: illidan
comments: true
date: 2011-11-18 09:41:54+00:00
layout: post
slug: linux-dynamic-library-under-base-and-the-use-of-c-notes
title: linux下动态库基础及C++下使用的注意事项
wordpress_id: 274
categories:
- 开发知识
---

 【图】




![](http://top.oa.com/pictures/201109/1315544326_71.png)




 




【生成动态库】




lib0x513pb.so: plugin_0x513.cpp oidb_0x513._0x513.pb.cc




        ${CC} ${GPB_INC} $^ -fPIC -shared -o $@ ${GPB_LIB}




 




【注册动态库路径】




添加动态库所在path到/etc/ld.so.conf




运行/sbin/ldconfig令配置生效。




 




【使用动态库】




tdb_now_demo: tdb_now_demo.cpp




${CC} ${GPB_INC} -rdynamic -o $@ $^ ${GPB_LIB} -L. -l0x513pb




——直接链接。修改动态库后，需要重启tdb_now_demo加载动态库，程序不会自动加载。




 




tdb_now_demo: tdb_now_demo.cpp




${CC} ${GPB_INC} -rdynamic -o $@ $^ ${GPB_LIB} -ldl




——配合dlopen()等函数。修改动态库后，程序可以自动检测并加载，不需要重启。




 




【dlopen的使用】




 Dl API

















函数








描述












dlopen








使对象文件可被程序访问












dlsym








获取执行了 dlopen 函数的对象文件中的符号的地址












dlerror








返回上一次出现错误的字符串错误












dlclose








关闭目标文件












该过程首先是调用 dlopen，提供要访问的文件对象和模式。调用 dlopen 的结果是稍候要使用的对象的句柄。mode 参数通知动态链接器何时执行再定位。有两个可能的值。第一个是 RTLD_NOW，它表明动态链接器将会在调用 dlopen 时完成所有必要的再定位。第二个可选的模式是 RTLD_LAZY，它只在需要时执行再定位。这是通过在内部使用动态链接器重定向所有尚未再定位的请求来完成的。这样，动态链接器就能够在请求时知晓何时发生了新的引用，而且再定位可以正常进行。后面的调用无需重复再定位过程。




还可以选择另外两种模式，它们可以按位 OR 到 mode 参数中。RTLD_LOCAL 表明其他任何对象都无法使加载的共享对象的符号用于再定位过程。如果这正是您想要的的话（例如，为了让共享的对象能够调用原始进程映像中的符号），那就使用 RTLD_GLOBAL 吧。




dlopen 函数还会自动解析共享库中的依赖项。这样，如果您打开了一个依赖于其他共享库的对象，它就会自动加载它们。函数返回一个句柄，该句柄用于后续的 API 调用。dlopen 的原型为：




#include <dlfcn.h>
void *dlopen( const char *file, int mode );




有了 ELF 对象的句柄，就可以通过调用 dlsym 来识别这个对象内的符号的地址了。该函数采用一个符号名称，如对象内的一个函数的名称。返回值为对象符号的解析地址：




void *dlsym( void *restrict handle, const char *restrict name );




如果调用该 API 时发生了错误，可以使用 dlerror 函数返回一个表示此错误的人类可读的字符串。该函数没有参数，它会在发生前面的错误时返回一个字符串，在没有错误发生时返回 NULL：




char *dlerror();




最后，如果无需再调用共享对象的话，应用程序可以调用 dlclose 来通知操作系统不再需要句柄和对象引用了。它完全是按引用来计数的，所以同一个共享对象的多个用户相互间不会发生冲突（只要还有一个用户在使用它，它就会待在内存中）。任何通过已关闭的对象的 dlsym 解析的符号都将不再可用。




char *dlclose( void *handle );


void invoke_method( char *lib, char *method, float argument )
{
void *dl_handle;
float (*func)(float);
char *error;

/* Open the shared object */
dl_handle = dlopen( lib, RTLD_LAZY );
if (!dl_handle) {
printf( "!!! %s\n", dlerror() );
return;
}

/* Resolve the symbol (method) from the object */
func = dlsym( dl_handle, method );
error = dlerror();
if (error != NULL) {
printf( "!!! %s\n", error );
return;
}

/* Call the resolved method and print the result */
printf(" %f\n", (*func)(argument) );

/* Close the object */
dlclose( dl_handle );

return;
}

int main( int argc, char *argv[] )
{
char line[MAX_STRING+1];
char lib[MAX_STRING+1];
char method[MAX_STRING+1];
float argument;

while (1) {

printf("> ");

line[0]=0;
fgets( line, MAX_STRING, stdin);

if (!strncmp(line, "bye", 3)) break;

sscanf( line, "%s %s %f", lib, method, &argument);

invoke_method( lib, method, argument );

}

}


【c++使用动态库的注意事项】




动态库的头文件.h中需要使用




#ifdef __cplusplus




extern "C"{




#endif




 




......




 




#ifdef __cplusplus




}




#endif




 




void *dlsym( void *restrict handle, const char *restrict name );




C＋+有Name Mangling机制。用g++编译的库文件，自然的运用了Name Mangling技术，原本的getdate(DateType& d)函数，很有可能其名字已变成了_getdate_DateType(DateType& d)（以支持多态，不同的编译器实现不一样），dlsym发现查找不到指定的函数名，运行的时候就会报错。
