select process

int select(int maxfdp1,fd_set *readset,fd_set *write_set,fd_set *exceptset,const struct timeval *timeout);

@return 就绪的描述符个数，-1时出错

struct timeval{
    long tv_sec; /* seconds */
    long tv_usec; /* microseconds*/
}
timeout在某些linux系统返回时会被设置为剩余时间，所以我们应该对这个参数设置为const

fd_set是一个bitset，数据结构为整型数组，数组第一个元素标识0-31的fd，第二个元素标识32-63，以此类推

maxfdp1 是最大的描述符+1，比如一个集合{1,2,5},maxfdp1就应该是为6（5+1）

就绪的fd会在传入的fd_set参数对应位置中被设置为1，没有就绪的被设置为0。**所以在每一次调用select都要对fd_set以及timeout进行设置**

四个宏对fd_set进行操作
```c
void FD_ZERO(fd_set *fd_set);   /* clear all bit int fdset */

void FD_SET(int fd, fd_set *fdset);     /* turn on the bit for fd in fdset */

void FD_CLR(int fd, fd_set *fdset);     /* turn off the bit for fd in fdset */

void FD_ISSET(int fd, fd_set *fdset);    /* is the bit for fd on in fdset? */
```


IO复用模型比起阻塞IO模型，优势在于可以等待多个fd。


poll
#inlucde<poll.h>

poll和select的区别在于，poll没有fd数量的限制

int poll(struct poolfd *fdarray, unsigned long nfds, int timeout);

