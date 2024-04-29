## ThreadLoacal
### 案例及分析
1. 父子线程传递不规范
	通常开发时，ThreadLocal存租户信息，通常会遇到父线程执行业务，子线程进行日志打印，（父线程对子线程进行赋值）。但是子线程在获取数据前，有其他操作，而父线程已经结束操作并清除值。
	解决：
		使用深拷贝复制对象到子线程
2. 线程池内复用线程导致的数据污染
	线程池创建线程进行任务执行（并携带InheritableThreadLocal），出现了无法修改threadLocal的问题，导致数据错误。
	解决：
		InheritableThreadLocal核心线程在初始化后就固定不可变。可以使用阿里的TransmittableThreadLocal来解决。
3. 线程服用及父子线程传递不规范的问题
	TransmittableThreadLocal由于父子线程的值传递由初始化init变成运行时传递，因此childValue的方法不会生效。
	解决：
		翻阅文档，使用重写copy方法，父线程无论如何修改，都不会影响到子线程的值。
4. ThreadLocal内存泄漏
	线程池的线程内部threadLocal中的key（弱引用会被gc，但是value是强引用）且由于线程存在，线程无法正确获取threadLocal 的key，而value却存在。造成内存泄漏。
		慎用线程池中的ThreadLocal（线程不会结束，线程持续的时间不可测，
