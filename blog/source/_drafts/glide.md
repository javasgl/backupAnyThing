
 Golang挺好用的，但是目前比较受到诟病的是其没有自带包管理工具(golang社区已经发起了一个[dep](https://github.com/golang/dep)的项目，不过要正式被golang接受还需要一段时间)。官方没有咋办，那就自己造轮子呗。所以 go的包管理工具就五花八门、层出不穷了。目前有Glide, gopm, godep,gpm，gom等等。。。

这里介绍一个工具，使用比较简单: glide

我已经将其的文档翻译成了中文:  [glide 中文文档](http://glidedocs.readthedocs.io/).

翻译的内容放在github上: https://github.com/javasgl/GlideDocs

不想看文章的可以直接去看中文文档，文档写的很清晰，文档中关于`glide`的用法和原理都有说明。

## 安装

安装就没啥可以说的了，去下载安装即可了(http://glidedocs.readthedocs.io/zh/latest/#glide)

## 初始化

在你已有的go项目的根目录下直接运行:`glide init`，就会生成一个`glide.yaml`配置文件，配置信息的详细说明见[文档](http://glidedocs.readthedocs.io/zh/latest/glide.yaml/)。
运行`glide init`之后，`glide`自动分析当前项目里已有的依赖包的关系并自动生成了`glide.yaml`文件来管理这些依赖关系。
这个过程需要一段时间来运行，因为它在扫描你的代码在推算依赖关系，请耐心等待。
```
~/codes/xxxxx/xxxx/: glide init
[INFO]	Generating a YAML configuration file and guessing the dependencies
[INFO]	Attempting to import from other package managers (use --skip-import to skip)
[INFO]	Scanning code to look for dependencies
[INFO]	--> Found reference to github.com/astaxie/beego
[INFO]	--> Found reference to github.com/stretchr/testify/assert
[INFO]	--> Found reference to gopkg.in/olivere/elastic.v3
....
```
运行完成之后,`glide.yaml`中的内容如下：
```
package: .
import:
- package: github.com/astaxie/beego
- package: github.com/stretchr/testify
  subpackages:
  - assert
- package: gopkg.in/olivere/elastic.v3
```

## 添加依赖

运行`glide get`,类似于`go get`,不过不同的是，`glide get`会下载你指定包到`vendor/`目录中而不是之前的`GOPATH`之中。关于go的 `vendor/`目录请执行google，这是go 1.5版本之后的功能。
这个`glide get`下载包的同时，也会更新`glide.yaml`中的配置:
```
~/cods/xxxx/xxxx/:glide get https://github.com/garyburd/redigo
....
```
完成之后,`glide.yaml`也更新了：
```
package: .
import:
- package: github.com/astaxie/beego
- package: github.com/stretchr/testify
  subpackages:
  - assert
- package: gopkg.in/olivere/elastic.v3
- package: github.com/grayburd/redigo
```

## 更新依赖

运行`glide up`来更新`glide.yaml`中配置的依赖包。
首次运行的时候，会生成一个`glide.lock`文件，对php的`composer`熟悉的一样就知道这个锁文件是干嘛的了。这个锁文件是保证再次运行`glide up`的时候不会去远程获取更新，而是直接使用这个锁文件中定义的依赖包及其版本。在协作开发时，一般这个锁文件可以进行版本控制，这样其他人拿到这个锁文件之后，执行`glide up`则会获取锁文件中指定的包版本，起到多人协作开发时统一开发环境的问题。
```
~/cods/xxxx/xxxx/:glide up
[INFO]	Downloading dependencies. Please wait...
[INFO]	--> Fetching github.com/stretchr/testify.
[INFO]	--> Fetching gopkg.in/olivere/elastic.v3.
[INFO]	--> Fetching github.com/astaxie/beego.
....
```
## 其他

其他的一些命令主要是帮助管理这些依赖的。详细见 http://glidedocs.readthedocs.io/zh/latest/commands/
