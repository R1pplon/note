# 介绍

Docker 的本质是利用 LXC 来实现类似虚拟机的功能，从而节省硬件资源并为用户提供更多计算资源。本项目将 C++ 与 Linux 的命名空间（Namespace）和控制组（Control Group）技术相结合，实现一个简单的 Docker 容器。

最后，我们将为容器实现以下功能：

1. 独立的文件系统
2. 支持网络访问

## 👀 预览

```bash
$ make
make container
make[1]: Entering directory '/home/labex/project'
gcc -c network.c nl.c
g++ -std=c++11 -o docker-run main.cpp network.o nl.o
make[1]: Leaving directory '/home/labex/project'
$ sudo./docker-run
...启动容器
root@labex:/# ifconfig
eth0      Link encap:Ethernet  HWaddr 00:16:3e:da:01:72
          inet6 addr: fe80::dc15:18ff:fe43:53b9/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:38 errors:0 dropped:0 overruns:0 frame:0
          TX packets:9 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:5744 (5.7 KB)  TX bytes:726 (726.0 B)

lo        Link encap:Local Loopback
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)
```

## 🎯 任务

在本项目中，你将学习：

- 如何使用 C++ 和 Linux 的命名空间技术创建一个简单的 Docker 容器
- 如何为容器实现一个独立的文件系统
- 如何为容器启用网络访问

## 🏆 成果

完成本项目后，你将能够：

- 使用 C++ 和 Linux 的命名空间技术创建一个简单的 Docker 容器
- 为容器实现一个独立的文件系统
- 为容器启用网络访问
# Linux 命名空间技术

在 C++ 中，我们熟悉关键字`namespace`。在 C++ 里，每个命名空间会隔离不同代码中的相同名称，所以只要命名空间的名称不同，命名空间内代码的名称就可以相同，从而解决了代码中的名称冲突问题。

另一方面，Linux 命名空间是 Linux 内核提供的一项技术，它为应用程序的资源隔离提供了一种解决方案，类似于 C++ 中`namespace`的概念。我们知道诸如 PID、IPC 和网络等资源应由操作系统本身管理，但 Linux 命名空间可以使这些资源不再全局化，并将它们分配到特定的命名空间。

在 Docker 技术领域，我们经常听到像 LXC 和操作系统级虚拟化这样的术语，而 LXC 利用命名空间技术来实现不同容器之间的资源隔离。通过使用命名空间技术，不同容器内的进程属于不同的命名空间，彼此互不干扰。总之，命名空间技术提供了一种轻量级的虚拟化形式，使我们能够从不同角度操作系统范围的属性。

在 Linux 中，与命名空间相关的最重要的系统调用是`clone()`。`clone()`的目的是在创建进程时将线程限制在特定的命名空间内。
# 总结

通过这个项目，我们逐步实现了以下目标：将文件系统集成到容器中，并实现对外部网络的访问。

我们成功创建了一个基本的 Docker 容器。你可以进一步优化这个容器，以实现更逼真的模拟。
# 系统调用的封装

由于 Linux 系统调用是用 C 编写的，而我们的项目需要编写 C++ 代码。为了保持纯粹的 C++ 一致编码风格，我们将首先把这些必要的 API 封装成 C++ 形式，这也将使我们能更深入地理解这些 API 的使用方式。

我们将使用以下 API：

## `clone()`

`clone`和`fork`系统调用在 Linux 中都用于创建进程。然而，`fork`只是`clone`的一小部分。它们的区别在于，`fork`只创建一个与父进程完全相同的子进程，而`clone`更强大，因为它允许有选择地将父进程资源复制到子进程。未被复制的资源通过指针复制（`arg`）在进程间共享。可以使用`flags`指定要复制的特定资源，该函数返回子进程的 PID。

我们知道一个进程由四个主要元素组成：

1. 一段要执行的代码
2. 进程的私有栈空间
3. 进程控制块（PCB）
4. 进程特定的命名空间

前两个元素对应于`clone`中的参数`fn`和`child_stack`。进程控制块由内核控制，我们无需担心。因此，命名空间与`flags`参数相关联。为了实现创建 Docker 容器的目标，我们需要的主要参数如下：

| 命名空间分类 | 系统调用参数 |
| ------------ | ------------ |
| UTS          | CLONE_NEWUTS |
| 挂载         | CLONE_NEWNS  |
| PID          | CLONE_NEWPID |
| 网络         | CLONE_NEWNET |

从名称可以看出，`CLONE_NEWNS`为复制提供与文件系统相关的挂载和与文件系统相关的资源，`CLONE_NEWUTS`提供设置主机名的功能，`CLONE_NEWPID`提供独立进程空间支持，`CLONE_NEWNET`提供与网络相关的支持。

## `execv()`

```c
int execv(const char *path, char *const argv[]);
```

`execv`执行`path`指定的可执行文件。这个系统调用允许我们的子进程执行`/bin/bash`，以使容器保持运行。

## `sethostname()`

```c
int sethostname(const char *name, size_t len);
```

顾名思义，这个系统调用用于设置主机名。值得一提的是，由于 C 风格字符串使用指针，无法直接从内部确定字符串的长度，因此使用`len`参数来获取字符串的长度。

## `chdir()`

```c
int chdir(const char *path);
```

我们知道任何程序都在特定目录中运行。当我们需要访问资源时，可以使用相对路径而不是绝对路径来访问相关资源。`chdir`为我们提供了更改程序工作目录的便利，可用于某些未公开的目的。

## `chroot()`

这个系统调用用于更改根目录：

```c
int chroot(const char *path);
```

## `mount()`

这个系统调用用于挂载文件系统，类似于`mount`命令。

```c
int mount(const char *source, const char *target,
                 const char *filesystemtype, unsigned long mountflags,
                 const void *data);
```
# 创建容器子进程

进入`~/project`目录并创建一个名为`docker.hpp`的文件。在这个文件中，我们首先创建一个名为`docker`的命名空间，可供外部代码调用。

```cpp
//
// docker.hpp
// cpp_docker
//

// 系统调用的头文件
#include <sys/wait.h>   // waitpid
#include <sys/mount.h>  // mount
#include <fcntl.h>      // open
#include <unistd.h>     // execv, sethostname, chroot, fchdir
#include <sched.h>      // clone

// C 标准库
#include <cstring>

// C++ 标准库
#include <string>       // std::string

#define STACK_SIZE (512 * 512) // 定义子进程空间的大小

namespace docker {
    //.. 这里是 docker 魔法开始的地方
}
```

让我们先定义一些变量以提高可读性：

```cpp
// 在 `docker` 命名空间内定义
typedef int proc_status;
proc_status proc_err  = -1;
proc_status proc_exit = 0;
proc_status proc_wait = 1;
```

在定义容器类之前，让我们分析一下创建容器所需的参数。目前我们暂不考虑网络相关配置。要从镜像创建一个 Docker 容器，我们只需要指定主机名和镜像的位置。因此：

```cpp
// Docker 容器启动配置
typedef struct container_config {
    std::string host_name;      // 主机名
    std::string root_dir;       // 容器的根目录
} container_config;
```

现在，让我们定义`container`类，并在构造函数中为容器执行必要的配置：

```cpp
class container {
private:
    // 提高可读性
    typedef int process_pid;

    // 子进程栈
    char child_stack[STACK_SIZE];

    // 容器配置
    container_config config;
public:
    container(container_config &config) {
        this->config = config;
    }
};
```

在考虑`container`类中的具体方法之前，让我们先思考一下如何使用这个`container`类。为此，我们在`~/project`文件夹中创建一个`main.cpp`文件：

```cpp
//
// main.cpp
// cpp_docker
//

#include "docker.hpp"
#include <iostream>

int main(int argc, char** argv) {
    std::cout << "...start container" << std::endl;
    docker::container_config config;

    // 配置容器
    //...

    docker::container container(config);// 根据配置构造容器
    container.start();                  // 启动容器
    std::cout << "stop container..." << std::endl;
    return 0;
}
```

在`main.cpp`中，为了使容器启动简洁易懂，我们假设使用`start()`方法来启动容器。这为稍后编写`docker.hpp`文件奠定了基础。

现在，让我们回到`docker.hpp`并实现`start()`方法：

```cpp
void start() {
    auto setup = [](void *args) -> int {
        auto _this = reinterpret_cast<container *>(args);

        // 为容器执行相关配置
        //...

        return proc_wait;
    };

    process_pid child_pid = clone(setup, child_stack+STACK_SIZE, // 移动到栈底
                        SIGCHLD,      // 子进程退出时向父进程发送信号
                        this);
    waitpid(child_pid, nullptr, 0); // 等待子进程退出
}
```

`docker::container::start()`方法使用了 Linux 中的`clone()`系统调用。为了将`docker::container`实例对象传递给回调函数`setup`，我们可以使用`clone()`的第四个参数来传递。这里我们传递`this`指针。

至于`setup`函数，我们为它创建了一个 lambda 表达式。在 C++ 中，一个捕获列表为空的 lambda 表达式可以作为函数指针传递。因此，`setup`成为传递给`clone()`的回调函数。

> 你也可以使用类中定义的静态成员函数代替 lambda 表达式，但那样代码会不那么优雅。

在这个`container`类的构造函数中，我们定义了一个子进程处理函数，供`clone()`系统调用调用。我们使用`typedef`将这个函数的返回类型改为`proc_status`。当这个函数返回`proc_wait`时，由`clone()`克隆的子进程将等待退出。

然而，这还不够，因为我们在进程内部尚未执行任何配置。结果，我们的程序一旦启动，由于没有其他事情可做，将立即退出。如我们所知，在 Docker 中，为了保持容器运行，我们可以使用：

```bash
docker run -it ubuntu:14.04 /bin/bash
```

这将 STDIN 绑定到容器的`/bin/bash`。所以，让我们在`docker::container`类中添加一个`start_bash()`方法：

```cpp
private:
void start_bash() {
    // 安全地将 C++ std::string 转换为 C 风格字符串 char *
    // 从 C++14 开始，禁止直接赋值：`char *str = "test";`
    std::string bash = "/bin/bash";
    char *c_bash = new char[bash.length()+1];   // +1 用于'\0'
    strcpy(c_bash, bash.c_str());

    char* const child_args[] = { c_bash, NULL };
    execv(child_args[0], child_args);           // 在子进程中执行/bin/bash
    delete []c_bash;
}
```

并在`setup`中调用它：

```cpp
auto setup = [](void *args) -> int {
    auto _this = reinterpret_cast<container *>(args);
    _this->start_bash();
    return proc_wait;
}
```

现在，我们可以看到以下操作：

```bash
labex:project/ $ hostname
iZj6cboigynrxh4mn2oo16Z
labex:project/ $ g++ main.cpp -std=c++11
labex:project/ $./a.out
...start container
labex@iZj6cboigynrxh4mn2oo16Z:~/project$ mkdir test
labex@iZj6cboigynrxh4mn2oo16Z:~/project$ ls
a.out docker.hpp main.cpp test
labex@iZj6cboigynrxh4mn2oo16Z:~/project$ exit
exit
stop container...
```

在上述步骤中，我们首先检查当前的`hostname`，编译我们到目前为止编写的代码，运行它，然后进入我们的容器。我们可以看到进入容器后，bash 提示符发生了变化，这正是我们所期望的。

然而，很容易注意到这并不是我们想要的结果，因为它与我们的主机系统完全相同。在这个“容器”中执行的任何操作都会直接影响主机系统。

这就是我们在`clone` API 中引入所需命名空间的地方。
# 让容器拥有自己的主机名

如前文在系统调用部分所述，使用系统调用设置子进程的主机名相当简单。因此，我们为`docker::container`类创建一个私有方法：

```cpp
private:
// 设置容器的主机名
void set_hostname() {
    sethostname(this->config.host_name.c_str(), this->config.host_name.length());
}
```

我们还对`start()`方法做如下修改：

```cpp
void start() {
    auto setup = [](void *args) -> int {
        auto _this = reinterpret_cast<container *>(args);

        // 配置容器
        _this->set_hostname();
        _this->start_bash();

        return proc_wait;
    };

    process_pid child_pid = clone(setup, child_stack+STACK_SIZE,
                        CLONE_NEWUTS| // 添加 UTS 命名空间
                        SIGCHLD,      // 子进程退出时向父进程发送信号
                        this);
    waitpid(child_pid, nullptr, 0); // 等待子进程退出
}
```

在`main.cpp`文件中，我们配置主机名：

```cpp
int main(int argc, char** argv) {
    std::cout << "...start container" << std::endl;
    docker::container_config config;
    config.host_name = "labex";
   ……
```

现在，让我们重新编译代码：

```bash
labex:project/ $ g++ main.cpp -std=c++11
labex:project/ $./a.out
...start container
stop container...
```

可以观察到我们的容器立即退出。这是因为一旦我们引入了命名空间，我们的程序就需要超级用户权限。因此，我们需要使用`sudo`来执行程序：

```bash
labex:project/ $ sudo./a.out
...start container
root@labex:/home/labex/project# hostname
labex
root@labex:/home/labex/project# exit
exit
stop container...
labex:project/ $ hostname
iZj6cboigynrxh4mn2oo16Z
```

然而，这仍然没有达到容器的预期效果，因为从`ls`命令可以看出，我们仍然可以访问主机的目录。
# 为容器启用其自身的文件系统

在 Docker 技术中，容器是基于镜像创建的。既然我们要实现一个容器，自然需要基于一个镜像来创建它。幸运的是，我们已经为你准备了一个 Docker 镜像。你可以通过从以下地址下载来获取它：

```bash
cd ~/project
wget --header="User-Agent: Mozilla/5.0" https://file.labex.io/lab/171925/docker-image.tar
```

然后，将其解压到`~/project/labex`文件夹中：

```bash
mkdir labex
tar -xf docker-image.tar --directory labex/
rm docker-image.tar
```

在这里，你可能会遇到一些解压错误。这是因为在该环境中，某些文件被禁止外部创建。但这并不影响我们实现自己的容器，所以可以忽略它。

```bash
tar: dev/agpgart: Cannot mknod: Operation not permitted
tar: dev/audio: Cannot mknod: Operation not permitted
tar: dev/audio1: Cannot mknod: Operation not permitted
tar: dev/audio2: Cannot mknod: Operation not permitted
tar: dev/audio3: Cannot mknod: Operation not permitted
tar: dev/audioctl: Cannot mknod: Operation not permitted
……
```

解压完成后，我们将能够在`labex`下看到一个几乎完整的 Linux 目录：

```bash
labex:project/ $ ls labex
bin boot dev etc home lib lib64 media mnt opt proc root run sbin srv sys tmp usr var
```

现在，我们希望`docker::container`进入这个目录，并将其用作根目录，在启动时屏蔽子进程对外部的访问：

```cpp
private:
// 设置根目录
void set_rootdir() {

    // chdir 系统调用，切换到某个目录
    chdir(this->config.root_dir.c_str());

    // chroot 系统调用，设置根目录，因为我们之前已经
    // 切换到了当前目录，所以可以简单地使用当前目录作为根目录
    chroot(".");
}
```

然后，在`main.cpp`中填写相关配置：

```cpp
#include "docker.hpp"
#include <iostream>

int main(int argc, char** argv) {
    std::cout << "...start container" << std::endl;
    docker::container_config config;
    config.host_name = "labex";
    config.root_dir  = "./labex";
    ……
```

并在`clone()`调用中启用`CLONE_NEWNS`以激活挂载命名空间：

```cpp
void start() {
    auto setup = [](void *args) -> int {
        auto _this = reinterpret_cast<container *>(args);
        _this->set_hostname();
        _this->set_rootdir();
        _this->start_bash();
        return proc_wait;
    };

    process_pid child_pid = clone(setup, child_stack+STACK_SIZE,
                      CLONE_NEWUTS| // UTS 命名空间
                      CLONE_NEWNS|  // 挂载命名空间
                      SIGCHLD,      // 子进程退出时向父进程发送信号
                      this);
    waitpid(child_pid, nullptr, 0); // 等待子进程退出
}
```

现在，让我们重新编译：

```bash
labex:project/ $ g++ main.cpp -std=c++11
labex:project/ $ sudo./a.out
...start container
root@labex:/# ls
bin boot dev etc home lib lib64 media mnt opt proc root run sbin srv sys tmp usr var
root@labex:/# hostname
labex
```

通过运行`ls`，我们可以看到子进程现在处于一个完整的`linux`目录中。
# 让容器拥有其自身的进程系统

然而，仍然存在一个问题。如果我们使用`ps`或`top`等命令，仍然可以观察到父进程中的所有进程。这不是我们想要的效果。例如，在`ps`的输出中我们可以看到`a.out`，并且进程 ID 值也非常大。

为了解决这个问题，我们需要引入 PID 命名空间，将子进程的 PID 空间与父进程隔离开来。

```cpp
private:
// 设置独立的进程命名空间
void set_procsys() {
    // 挂载 proc 文件系统
    mount("none", "/proc", "proc", 0, nullptr);
    mount("none", "/sys", "sysfs", 0, nullptr);
}
```

同样，我们仍然需要在`start()`中添加这部分代码，引入`CLONE_NEWPID`：

```cpp
void start() {
    auto setup = [](void *args) -> int {
        auto _this = reinterpret_cast<container *>(args);
        _this->set_hostname();
        _this->set_rootdir();
        _this->set_procsys();
        _this->start_bash();
        return proc_wait;
    };

    process_pid child_pid = clone(setup, child_stack,
                      CLONE_NEWUTS| // UTS 命名空间
                      CLONE_NEWNS|  // 挂载命名空间
                      CLONE_NEWPID| // PID 命名空间
                      SIGCHLD,      // 子进程退出时向父进程发送信号
                      this);
    waitpid(child_pid, nullptr, 0); // 等待子进程退出
}
```

现在，当我们再次编译并运行时，我们将看到容器拥有自己独立的进程空间：

此时，我们已经使用了 Linux 中的命名空间技术来隔离子进程中的资源，并为我们的 Docker 容器赋予了自己的进程空间和文件系统。

然而，容器仍然无法访问网络，甚至我们可以使用`ifconfig`访问主机的网络设备。这不是我们想要的。接下来，我们将进一步增强容器，使其更像一个完整的容器，提供对网络访问的支持。
# Docker 网络原理

之前，我们对 Docker 如何实现一个封闭的容器有了初步的了解。然而，我们也发现我们实现的 Docker 容器不支持网络访问，而且我们运行的不同容器之间也无法相互通信。

![Docker 容器网络桥接图](https://file.labex.io/namespace/718bace8-27a3-4200-a588-dde4041ceeb9/cpp/project-creating-a-simple-docker-container-in-cpp/lab-1/zh/../assets/document-uid29879labid2005timestamp1471251217443.png)

Docker 容器之间的网络通信原理是通过一个名为“docker0”的网桥来实现的。两个容器“container1”和“container2”各自有自己的网络设备“eth0”。所有的网络请求都会通过“eth0”进行转发。由于容器运行在子进程中，为了使它们的“eth0”接口能够通信，需要创建一对网络设备“veth1”和“veth2”，并将它们添加到“docker0”网桥中。这样，网桥就可以无条件地转发和路由容器内“eth0”接口产生的网络请求，从而实现容器之间的通信。

因此，为了使我们编写的容器具备网络通信能力，我们首先需要创建一个它们可以使用的网桥。为了方便起见，我们将直接使用环境中现有的“docker0”。
# 网络创建准备

使用原生 Linux API 来操作网络是一项非常复杂的任务，其中还涉及到许多 C 语言操作。为了更专注于使用 C++ 进行编码，这里为你准备了一些已经实现好的“轮子”，这会让你操作网络更加方便。

> 进入`/tmp`目录，我们为你提供了四个文件：`network.h`、`nl.h`、`network.c`和`nl.c`。

将这四个文件复制到`~/project`目录：

```bash
cp /tmp/network.h /tmp/nl.h /tmp/network.c /tmp/nl.c ~/project/
```

后三个文件的代码取自`LXC`工具集。不过这段代码是用 C 语言编写的。由于从 C++11 开始 C++ 和 C 不再相互兼容，为了让 C++ 能够顺利调用这段代码，我们必须掌握一些 C/C++ 混合编程的知识。

首先，我们知道将源代码转换为可执行文件不是直接完成的，而是要经过预处理、编译、汇编和链接几个步骤。通常我们使用`g++ main.cpp`这一步骤一次性完成上述所有步骤。

然而，当项目变得更大，源文件数量增加时，仅仅因为一个小改动就重新编译整个项目是不划算的。此时，我们可以先将代码编译成`.o`文件，然后再进行链接工作。这也使得我们能够同时编译一个 C 语言编译链接文件和 C++ 相关的源代码。

C++ 和 C 有不同的编译和处理方式，所以当我们想要编译一组 C 语言代码时，需要使用`__cplusplus`宏和`extern "C"`。

在`network.h`中存储了`network.c`的相关接口声明。如果我们注释掉以下带注释的部分：

```c
// #ifdef __cplusplus
// extern "C"
// {
// #endif
#include <sys/types.h>
int netdev_set_flag(const char *name, int flag);
……
void new_hwaddr(char *hwaddr);
// #ifdef __cplusplus
// }
// #endif
```

使用`gcc`直接将其编译成`.o`文件：

```bash
gcc -c network.c nl.c
```

然后使用以下代码：

```cpp
// test.cpp
#include "network.h"
int main() {
    new_hwaddr(nullptr);
    return 0;
}
```

进行编译和测试：

```bash
g++ test.cpp network.o nl.o -std=c++11
```

我们会发现编译失败，并提示错误`undefined reference to 'new_hwaddr(char*)'`。

```bash
/usr/bin/ld: /tmp/ccz4DEEy.o: in function `main':
test.cpp:(.text+0xe): undefined reference to `new_hwaddr(char*)'
collect2: error: ld returned 1 exit status
```

也就是说：

当我们想要将 C 库编译链接到 C++ 中时，需要将接口的相关声明进行包裹：

```c
#ifdef __cplusplus
extern "C"
{
#endif
// C 接口函数
#ifdef __cplusplus
}
#endif
```

此时，我们再次将`network.c`和`nl.c`编译成`.o`文件，然后与`test.cpp`一起编译`.o`文件，就可以成功编译了。
# 创建容器网络

基于上一节关于 Docker 网络原理的内容，我们可以总结出以下步骤，以使我们创建的容器支持网络：

1. 创建一对虚拟网络设备 veth1/veth2；
2. 设置 veth1 的 MAC 地址；
3. 将 veth1 添加到网桥 labex0；
4. 激活 veth1；
5. 创建子进程；
6. 将 veth2 移动到子进程的网络命名空间并将其重命名为 eth0；
7. 等待子进程完成；
8. 删除网络设备 veth1 和 veth2；

所以我们必须进一步优化`start()`逻辑。

首先，我们应该在`docker::container_config`中添加网络相关配置：

包含头文件：

```cpp
#include <net/if.h>     // if_nametoindex
#include <arpa/inet.h>  // inet_pton
#include "network.h"
```

添加`docker::container_config`配置：

```cpp
// Docker 容器启动配置
typedef struct container_config {
    std::string host_name;      // 主机名
    std::string root_dir;       // 容器根目录
    std::string ip;             // 容器 IP
    std::string bridge_name;    // 网桥名称
    std::string bridge_ip;      // 网桥 IP
} container_config;
```

然后，在`main.cpp`中设置容器 IP、要添加的网桥名称为`docker0`以及网桥的 IP：

```cpp
int main(int argc, char** argv) {
    std::cout << "...start container" << std::endl;
    docker::container_config config;
    config.host_name = "labex";
    config.root_dir  = "./labex";

    // 配置网络参数
    config.ip        = "192.168.0.100"; // 容器 IP
    config.bridge_name = "docker0";     // 主机网桥
    config.bridge_ip   = "192.168.0.1"; // 主机网桥 IP

    docker::container container(config);
    container.start();
    std::cout << "stop container..." << std::endl;
    return 0;
}
```

让我们根据上述加载网络设备的逻辑重构`start()`方法：

```cpp
private:
    // 保存容器网络设备以便删除
    char *veth1;
    char *veth2;
public:
void start() {
    char veth1buf[IFNAMSIZ] = "labex0X";
    char veth2buf[IFNAMSIZ] = "labex0X";
    // 创建一对网络设备，一个加载到主机上，另一个在子进程中移动到容器中
    veth1 = lxc_mkifname(veth1buf); // lxc_mkifname API 要求在虚拟网络设备名称中至少添加一个“X”以支持随机创建虚拟网络设备
    veth2 = lxc_mkifname(veth2buf); // 这是为了确保正确创建网络设备。有关详细信息，请参阅 network.c 中 lxc_mkifname 的实现
    lxc_veth_create(veth1, veth2);

    // 设置 veth1 的 MAC 地址
    setup_private_host_hw_addr(veth1);

    // 将 veth1 添加到网桥
    lxc_bridge_attach(config.bridge_name.c_str(), veth1);

    // 激活 veth1
    lxc_netdev_up(veth1);

    // 容器创建前的一些配置工作
    auto setup = [](void *args) -> int {
        auto _this = reinterpret_cast<container *>(args);
        _this->set_hostname();
        _this->set_rootdir();
        _this->set_procsys();

        // 配置容器内部的网络
        //...

        _this->start_bash();
        return proc_wait;
    };

    // 使用 clone 创建容器
    process_pid child_pid = clone(setup, child_stack,
                      CLONE_NEWUTS| // UTS 命名空间
                      CLONE_NEWNS|  // 挂载命名空间
                      CLONE_NEWPID| // PID 命名空间
                      CLONE_NEWNET| // 网络命名空间
                      SIGCHLD,      // 子进程退出时会向父进程发送信号
                      this);

    // 将 veth2 移动到容器并将其重命名为 eth0
    lxc_netdev_move_by_name(veth2, child_pid, "eth0");

    waitpid(child_pid, nullptr, 0); // 等待子进程退出
}
~container() {
    // 记住在退出时删除创建的虚拟网络设备
    lxc_netdev_delete_by_name(veth1);
    lxc_netdev_delete_by_name(veth2);
}
```

> 注意：在`clone`中添加`CLONE_NEWNET`。

从上述步骤可以看出，在创建网络设备后以及创建子进程期间，我们需要与外部网络设备配合在容器内部进行相关配置：

1. 激活容器内部的`lo`设备；
2. 配置`eth0`的 IP 地址；
3. 激活`eth0`；
4. 设置网关；
5. 设置`eth0`的 MAC 地址；

```cpp
private:
void set_network() {

    int ifindex = if_nametoindex("eth0");
    struct in_addr ipv4;
    struct in_addr bcast;
    struct in_addr gateway;

    // IP 地址转换函数，用于在点分十进制和二进制之间转换 IP 地址
    inet_pton(AF_INET, this->config.ip.c_str(), &ipv4);
    inet_pton(AF_INET, "255.255.255.0", &bcast);
    inet_pton(AF_INET, this->config.bridge_ip.c_str(), &gateway);

    // 配置 eth0 的 IP 地址
    lxc_ipv4_addr_add(ifindex, &ipv4, &bcast, 16);

    // 激活 lo
    lxc_netdev_up("lo");

    // 激活 eth0
    lxc_netdev_up("eth0");

    // 设置网关
    lxc_ipv4_gateway_add(ifindex, &gateway);

    // 设置 eth0 的 MAC 地址
    char mac[18];
    new_hwaddr(mac);
    setup_hw_addr(mac, "eth0");
}
```

然后，在容器的`setup`中调用此方法：

```cpp
……
_this->set_procsys();
_this->set_network();   // 容器内部网络配置的配合
_this->start_bash();
return proc_wait;
```

此时，由于我们已经开始使用`network.o`和`nl.o`编译链接文件，让我们编写一个非常简单的`Makefile`：

```makefile
C = gcc
CXX = g++
C_LIB = network.c nl.c
C_LINK = network.o nl.o
MAIN = main.cpp
LD = -std=c++11
OUT = docker-run

all:
    make container
container:
    $(C) -c $(C_LIB)
    $(CXX) $(LD) -o $(OUT) $(MAIN) $(C_LINK)
clean:
    rm *.o $(OUT)
```

> 注意：Makefile 中的命令应以 Tab 开头而不是空格。这是因为 Markdown 解释器会将 Tab 转换为四个空格。编写 Makefile 时，请务必使用 Tab 而不是四个空格。否则，Makefile 将提示错误“Makefile:10: \*\*\* missing separator. Stop.”

再次编译并执行，然后进入容器。我们可以使用`ifconfig`检查网络：

```bash
labex:project/ $ make
make container
make[1]: Entering directory '/home/labex/project'
gcc -c network.c nl.c
g++ -std=c++11 -o docker-run main.cpp network.o nl.o
make[1]: Leaving directory '/home/labex/project'
labex:project/ $ sudo./docker-run
...start container
root@labex:/# ifconfig
eth0      Link encap:Ethernet  HWaddr 00:16:3e:da:01:72
          inet6 addr: fe80::dc15:18ff:fe43:53b9/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:38 errors:0 dropped:0 overruns:0 frame:0
          TX packets:9 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:5744 (5.7 KB)  TX bytes:726 (726.0 B)

lo        Link encap:Local Loopback
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)
```
