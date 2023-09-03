### gdb调试

- 栈帧：

  ```shell
  bt 			# 查看栈帧
  info locals	# 查看局部变量
  frame 		# 栈帧号切换栈帧
  ```

`strace 二进制`，启动二进制，可以打印所有系统调用

**查看系统调用api**

what：操作系统实现提供给外部程序的接口，只有通过系统接口才能进内核

```
man man	# man手册，shell指令，系统调用，库调用等
man 2	# 2是系统调用
```

## 文件操作

```c++
// 文件路径，打开模式(O_RDONLY|O_WRONLY|O_RDWR O_CREATE|O_APPEND|O_TRUNC|O_EXC:|O_NONBLOCK)，创建文件的访问权限（rws, 受umask影响）
// fd是文件描述符表的数组下标
// 失败返回-1，并设置errno(<errno.h>中全局变量), 0读到文件尾了
// <string.h>中strerror()可以打印错误码对应字符串，perror()直接控制台输出错误码
// 头文件<unistd.h>系统api操作, <fcntl.h>文件操作flag等定义
// unbuffered I/O没有用户缓冲区，每次写操作都得进内核写内核缓冲区
// buf ---->  std库的缓冲区 ----|----> 内核缓冲区 ------> 磁盘
int open(const char *pathname, int flags, mode_t mode); 
ssize_t read(int fd, void *buf, size_t count);			// count缓冲区大小
ssize_t write(int fd, const void *buf, size_t count);	// count写入数据大小
int close(int fd);	// 不close：资源泄露（fd有限制，1024）文件锁定（文件锁），数据丢失（写操作在close时才刷新到磁盘上）
int fcntl(int fd, int cmd, ... /* arg */ );	// 文件属性，F_GETFL获取文件属性，F_SETFL设置文件属性（位图）
int ioctl(int d, int request, ...);
off_t lseek(int fd, off_t offset, int whence);// whence:起始位置SEEK_SET/SEEK_CUR/SEEK_END,返回的是相较于文件起始位置的偏移。读写用同一个偏移。可以用于获取文件大小（0,SEEK_END返回值），扩展文件大小(扩展大小,SEEK_END，偏移之后必须有io操作)
int truncate(const char *path, off_t length);	// 扩展文件大小
int stat(const char *pathname, struct stat *buf);	// 查看文件属性inode，文件类型
											// 符号穿透，硬软链接看到的链接之后的文件属性
int lstat(const char *pathname, struct stat *buf);	// 不会穿透，看到是链接文件
						// 4位文件类型（普通文件，目录文件，）+3位特殊权限位+用户rwx组rwx其他rwx
int link(const char *oldpath, const char *newpath);	// 创建硬链接（创建dentry）
int unlink(const char *pathname);					// 删除硬链接（删除dentry，系统等所有打开该文件的进程关闭该文件，才会挑时间释放掉）

opendir
readdir		// 返回目录项指针，一次读一个，读到文件尾NULL，读到文件
```

|      | r                               | w                            | x                            |
| ---- | ------------------------------- | ---------------------------- | ---------------------------- |
| 文件 | 文件内容可以被查看cat,more,less | 内容可以被修改vi，>          | 可以运行产生一个进程./文件名 |
| 目录 | 目录可以被浏览ls，tree          | 创建，删除，修改目录中的文件 | 可以被进入，cd               |



dentry ---------------->    inode  -----------------> 磁盘

文件路径                       磁盘位置                    

inode索引					引用计数



**阻塞，非阻塞：**

- 文件的属性，产生阻塞的场景，读设备文件，网络文件等
- 非阻塞读取终端文件（"/dev/tty"），修改文件属性（fctl ioctl或者把终端文件关了之后再O_NONBLOCK打开）。
  - 如果`read == -1`  并且 `errno= EARGAN || EWOULDBLOCK`，说明不是read失败，而是非阻塞读，目前无数据

**隐式回收回收：**当进程结束时，关闭所有该进程打开的文件，释放申请的内存空间。

## PCB进程控制块

`ulimit -a` 查看openfiles对应值，默认1024。可以`ulimit -n 数字` 修改

```c++
struct task_struct {
	struct files_struct *files;//打开文件的信息指针, 指向进程打开的文件描述符表(数组)
    						   		// STDIN_FILENO  标准输入 0
    								// STDOUT_FILENO 标准输出 1
    								// STDERR_FILENO 标准出错 2
    								// 其他进程打开的文件信息，数组最大1024个
}	// 结构体
```

