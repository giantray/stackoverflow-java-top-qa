加载JNI共享库失败（JDK）
问题：当我试图打开Eclipse时，会弹出一个提示写着：
Failed to load the JNI shared library "C:/JDK/bin/client/jvm.dll"`.
然后，Eclipse会强制关闭。
我做了以下几点：
·我检查那个路径有没有存在什么，真的有存在。
·我的Eclipse和Java SE Development Kit都是64位的。我检查我的系统，它能处理64位。
·我也在Google和Stack Overflow搜索解决方法，我找到唯一的方法是下载一个32位版本的JDK和Eclipse。
下载32位版本是我没办法下的办法。但还有其他的解决方法吗？

回答：
你需要一个三件套：
·64位的操作系统
·64位的Java
·64位的Eclipse


