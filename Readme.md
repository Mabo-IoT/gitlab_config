本项目已经尽量脱敏了。应该没啥敏感要素吧。

# gitlab实践操作，etc

作为一个目录，给与一些建议操作。

## gitlab的搭建

**本例主要使用私有证书，其中有很多私有证书相关内容。**

### 首先把gitlab搭建起来：

​	[gitlab搭建指南](gitlab搭建指南.md)

### 作为gitlab最实用的功能--ci，启动一个ci的执行者，gitlab-runner:

> 参见gitlab项目中的`intermediate_runner`的`Readme.md`

​	PS：作为实践项目，gitlab中使用的是内网环境，因此需要一个download镜像，将镜像上传gitlab，执行ci的过程。如果为外网环境，则可以在ci中省去上传镜像过程，整合至ci自动化中，请根据使用环境自行修改ci。

### 导入git项目，在docker环境中进行干干净净的git clone：

> 详见gitlab项目中的`git` 或`alpine_git`项目。
>

### ci编写：

CI作为gitlab最大的福利之一，可以极大程度把重复性劳动交给电脑，减少人力，同时也减少因人导致的错误。

CI的触发默认为提交即触发。同时，gitlab提供许多用于ci的环境变量，在保证自动化效率的同时，最大程度地保证安全性。

![gitlab ci](.\jpg\cicd_pipeline_infograph.png)

详见 [CI文件编写指南](CI文件编写指南.md)

### 示例套件：

[patac套件](patac套件.md)