**写在前面**

卫星测高数据处理方面的知识，基本上都是自己看资料摸索的，所以不可避免的会有很多的不足（如果我有地方说的不明白望谅解），所以我把这些放在[GitHub](https://github.com/shanchuantian/altimetry/blob/master/DataProcessing/%E5%9F%BA%E6%9C%AC%E9%9B%B7%E8%BE%BE%E6%B5%8B%E9%AB%98%E5%B7%A5%E5%85%B7%E7%AE%B1BRAT(Basic%20Radar%20Altimetry%20Toolbox)%E7%AE%80%E5%8D%95%E7%9A%84%E4%BD%BF%E7%94%A8%E8%AF%B4%E6%98%8E.md)上面，希望有人去`fork`和`pull request`，让其更完善，也希望有人可以和我讨论，把这些数据处理经验都总结起来，如果后来人跟我们有一样的困惑不至于找不到答案。

英文很重要，没有办法，软件是老外搞的，界面、说明文档等都英文的，如果英文好，看文档绝大部分问题应该都可以解决。我的英文水平也不好，所以看起来也有些费力，但是没有办法。所以我想做的事情，不是把英文翻译成中文，而是做资源的整合，知识脉络的整理，问题的讨论。

# 1.简介

 ![1](http://oei6q3yqv.bkt.clouddn.com/2016/10/28/brat/1.png)

**BRAT**（Basic Radar Altimetry Toolbox，基本雷达测高工具箱），[官网](http://www.altimetry.info/toolbox/)，[下地地址](http://www.altimetry.info/toolbox/install_yes/)，截至2016年10月28日其版本号为v3.3.0。

BART为开源软件，其源码挂在GitHub（[地址](https://github.com/BRAT-DEV/main)）上，由**欧空局(ESA)**与**法国空间局(CNES)**共同开发。主要功能（官网介绍）：

- **读取卫星测高数据**（包括： [ERS](http://www.altimetry.info/glossary_/ers/)-1 and 2, [Topex/Poseidon](http://www.altimetry.info/glossary_/topexposeidon/), [Geosat](http://www.altimetry.info/glossary_/geosat/) Follow-on, [Jason-1](http://www.altimetry.info/glossary_/jason-1/), [Envisat](http://www.altimetry.info/glossary_/envisat/), Jason-2, [Cryosat](http://www.altimetry.info/glossary_/cryosat/) and Sentinel-3）。
- **做一些处理和计算**。
- **显示结果（图形界面）**。

**下载** ：

其下载需要注册，过程不算麻烦，注册后即可下载，如果读者不想注册，此处提供一个`brat-3.3.0-x64-installer`(链接：http://pan.baidu.com/s/1o8hf53c 密码：07c4)

# 2.一些简单操作

操作手册《brat_user_manual》里面有它的详细用法，我不作翻译了，简单说一下使用的流程和一些注意事项。

基于我用到的功能，做如下介绍：

1. BRAT界面的简单介绍。
2. 数据读取导出。
3. 筛选
4. 用到的视图

## 2.1 BRAT界面

 这是BRAT的界面：

![2](http://oei6q3yqv.bkt.clouddn.com/2016/10/28/brat/2.png)

用BRAT的操作流程可以概括为4个步骤（参看《brat_user_manual》16页）：

1. **创建Workspace（工作空间）**
2. **创建Dataset （数据集）**
3. **创建Operation （操作）**
4. **创建View （视图）**

这几个功能分别对应于界面上的选项卡。

需要**注意**的是，这4个步骤是一个递进的关系，后面的**依赖**于前面的。比如，workspace没有创建就无法加载Dateset，没有Dateset就无法进行Operation，没有Operation就无法创建View。这其实是很自然的一个逻辑关系。

## 2.2 创建Workspace（工作空间） 

BRAT处理的策略很简单，使用工作空间，将与该项目相关的所有信息（比如，数据的路径、筛选条件等）都存放在该文件夹下。

从菜单`Workspace`->`New`即可创建一个工作空间，**路径及名称不建议使用中文**，我无法确定使用中文是否会导致意想不到的问题。

创建后会在相应路径下生成文件夹及文件(我的目录：F:\BRATworkspace\test)：

```
│  setup.ini
│
├─Datasets
│      setup.ini
│
├─Displays
│      setup.ini
│
├─Formulas
│      setup.ini
│
└─Operations
        setup.ini
```

---

`setup.ini`是配置文件，记录配置信息。下面是测试，对理解软件是如何工作的有帮助，对实际操作无太大影响（不看也不会影响软件的使用）：

新建一个工作空间后，不做任何操作，该工作空间是空的，查看各个`setup.ini`文件内容：

```ini
; test\setup.ini内容
[General]
WorkspaceName=test
WorkspaceLevel=1
; test\Datasets\setup.ini内容
[General]
WorkspaceName=Datasets
WorkspaceLevel=2
; Displays、Displays、Operations下的内容大同小异
```

可以看到，`setup.ini`文件存放了工作空间的名称和工作空间的等级，test是首级所以Level=1，而Datasets是其下面的一级所以Level=2，从目录树就可以反应出来。

现在创建数据集、加载数据，保存工作空间，Datasets文件夹下的`setup.ini`文件内容发生如下变化：

```ini
; test\Datasets\setup.ini内容
[General]
WorkspaceName=Datasets
WorkspaceLevel=2
[Datasets]
DatasetName1=Datasets_1
DatasetName2=Datasets_2
DatasetName3=Datasets_3
[Datasets_1]
File1=F:\\JA2_GPS_2PdP002_138_20080727_074001_20080727_083614.nc
[Datasets_2]
File1=F:\\JA2_GPS_2PdP003_138_20080806_053833_20080806_063445.nc
[Datasets_3]
File1=F:\\JA2_GPS_2PdP001_138_20080717_094129_20080717_103742.nc
File2=F:\\JA2_GPS_2PdP002_138_20080727_074001_20080727_083614.nc
File3=F:\\JA2_GPS_2PdP003_138_20080806_053833_20080806_063445.nc
```

即使不懂配置文件的语法也能看出我做了什么事情。是的，我创建了三个数据集分别为Datasets\_1、Datasets\_2、Datasets\_3，在Datasets\_1和Datasets\_2中各有一个nc文件，在Datasets\_3中有三个nc文件。

---

## 2.3 创建Dataset （数据集）

第一个选项卡即`Datasets`，单击`New`按钮即可创建新数据集，所有按钮的功能基本看名字就能明白是做什么的。

示例数据：链接：http://pan.baidu.com/s/1qXKaU2C 密码：3rxl

![8](http://oei6q3yqv.bkt.clouddn.com/2016/10/28/brat/8.png)

在这个页面，单击一个nc文件，可以查看该文件中的所有字段的**名称**、**单位**、**维度**、**详细描述**等信息，但不能看到具体的数据（需要导出后查看）。用**HDF Explorer**可以查看数据结构，并且能直接查看数据，见：[用matlab读取测高卫星jason2的netCDF格式数据的简单示例（一）](http://blog.csdn.net/shanchuan2012/article/details/52823892)。使用BRAT前一定要知道nc文件的数据格式。

## 2.4 创建Operation （操作） 

Operation 做的事情就是，对选定的数据进行编辑（筛选），支持数据的导出。以下为一个示例：

### 2.4.1 选择需要的数据字段

新建一个Operation ，选定一个Dataset ，将纬度（lat）拖拽到X或者右键`Set as X`；经度（lon）、轨道高（alt）、测距值（range_20hz_ku)拖拽到Data下，或者右键`Set as Data`。

### 2.4.2 设置筛选条件

设置筛选条件的目的就是选出我们需要的数据，筛选条件有很多，比如将其限制在一定的经纬度范围内，测距值不为空，以及许多的flag，等等。

这就要涉及条件表达式和函数了，常见的有：

```matlab
!   % 非
!=  % 不等于
&&  % 并且
||  % 或者
<   % 小于
is_bounded % 限制范围，例如 is_bounded(22.0682,lat,22.3311) 限制纬度的范围
......
```

可以单击`Insert fuction`按钮查看所有的符号和函数。

最简单的例，只限制纬度范围，并且要求测距值不为缺省值（DV），那么表达式应该这样写(写在`Selection creiteria`里面)：

```matlab
is_bounded(22.0682,lat,22.3311) && range_20hz_ku != DV
```

![6](http://oei6q3yqv.bkt.clouddn.com/2016/10/28/brat/6.png)

在`Insert formula`中，给出了很多卫星的筛选条件，可以参考。

### 2.4.3 计算和导出

设置好筛选条件后，执行它，再按照我们的需要导出。执行单击`Execute`按钮，其实只需要点击`Export`，在导出的时候软件会根据筛选条件进行筛选。

如果需要做一些计算，比如计算**高程**，那么右键`Data`，`Insert an empty expression`并将其重命名为`height`，在右边的`Expression`框中输入：

```matlab
alt - range_ku
```

![7](http://oei6q3yqv.bkt.clouddn.com/2016/10/28/brat/7.png)

也可以单击`Insert formula`查看软件自带的一些计算公式，比如计算SSH（Sea Surface Height ）和SLA（Sea level anomalies ）。导出的时候就会增加一列`height`。

单击`Export`按钮，导出格式选择`Ascii`，即文本文件，此处导出的数据如下(为了整齐，我把每列保留的小数统一了)：

```matlab
alt (m)  	height (m)  	lat (degrees_north)  	lon (degrees_east)  	range_ku (m)  
1341754.087	34.2734	22.097624	129.760587	1341719.813
1341764.473	34.2311	22.146769	129.739138	1341730.242
1341774.879	34.1581	22.195909	129.717671	1341740.721
1341785.303	34.1056	22.245045	129.696186	1341751.198
1341795.746	33.9925	22.294177	129.674683	1341761.754
```

可以看到第二列即计算得到的海面高。

如果需要复杂的计算和操作，建议写程序。

还可以导出为**NetCdf**和**GeoTiff**，其中GeoTiff需要Y有值。但是我感觉这两个功能实在用不上，首先是NetCdf，我们就是因为NetCdf是二进制不能直接打开所以才需要这些软件的帮助，给果又导出为NetCdf，这不给自己添麻烦吗；GeoTiff中可以导出kml文件在Google Earth中查看，但是导出来的文件总有问题。

如果需要在Google Earth中查看导出来的点的位置，可以上网下载一个制作kml的软件，自己做，比较简单。如果觉得找起来比较困难，这里提供一个**kmlgenerator**：链接：http://pan.baidu.com/s/1jI2myAq 密码：g8hw，生成kml后直接双击它就会在Google Earth中中显示。

> 使用注意：
> excel格式要xls
> excel必须要有3个Sheet，并且依次为Sheet123（也就是默认的，不能改）
> 数据要有4列：经度、纬度、地名、备注（googleEarth点的格式吧）

**注意：**

如果是导出数据并做计算的话，尽量不要用到`Y`，设置一个`X`后其余数据全都设置为`Data`。以上面导出的结果为例，该例子以纬度（lat）作为`X`，导出的数据有5个点，每一行对应一个点。如果将经度（lon）设置为`Y`后是什么结果？明显，会有5*5=25行数据产生，这并不是我们想要的。

## 2.5 创建View （视图）

我觉得这个功能实在有点鸡肋，不过可以用一用，这里只做一个简单的介绍。

> 为什么说鸡肋？它的功能是绘图，一维折线图等可以用更专业的绘图软件来实现，再加以代码可以批量处理和细节化；如果是二维的附着在地图上，GMT可能更专业一些。

以前面的计算结果为例:

### 2.5.1 一维图（y=f(x)）

查看计算得到的海面高（height）按纬度从低到高分布的折线图：在`Views`选项卡中新建一个，在`Available`中选中height，添加到右边的`Selected`框中，单击`Execute`：

![3](http://oei6q3yqv.bkt.clouddn.com/2016/10/28/brat/3.png)

得如下结果：

![3](http://oei6q3yqv.bkt.clouddn.com/2016/10/28/brat/4.png)

可以设置线型、颜色、标题等等。

### 2.5.2 二维图（z=f(x,y)）

由于是二维的，这里要用到`Y`，将`Data`里面的经度（lon）删除后重新设置为Y，为了看的清楚我把经度范围扩大了：`is_bounded(2.0682,lat,22.3311) `。重新执行operation和view得如下结果：

![3](http://oei6q3yqv.bkt.clouddn.com/2016/10/28/brat/5.png)

可以看到海面高的分布，下面是它的色标，红色高、蓝色低。并且可以改变投影方式等等。

# 3. 一些问题

BRAT使用起来或许有如下问题：

1. 数据很多时，BRAT导出**速度慢**，但用matlab却很快，原因不明。
2. 导出的数据可能会有问题，比如cycle号出现了小数（cycle号只能是整数），原因不明。
3. 用BRAT会让人忽略掉**数据的结构**，如果我问测距值（range）是由什么确定的？它不是由经纬度确定的，1hz的range是由`time`一个维度确定的，20hz的range是由`time`和`meas_ind`两个维度共同确定的，而波形数据则是由`time`、`meas_ind`和`wvf_inf`三个维度共同确定的。
4. 如果你觉得一些筛选条件是有效的，可以记录起来，并作说明，免得以后忘了。





**最后**：

手册内容是英文，且内容太多，也许会让人产生一些困惑，但是多看就能理解。

我写的实在不够好，远远不如参考手册，希望不至于误导他人。如果你完整看完参考文档，使用软件的能力会远远超过我的。



**参考**：

《brat_user_manual》（比较全面的介绍）

《20090929_RApractical》（这里里面有10个操作实例，值得一看）

《brat_reference_manual_3.3.0》（代码接口的介绍，如果想用它的代码可以看看）

BRAT[官网](http://www.altimetry.info/toolbox/)
