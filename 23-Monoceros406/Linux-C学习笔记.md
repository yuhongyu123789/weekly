---
title: Linux-C学习笔记
date: 2024-01-15 08:24:23
tags: C
mathjax: true
---

# Linux-C学习笔记

## 内存管理

### 动态内存管理

```c
long *buffer;
buffer=(long *)malloc(400);
buffer=(long *)realloc(buffer,256);
free(buffer);

long *buffer;
buffer=(long *)calloc(20,sizeof(long));
free(buffer);
```

### 链表

```c
#include <malloc.h>
#include <stdio.h>
#define LEN sizeof(struct student)
typedef struct student{
    int num,age;
    float score;
    struct student *next;
}stu;
int n;
stu *create(void){
    stu *head,*p1,*p2;
    n=0;
    p1=p2=(stu *)malloc(LEN);
    scanf("%d,%d,%f",&p1->num,&p1->age,&p1->score);
    head=NULL;
    while(p1->num!=0){
        n++;
        if(n==1)
            head=p1;
        else
            p2->next=p1;
        p2=p1;
        p1=(stu *)malloc(LEN);
        scanf("%d,%d,%f",&p1->num,&p1->age,&p1->score);
    };
    p2->next=NULL;
    return head;
};
int main(void){
    stu *p,*head;
    head=creat();
    p=head;
    if(head!=NULL)
        do{
            printf("%d,%d,%f\n",p->num,p->age,p->score);
            p=p->next;
        }while(p!=NULL);
    return 0;
};
```

## 进程控制

### 进程

```c
//fork函数复制一个子进程内容与父进程相同（内存、环境等完全相同且独立） 父进程返回子进程的ID 子进程返回0
#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h> //fork所需的两个头文件
#include <unistd.h>
int main(void){
    pid_t pid;
    if((pid=fork())<0){
        //进程创建失败
    }
    else if(pid==0){
        //在子进程中
    }
    else{
        //在父进程中
    }
    exit(0);
    return 0;
};

//vfork阻塞父进程 两个进程共用同一份内存
#include <stdio.h>
#include <unistd.h>//vfork所需的两个头文件
#include <sys/types.h>
int gvar=2;
int main(void){
    pid_t pid;
    int var=5;
    printf("process id:%ld\n",(long)getpid());
    printf("gvar=%d var=%d\n",gvar,var);
    if((pid=vfork())<0){
        perror("error!");
        return 1;
    }
    else if(pid==0){
        gvar--;
        var++;
        printf("the child process id:%ld\ngvar=$d var=%d\n",(long)getpid(),gvar,var); //1 6
        _exit(0); //子进程退出 父进程取消阻塞
    }
    else{
        printf("the parent process id:%l\ngvar=%d var=%d\n",(long)getpid(),gvar,var); //1 6
        return 0;
    };
};

//execve将某可执行文件转移至此地
//execve.c文件编译为execve
#include <stdio.h>
#include <unistd.h>
#include <sys/types.h>
extern char **environ;
int main(int argc,char *argv[]){
    execve("new",argv,environ);
    //该处代码不会执行 否则需要fork配合
};
//new2.c文件编译为new
#include <sys/types.h>
#include <unistd.h>
#include <stdio.h>
int main(void){
    puts("asdf");
    return 0;
};

//execlp可传参
#include <sys/types.h>
#include <unistd.h>
#include <stdio.h>
int main(int argc,char* argv[]){
    if(argc<2){
        return 1;
    };
    execlp("/bin/vi","vi",argv[1],(char*)NULL);
    return 0;
};

//wait挂起父进程直到子进程结束
#include <sys/types.h>
#include <sys/wait.h>
#include <unistd.h>
#include <stdio.h>
#include <stdlib.h>
void exit_s(int status){
    if(WIFEXITED(status)) //正常退出
        printf("normal exit,status=%d\n",WEXISTATUS(status)); //子进程正常退出时的状态
    else if(WIFSIGNALED(status)) //子进程被没有捕获的信号终止
        printf("signal exit!status=%d\n",WTERMSIG(status)); //进程被信号终止时 返回该信号类型
};
int main(void){
    pid_t pid,pid1;
    int status;
    if((pid=fork())<0){
        printf("child process error!\n");
        exit(0);
    }
    else if(pid==0){
        printf("the child process!\n");
        exit(2);//子进程正常退出方法
    };
    if(wait(&status)!=pid){
        printf("this is a parent process!\nwait error!\n");
        exit(0);
    };
    exit_s(status);

    //kill的用法
    if((pid=fork())<0){
        printf("child process error!\n");
        exit(0);
    }
    else if(pid==0){
        printf("the child process!\n");
        pid1=getpid();
        //kill(pid1,9); //结束进程
        //kill(pid1,17); //进入父进程
        kill(pid1,19); //暂时停止进程
    };
    if(wait(%status)!=pid){
        printf("this is a parent process!\nwait error!\n");
        exit(0);
    };
    exit_s(status);
    exit(0);
};
```

时间片分配略。

### 线程

略。

## IPC

### pipe

```c
#include <unistd.h>
#include <stdio.h>
#include <string.h>
#define MAXSIZE 100
int main(void){
    int fd[2],pid,line;
    char message[MAXSIZE];
    if(pipe(fd)==-1){
        perror("create pipe failed!");
        return 1;
    }
    else if((pid=fork())<0){
        perror("not create a new process!");
        return 1;
    }
    else if(pid==0){
        close(fd[0]);
        printf("child process send message!\n");
        write(fd[1],"asdfasdfasdf",13); //向文件写入数据
    }
    else{
        close(fd[1]);
        printf("parent process receive message is:\n");
        line=read(fd[0],message,MAXSIZE);
        write(STDOUT_FILENO,message,line);
        printf("\n");
        wait(NULL);
        _exit(0);
    };
    return 0;
};
```

### mkfifo

```c
#include <stdio.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>
#include <stdlib.h>
#define FIFO "/home/fifo4" //基于文件
int main(void){
    int fd,pid;
    char r_msg[BUFSIZ];
    if((pid=mkfifo(FIFO,0777))==-1){
        perror("create fifo channel failed!");
        return 1;
    };
    fd=open(FIFO,O_RDWR);
    if(fd==-1){
        perror("cannot open the FIFO");
        return 1;
    };
    if(write(fd,"hello world",12)==-1){
        perror("write data error!");
        return 1;
    };
    if(read(fd,r_msg,BUFSIZ)==-1){
        perror("read error!");
        return 1;
    }
    else
        printf("the receive data is %s!\n",r_msg);
    close(fd);
    return 0;
};
```

### SYSV基础

```c
#include <stdio.h>
#include <string.h>
#include <unistd.h>
#include <sys/types.h>
#include <sys/ipc.h>
#include <sys/shm.h>
int main(void){
    int shmid,proj_id,size;
    key_t key=IPC_PRIVATE;
    char *addr;
    pid_t pid;
    shmid=shmget(key,1024,IPC_CREAT|0660); //创建一块共享内存区域 键值为key 1024字节 
    if(shmid==-1){
        perror("create share memory failed!");
        return 1;
    }
    addr=(char *)shmat(shmid,NULL,0); //将共享内存区域附加到指定进程的地址空间中
    if(addr==(char*)(-1)){
        perror("cannot attach!");
        return 1;
    };
    printf("share memory segment's address:%x\n",addr);
    strcpy(addr,"asdfasdfasdf");
    pid=fork();
    if(pid==-1){
        perror("error!");
        return 1;
    }
    else if(pid==0){
        printf("child process string is '%s'\n",addr);
        _exit(0);
    }
    else{
        wait(NULL);
        printf("parent process string is '%s'\n",addr);
        if(shmdt(addr)==-1){ //将使用shmat附加的共享内存区域从该进程地址空间中分离出来
            perror("release failed!");
            return 1;
        }
        if(shmctl(shmid,IPC_RMIDM,NULL)==-1){ //删除shmid标识符所指的共享内存区域
            perror("failed!");
            return 1;
        };
    };
    return 0;
};
```

### 信号量

分别编译sl1.c和sl2.c文件，并同时运行。

```c
//sl1.c
#include <sys/types.h>
#include <linux/sem.h>
#include <stdlib.h>
#include <stdio.h>
#define RESOURCE 4
int main(void){
    key_t key;
    int semid;
    struct sembuf sbuf={0,-1,IPC_NOWAIT}; //<0获取 >0释放 =0处于使用状态
    union semun arg;
    if((key=ftok("/home/cff",'c'))==-1){ //创建进程
        perror("ftok error!\n");
        exit(1);
    };
    if((semid=semget(key,1,IPC_CREAT|0666))==-1){//创建新的或打开已有的信号量集 信号量集中有一个信号量
        perror("semget error!\n");
        exit(1);
    };
    arg.val=RESOURCE; //总共4个可用资源
    if(semctl(semid,0,SETVAL,arg)==-1){
        perror("semctl error!\n");
        exit(1);
    };
    while(true){
        if(semop(semid,&sbuf,1)==-1){ //获取1个资源
            perror("semop error!\n");
            exit(1);
        };
        sleep(3);
    };
    semctl(semid,0,IPC_RMID,0);
    exit(0);
};

//sl2.c
#include <sys/types.h>
#include <linux/sem.h>
#include <stdlib.h>
#include <stdio.h>
int main(void){
    key_t key;
    int semid,semval;
    union semun arg;
    if((key=ftok("/home/cff",'c'))==-1){
        perror("key error!\n");
        exit(1);
    };
    if((semid=semget(key,1,IPC_CREAT|0666))==-1){
        perror("semget error!\n");
        exit(1);
    };
    while(true){
        if((semval=semctl(semid,0,GETVAL,0))==-1){
            perror("semctl error!\n");
            exit(1);
        };
        if(semval>0){
            //剩余semval个资源可使用
        }
        else{
            //无可用资源
            break;
        };
        sleep(3);
    };
    exit(0);
};
```

## 消息队列

```c
#include <sys/types.h>
#include <sys/ipc.h>
#include <sys/msg.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
int main(void){
    key_t key;
    int proj_id=1,msqid;
    char message1[]={"asdfasdfasdf"};
    char message2[]={"zxcvzxcvzxcv"};
    struct msgbuf{
        long msgtype;
        char msgtext[1024];
    }snd,rcv;
    key=ftok("/home/cff/2",proj_id);
    if(key==-1){
        perror("create key error!");
        return 1;
    };
    if((msqid=msgget(key,IPC_CREAT|0666))==-1){
        printf("msgget error!\n");
        exit(1);
    };
    snd.msgtype=1;
    sprintf(snd.msgtext,message1);
    if(msgsnd(msqid,(struct msgbuf *)&snd,sizeof(message1)+1,0)==-1){
        printf("msgsnd error!\n");
        exit(1);
    };
    snd.msgtype=2;
    sprintf(snd.msgtext,"%s",message2);
    if(msgsnd(msqid,(struct msgbuf *)&snd,sizeof(message2)+1,0)==-1){
        printf("msgsnd error!\n");
        exit(1);
    };
    if(msgrcv(msqid,(struct msgbuf *)&rcv,80,1,IPC_NOWAIT)==-1){ //长度为80字节 IPC_NOWAIT未收到消息不阻塞 返回-1
        printf("msgrcv error!\n");
        exit(1);
    };
    printf("the received message:%s.\n",rcv.msgtext);
    system("ipcs -q");
    msgctl(msqid,IPC_RMID,0);
    exit(0);
};
```

## 文件操作

```c
//chown fchown略
//chmod fchmod略

//rename更改文件名
#include <stdio.h>
int main(int argc,char *argv[]){
    if(rename(argv[1],argv[2])<0){
        printf("failed!\n");
        return 1;
    }
    else{
        printf("%s=>%s\nsuccessful!\n",argv[1],argv[2]);
    };
    return 0;
};

//truncate ftruncate略
//dup dup2略

//stat
#include <sys/stat.h>
#include <unistd.h>
#include <stdio.h>
int main(void){
    struct stat buf;
    stat("new.c",&buf);
    printf("file size=%d\n",buf.st_size);
    printf("file owner UID=%d\n",buf.st_uid);
    return 0;
};

//getcwd获取当前工作目录
#include <stdio.h>
#include <unistd.h>
#include <limits.h>
int main(void){
    char a[PATH_MAX];
    if(getcwd(a,PATH_MAX)==NULL){
        perror("getcwd failed!");
        return 1;
    };
    printf("%s\n",a);
    return 0;
};

//chdir fchdir略

//mkdir创建新工作目录
#include <sys/stat.h>
#include <sys/types.h>
#include <stdio.h>
int main(void){
    char* dir="/home/cff/9/hello";
    if(mkdir(dir,0700)==-1){
        perror("create failed!");
        return 1;
    };
    printf("create hello successful!\n");
    return 0;
};
//rmdir删除目录
#include <unistd.h>
#include <stdio.h>
int main(void){
    char* dir="/home/cff/9/hello";
    if(mkdir(dir)==-1){
        perror("failed!");
        return 1;
    };
    printf("remove successful!\n");
    return 0;
};

//opendir readdir closedir
#include <dirent.h>
#include <unistd.h>
#include <stdio.h>
int main(void){
    DIR *dir;
    struct dirent *ptr;
    int i;
    dir=opendir("/home/cff/9");
    while((ptr=readdir(dir))!=NULL){
        printf("d_name:%s\n",ptr->d_name);
    };
    closedir(dir);
    return 0;
};

//link unlink
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>
#include <stdio.h>
#include <stdlib.h>
int main(void){
    char *oldpath="/home/cff/9/old.c";
    char *newpath="/home/cff/9/hardlink.c";
    if(link(oldpath,newpath)==-1){
        perror("create hard link failed!");
        return 1;
    };
    printf("create hard link successful!\n");
    if(open(newpath,O_RDWR)<0){
        perror("open error!");
        return 1;
    };
    printf("open successful!\n");
    sleep(10);
    if(unlink(newpath)<0){
        perror("unlink error!");
        return 1;
    };
    printf("file unlink!\n");
    sleep(10);
    printf("well done!\n");
    return 0;
};

//symlink用法完全同link
//readlink略
```

## 文件I/O

### 文件读写

```c
#include <stdio.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>
#include <unistd.h>
int main(void){
    char *path="/home/cff/9/test.c";
    int fd,n,i;
    char buf[40],buf2[]="hello mrcff";
    if((fd=open(path,O_RDWR))<0){
        perror("open file failed!");
        return 1;
    }
    else
        printf("open file successful!\n");
    if((n=read(fd,buf,20))<0){ //读20字节
        perror("read failed!");
        return 1;
    }
    else{
        printf("output read data:\n");
        printf("%s\n",buf);
    };
    if((i=lseek(fd,11,SEEK_SET))<0){ //偏移量11开始 SEEK_SET从文件头
        perror("lseek error!");
        return 1;
    }
    else if(write(fd,buf2,11)<0){
        perror("write error!");
        return 1;
    }
    else
        printf("write successful!\n");
    close(fd);
    if((fd=open(path,O_RDWR))<0){
        perror("open file failed!");
        return 1;
    };
    if((n=read(fd,buf,40))<0){
        perror("read 2 failed");
        return 1;
    }
    else{
        printf("read the changed data:\n");
        printf("%s\n",buf);
    };
    if(close(fd)<0){
        perror("close failed!");
        return 1;
    }
    else
        printf("good bye!\n");
    return 0;
};
```

### 字符I/O

```c
#include <stdio.h>
int main(void){
    FILE *fp;
    int i;
    char *path="/home/cff/10/test.c";
    char a[]={'h','e','l','l','o',' ','m','r'},ch;
    fp=fopen(path,"w");
    if(fp){
        for(i=0;i<5;i++)
            if(fputc(a[i],fp)==EOF){
                perror("write error!");
                return 1;
            };
        printf("write successfule!\n");
    }
    else{
        printf("open error!\n");
        return 1;
    };
    fclose(fp);
    if((fp=fopen("/home/cff/10/test.c","r"))==NULL){
        perror("open error!");
        return 1;
    };
    printf("output data in the test.c\n");
    for(i=0;i<5;i++)
        if((ch=fgetc(fp))==EOF){
            perror("fgetc error!");
            return 1;
        }
        else
            printf("%c",ch);
    printf("\nget successful!\nplease examine test.c...\n");
    fclose(fp);
    return 0;
};
```

## 信号

略。

## 网络编程

### TCP

```c
//先运行服务端 再运行客户端 客户端发送消息come on! 服务端接收后回复thanks 客户端停止 服务器端不停监听
//服务端
#include <sys/types.h>
#include <sys/socket.h>
#include <stdio.h>
#include <netinet/in.h>
#include <arpa/inet.h>
#include <unistd.h>
#define PORT 3339
int main(void){
    char *sendbuf="thanks";
    char buf[256];
    int s_fd,c_fd; //服务端和客户端套接字标识符
    int s_len,c_len; //服务端和客户端消息长度
    struct sockaddr_in s_addr; //服务端套接字地址
    struct sockaddr_in c_addr; //客户端套接字地址
    s_fd=socket(AF_INET,SOCK_STREAM,0); //创建套接字 AF_INET表示互联网TCP/IP协议族 SOCK_STREAM流式套接字
    s_addr.sin_family=AF_INET;
    s_addr.sin_addr.s_addr=htonl(INADDR_ANY); //把32位值从主机字节序转换成网络字节序 本机地址
    s_addr.sin_port=PORT;
    s_len=sizeof(s_addr);
    bind(s_fd,(struct sockaddr *)&s_addr,s_len); //配置套接字
    listen(s_fd,10); //处于被动监听模式
    while(true){
        printf("please wait a moment!\n");
        c_len=sizeof(c_addr);
        c_fd=accept(s_fd,(struct sockaddr *)&c_addr,(socklen_t *__restrict)&c_len); //接收请求
        recv(c_fd,buf,256,0); //256字节
        buf[sizeof(buf)+1]='\0';
        printf("receive message:\n %s\n",buf);
        send(c_fd,sendbuf,sizeof(sendbuf),0);
        close(c_fd); //关闭请求 等待accept下一个请求
    };
    return 0;
};

//客户端
#include <sys/types.h>
#include <sys/socket.h>
#include <stdio.h>
#include <netinet/in.h>
#include <arpa/inet.h>
#include <unistd.h>
#define PORT 3339
int main(void){
    int sockfd,len,newsockfd,len2;
    struct sockaddr_in addr;
    char *buf="come on!";
    char rebuf[40];
    sockfd=socket(AF_INET,SOCK_STREAM,0);
    addr.sin_family=AF_INET;
    addr.sin_addr.s_addr=htonl(INADDR_ANY);
    addr.sin_port=PORT;
    len=sizeof(addr);
    newsockfd=connect(sockfd,(struct sockaddr *)&addr,len);
    if(newsockfd==-1){
        //连接失败
        return 1;
    };
    len2=sizeof(buf);
    send(sockfd,buf,len2,0);
    sleep(10);
    recv(sockfd,rebuf,256,0);
    rebuf[sizeof(rebuf)+1]='\0';
    printf("receive message:\n%s\n",rebuf);
    close(sockfd);
    return 0;
};
```

### UDP

```c
//服务端
#include <stdio.h>
#include <string.h>
#include <sys/types.h>
#include <netinet/in.h>
#include <sys/socket.h>
#include <errno.h>
#include <stdlib.h>
#include <arpa/inet.h>
#define PORT 8886
int main(int argc,char **argv){
    struct sockaddr_in s_addr,c_addr;
    int sock,len;
    socklen_t addr_len;
    char buff[128];
    if((sock=socket(AF_INET,SOCK_DGRAM,0))==-1){
        perror("socket");
        exit(errno);
    }
    else
        printf("create socket successful.\n\r");
    memset(&s_addr,0,sizeof(struct sockaddr_in));
    s_addr.sin_family=AF_INET;
    if(argv[2])
        s_addr.sin_port=htons(atoi(argv[2]));
    else
        s_addr.sin_port=htons(PORT);
    if(argv[1])
        s_addr.sin_addr.s_addr=inet_addr(argv[1]);
    else
        s_addr.sin_addr.s_addr=INADDR_ANY;
    if((bind(sock,(struct sockaddr *)&s_addr,sizeof(s_addr)))==-1){
        perror("bind error");
        exit(errno);
    }
    else
        printf("bind address to socket successfuly.\n\r");
    addr_len=sizeof(c_addr);
    while(true){
        len=recvfrom(sock,buff,sizeof(buff)-1,0,(struct sockaddr *)&c_addr,&addr_len);
        if(len<0){
            perror("recvfrom error");
            exit(errno);
        };
        buff[len]='\0';
        printf("收到远端计算机%s 端口号%d 的消息\n%s\n\r",inet_ntoa(c_addr.sin_addr),ntohs(c_addr.sin_port),buff);
    };
    return 0;
};

//客户端
#include <stdio.h>
#include <string.h>
#include <sys/types.h>
#include <netinet/in.h>
#include <sys/socket.h>
#include <errno.h>
#include <stdlib.h>
#include <arpa/inet.h>
#define PORT 8886
int main(int argc,char **argv){
    struct sockaddr_in s_addr;
    int sock;
    int addr_len;
    int len;
    char buff[]="Hello everyone,Merry Christmas!";
    if((sock=socket(AF_INET,SOCK_DGRAM,0))==-1){
        perror("socket error");
        exit(errno);
    }
    else
        printf("create socket successful.\n\r");
    s_addr.sin_family=AF_INET;
    if(argv[2])
        s_addr.sin_port=htons(atoi(argv[2]));
    else
        s_addr.sin_port=htons(PORT);
    if(argv[1])
        s_addr.sin_addr.s_addr=inet_addr(argv[1]);
    else{
        //没有接收者
        exit(0);
    };
    addr_len=sizeof(s_addr);
    len=sendto(sock,buff,sizeof(buff),0,(struct sockaddr *)&s_addr,addr_len);
    if(len<0){
        printf("\n\rsend error.\n\r");
        return 3;
    };
    printf("send success.\n\r");
    return 0;
};
```

