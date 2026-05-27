# Docker 网络原理

之前，我们对 Docker 如何实现一个封闭的容器有了初步的了解。然而，我们也发现我们实现的 Docker 容器不支持网络访问，而且我们运行的不同容器之间也无法相互通信。

![Docker 容器网络桥接图](https://file.labex.io/namespace/718bace8-27a3-4200-a588-dde4041ceeb9/cpp/project-creating-a-simple-docker-container-in-cpp/lab-1/zh/../assets/document-uid29879labid2005timestamp1471251217443.png)

Docker 容器之间的网络通信原理是通过一个名为“docker0”的网桥来实现的。两个容器“container1”和“container2”各自有自己的网络设备“eth0”。所有的网络请求都会通过“eth0”进行转发。由于容器运行在子进程中，为了使它们的“eth0”接口能够通信，需要创建一对网络设备“veth1”和“veth2”，并将它们添加到“docker0”网桥中。这样，网桥就可以无条件地转发和路由容器内“eth0”接口产生的网络请求，从而实现容器之间的通信。

因此，为了使我们编写的容器具备网络通信能力，我们首先需要创建一个它们可以使用的网桥。为了方便起见，我们将直接使用环境中现有的“docker0”。
