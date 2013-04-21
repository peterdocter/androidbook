<http://stackoverflow.com/questions/80622/maven-or-ant>

Ant还是Maven？

maven1是构建在ant之上的，所以用起来还是会处理一些关于ant的问题。maven2就不会啦～

Repeatable builds / Dependency Management. All it takes is a check out of your source and, if necessary, a Maven configuration file, and you can issue one command and all of the dependencies for your project will be downloaded and then the project will be built. No checking of binary jars into your source control, or manually copying things, or whatever. Also, it handles transitive dependencies. If you depend directly on one library, you don't have to list its dependencies as well. Maven automatically figures that out for you.

- 可重用的构建/依赖管理。要做的只要检查你的源文件，最多加一个maven配置文件。你可以用一个命令下载所有的依赖并构建你的项目。不用在你的源码控制中检查二进制的jars，或手动复制或者其他的。并且，它处理传递的依赖。如果你的依赖在一个目录，你不用列出所有的依赖，maven会帮你计算。


- 叙述结构。任何给出的maven项目都要有版本号。这样就很容易指向任何版本。

- 约定和配置重于脚本。

maven有一系列项目布局的约定，用ant你要写一大堆xml。

- IDE。确实，很多IDE集成了ant并且基于ant。很多人把构建文件（如Eclipese的.project）放进了版本控制系统里面，这很糟糕，因为绝对路径可能会出错。用maven的话，你可以马上生成对于Eclipese和IDEA的项目文件，并且其它像Netbeans这样的IDE可以简单的打开已经存在的Maven项目。不用检查版本控制系统里面的配置文件了。

这就是我喜欢maven的理由，但是maven还是不够完美：

- 缺少文档
- 有时候你要做一些笨活，因为依赖计算机制搞出的bugs
- 有时候比较慢（听说已经在改进了）

IDEs Yes, many ides have Ant integration or are based upon ant. Yet, I find that many people who use ant built scripts tend to check in IDE project files (.project for Eclipse, for example) into source control. This is often bad because of pathnames and so forth. With Maven, out of the box you can generate project files for Eclipse and IDEA, and other IDE's like Netbeans can simply open existing Maven projects. No need to check configuration files into source control.
Those are my main reasons for advocating Maven. However, as others have noted, it is far from perfect:

The documentation is lacking in places.
Sometimes you have to do silly things to work around bugs in the dependency calculation mechanism.
It can be slow at times (though I hear this is being worked on).
The declarative dependency management and version artifacts alone have got me sold on Maven.


我们选择Maven2因为：ant是构建工具，maven是构建系统