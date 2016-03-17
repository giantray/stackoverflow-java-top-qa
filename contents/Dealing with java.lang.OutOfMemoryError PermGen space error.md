##如何处理 java.lang.outOfMemoryError PermGen space error

###问题

最近，我在过运行我的web应用时得到：java.lang.OutOfMemoryError: PermGen space。
我的应用用一个典型的 Hibernate/JPA + IceFaces/JSF的应用.运行于Tomcat6.0和jdk1.6.我发布了多次以后，产生了这个错误。

是什么原因造成的，我如何避免？我怎样修复?


#回答


解决的方案是当TomeCat启时，在jvm的的命令行添加参数

	-XX:+CMSClassUnloadingEnabled -XX:+CMSPermGenSweepingEnabled


你也可以停止tomcat的服务，直接进入Tomcat/bin目录,运行tomcat6w.exe.在Java的标签下，参加上面的参数。单击"OK"，重新启动Tomcat的服务.


如果你得到不一个未安装的服务，你可以运行：
	
	tomcat6w //ES//servicename

servicename的名字你右以在services.msc中查看。

		
