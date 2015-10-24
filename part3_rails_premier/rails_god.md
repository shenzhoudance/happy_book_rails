#Rails god后台进程管理监控框架

**god**是使用ruby编写的一个管理监控**后台服务进程**的框架。它的主要作用就是对后台的服务进程进行监控，并且
在一定程度上进行管理和维护，当然这个后台服务进程可以是一个rails服务进程。god的监控功能，来源于它可以时时的
反应的当前后台服务进程的状态；而其管理功能则来源于god可以编写自己的**命令脚本**对后台服务进程进行一些操作。

在god出现之前，这里也有许许多多的方法来管理和监控后台服务进程，普遍被采用的方法是编写后台服务
进程的**shell**型的命令脚本，或者是**crontab**型的命令脚本。(crontab是指crontab文件，这个文件包含cron服务命令，它的
作用是在Unix或者类Unix的系统中在后台**周期性**的执行被包含在文件中的命令，它的本质就是Linux中内置的**计
划任务**服务)有关于crontab的更多详情请参考：

    http://baike.baidu.com/link?url=iYF1tptR31skjI2BtL5mlUpUMJ9eULeu3WORjnTqo0m4brTZN-5QAxD9jUhB6gmlSL4PYvH40VaCuFpyCCFsHq
    http://baike.baidu.com/link?url=6ZhysWXsdAaRTTWBalNT6L0FPktKUBJ806D3xUKMLvo6dYjWtpa2Ddr7gTZb3l3q0ftG6glXCLx_Y-MGwV6To_

除了编写脚本命令之外，还有一个方法就是为后台服务设置一个进程crush失败通知邮件方法，但是这个方
法完全没有任何实际意义；因为我们要做的就是防止这种情况的发生，而不是当这种情况发生了我们只是被
通知而已。除此之外，这些方法还有许许多多的缺点，比如说：我们的命令脚本很多时候并不总是那么完美，它们
或多或少的都有一些毛病；更重要的一点是这些我们自己写的脚本很可惜的往往只能针对一个应用进程，毫无可移植性。

god应运而生的目的就是解决这些传统方法的问题。因为它使用ruby编写，这使得它十分简单和灵活，并且更加有效。
首先因为ruby语言的灵活性，我们能编写许多属于自己需要的条件的功能；其次god不仅支
持**poll()**函数也支持**事件驱动**。(poll()函数是Unix/Linux系统的一个回调函数，其本身也是基于事件驱动的。
详情请访问：http://www.tutorialspoint.com/unix_system_calls/poll.htm )

另外，god有一个强大的集成系统通知，对于每一个notofication都有其详尽的描述；而且对于**非守护进程**，god同
样能编写命令对其有一个很好的控制。god的最终目的就是为了让后台进程服务管理监控更加简便和自动化，安装god也
十分简单，因为god是使用ruby编写的，即也可以使用ruby gem来安装。

```
$ sudo gem install god
```

god的使用方法也很简单，网上有许多资源可以参考，这里便不再赘述。以下是一些参考，对于大家了解和使用god很有帮助：

① 关于god的本质和基础教程：http://godrb.com/

② god的实际运用：http://www.synbioz.com/blog/monitoring_server_processes_with_god

③ god在github上的开源项目：https://github.com/mojombo/god/

**TODO：请使用一个简单的rails例子来说明god的工作机制。**
