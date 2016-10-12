[TOC]

如何生成目录？

# OSTM/Jason-2产品手册

**说明**：

- 所有图片均存储在七牛云，从本地转为外链时，将“prefix_avoid_same_name_pic\”替换为“http://oex6dwgjw.bkt.clouddn.com/”


- 有译者注释的地方有如下标记：(*)
- 业余时间翻译，进度慢。



## 1.  简介

OSTM/Jason-2是jason-1的后续任务。这个卫星的名字是以希腊神话中的一位英雄的名字命名的，该英雄出自于阿尔戈英雄，指的是希腊传说中同伊阿宋（*）（jason）一道乘快船“阿尔戈号”去科尔基斯（Colchis）的阿瑞斯圣林取金羊毛的50位英雄，jason是首领。OSTM/Jason-2接管并延续TOPEX/Poseidon和Jason-1的任务。TOPEX/Poseidon和Jason-1任务由法国太空局（CNES）和美国国家航空和宇宙航行局（NASA）两个机构共同管理。OSTM/Jason-2的除了上面的两个机构，还有2个新成员：欧洲气象卫星开发组织（EUMETSAT）以及美国国家海洋和大气管理局（NOAA），为的是促进向全面的测高任务的过渡，能够满足运行程序对数据的时效性和可靠性的需求。

> 译者：伊阿宋（拉丁文：Easun，英：Jason）英译后按英语发音就翻译成了“杰森”。

### 1.1. OSTM/Jason-2产品家族的概述

这分文档的目的是通过对产品**内容**和**格式**进行综合描述，来帮助用户使用OSTM/Jason-2产品（3种产品：可使用的地球物理数据记录（OGDR）; 临时地球物理数据记录（IGDR）；球物理数据记录（GDR））。当资料与3种产品都相关的时候，我们会标：(O)(I)GDR。

(O)(I)GDR产品除了以下数据有差别，其余是完全相同的，有差别的数据是处理中的辅助数据（Auxiliary Data）：



**表0 O/I/GDR产品辅助数据的区别**

| 辅助数据          | 影响参数                            | OGDR     | IGDR             | GDR                      |
| ------------- | ------------------------------- | -------- | ---------------- | ------------------------ |
| 轨道            | 卫星高度，多普勒校正…                     | DORIS导航器 | 初步的（MOE用DORIS数据） | 精确的（POE使用DORIS/激光/GPS数据） |
| 气压计字段         | 干/湿对流层改正，U/V风矢量，地面气压，逆气压改正      | 预测的      | 恢复的              |                          |
| 极点位置          | 极潮高度                            | 预测的      | 恢复的              |                          |
| Mog2D         | HF ocean dealiasing  correction | 无法使用     | 初步的              | 精确的                      |
| GIM           | 电离层校正                           | 无法使用     | 可用               |                          |
| 辐射计天线温度的多项式系数 | 湿对流层改正，Sigma0降雨衰减，…             | 初步的      | 精确的（辐射计定标）       |                          |

       GDR产品与OGDR和IGDR产品不同，它是完全验证过的产品。

#### 1.1.1. 产品内容

这个任务（jason2）的2级产品包含9个不同类型的地球物理数据记录（GDRs）。如表1所示，GDRs有3个家族，由**延迟**和**精度**的上升来区别，从OGDR到IGDR再到GDR，发布数据的延迟时间越长，精度越高。与这3种数据对应的，在NetCDF格式中有3种文件与之对应，其大小和复杂程度依次增加：

1.经过简化的1HZ数据


2.原始的[NetCDF格式](undefined)数据集（(O/I/GDRs），包括1HZ数据和20HZ数据。


3.专业的传感器产品，NetCDF格式（SIGDR/S-GDR，对OGDR不可用）中包括所有的雷达回波。

对于OGDR家族，它提供一种特别的产品，是BUFR格式的，只有1HZ的数据是可用的，设计的目的是满足要实时获取观测数据的机构的需求，比如世界气象组织（WMO）需要标准化的格式用于数值天气预报（NWP）模型的同化。

![table1](http://oex6dwgjw.bkt.clouddn.com/table1.png)

（所有的文件都在NetCDF中除了OGDR-BUFR，这个不包括20HZ的数据）

**表1. Jason-2运行的（？operational）、过渡的、最终的地球物理记录产品（(O/I/GDR)）的概要**

这9个文件都包含海面高，海面风速，有效波高信息和所有需要的改正项。NetCDF文件的不同之处在于辅助数据的数量和类型，产品格式都是一样的。

#### 1.1.2. 文件名的约定

产品名称基于以下约定：

JA2__\<O/I/G>P\<N/R/S>_2P\<v>\<S/P>\<ccc>\_\<ppp>_\<yyyymmdd_hhnnss>_\<yyyymmdd_hhnnss>.nc

其中：

\<O/I/G>:产品家族（O:OGDR,I:IGDR, G:GDR）

\<N/R/S>:产品类型（N:原始的，R:简化的，S: 传感器）

\<v>：产品版本（‘T’：检验和验证阶段；‘c‘：jason-1的数据标准，以及jason-2刚开始向用户发送的时候；‘d’：再处理后发送的）

\<S/P>：产品持续时间(S : segmentfor OGDR, P : pass for I/GDR)

\<ccc>：第一个(*)产品记录的cycle number

> 译者：可以把它理解成一条pass上的第一记录

\<ppp>：[第一个产品记录](undefined)（1-254）的pass number

\<yyyymmdd_hhnnss>:第一个产品记录日期

\<yyyymmdd_hhnnss>：最后一个产品记录日期

因此对于OGDR我们有：

OGDR:JA2_OPN_2PvSccc_ppp_yyyymmdd_hhnnss_yyyymmdd_hhnnss.nc

OGDR-SSHA:JA2_OPR_2PvSccc_ppp_yyyymmdd_hhnnss_yyyymmdd_hhnnss.nc



对于OGDR-BUFR，如果你通过GTS访问文件，文件名是不相关的（具体的访问NRT数据的方法见下面）。另外，如果你是通过档案（CLASS或者UMARF）或者EUMETCast访问文件，OGDR-BUFR的文件名如下：

在NOAA/CLASS中：

JA2_OPB_2PvSccc_ppp_yyyymmdd_hhnnss_yyyymmdd_hhnnss

在EUMETSAT/UMARF和EUMETCast中：

W_US–NOAA–Washington,SURFACE+SATELLITE,JASON2+OGDR_C_KNES_yyyymmddhhnnss_v_ccc_ppp_yyyymmddhhnnss.bin

W_XX-EUMETSATDarmstadt,SURFACE+SATELLITE,JASON2+OGDR_C_EUMS_yyyymmddhhnnss_v_ccc_ppp_yyyymmddhhnnss.bin

对于IGDR：

IGDR: JA2_IPN_2PvPccc_ppp_yyyymmdd_hhnnss_yyyymmdd_hhnnss.nc

IGDR-SSHA: JA2_IPR_2PvPccc_ppp_yyyymmdd_hhnnss_yyyymmdd_hhnnss.nc

S-IGDR:JA2_IPS_2PvPccc_ppp_yyyymmdd_hhnnss_yyyymmdd_hhnnss.nc

对于GDR：

GDR: JA2_GPN_2PvPccc_ppp_yyyymmdd_hhnnss_yyyymmdd_hhnnss.nc

GDR-SSHA:  JA2_GPR_2PvPccc_ppp_yyyymmdd_hhnnss_yyyymmdd_hhnnss.nc

S-GDR: JA2_GPS_2PvPccc_ppp_yyyymmdd_hhnnss_yyyymmdd_hhnnss.nc

### 1.2. 手册概述

这是一个数据使用向导和参考手册的结合，所以不是所有的读者都需要阅读所有的章节。

第一章：提供产品发展历史信息

第二章：提供(O)(I)GDR和这个文档的背景信息。

第三章：OSTM/Jason-2任务的概述

第四章：如何使用OSTM/Jason-2数据的简介

第五章：OSTM/Jason-2高度计算法的简介

第六章：提供了OSTM/Jason-2(O)(I)GDRs数据的目录和格式的描述

附录A：包含的引用

附录B：包含的缩写

附录C：描述如何从CNES, EUMETSAT，NOAA订购信息和数据，并列出相关网站。

### 1.3. 参考文档和贡献者

当你使用这份文档的时候，请使用以下引用：

“OSTM/Jason-2 ProductsHandbook”，

CNES :                        SALP-MU-M-OP-15815-CN

EUMETSAT :              EUM/OPS-JAS/MAN/08/0041

JPL :                           OSTM-29-1237

NOAA/NESDIS :        Polar Series/OSTM J400

其他的贡献者包括：

J.P.Dumont, L. Carrère and V. Rosmorduc from CLS

J. Lillibridgeand Remko Scharroo from NOAA

S. Desai fromNASA/JPL

H. Bonekamp andJ. Figa from EUMETSAT

E. Bronner, A.Couhert and N. Picot from CNES

### 1.4.约定

#### 1.4.1.术语

##### 1.4.1.1.测高距离

为了减少论述测高值和改正时产生的困惑，这份文档中下面这些术语是这样定义的：

-  Distance（距离）和Length（长度），和平常的术语是一个意思。
-  Range（测距值）指从卫星高度计到地球表面的距离，因此高度计测量值用“range”或者“altimeter range”来表示。
-  Altitude（高度）是卫星（或者高度计）相对于一个参考点的距离。这个参考点使用的是参考椭球。这个距离从卫星星历数据计算得到。（注：说的就是轨道高）
-  Height（高度）指海平面到参考椭球的距离。海平面高是在参考椭球的基准下由卫星的高度减去测距值得到。


 ![pic1](http://oex6dwgjw.bkt.clouddn.com/pic1.png)


**图1 Altimetric distances – Altitude, Rangeand Height**

##### 1.4.1.2.Orbits, Revolutions, Passes, and Repeat Cycles（专有词汇，不译）

一个**Orbit**指的是卫星绕地球一圈，从一个升交点（**ascending node**）到下一个[升交点](undefined)。升交点指的是星下点（**sub satellite point**）从南到北跨过赤道时与赤道的交点。**Revolution** (REV)和orbit的意思相同。

OGDR数据被组织进了文件“segments”，相当于把数据倾倒进地球上的终端（特别是2小时数据集）。

(I)GDR数据被组织进了“pass”文件，为的是避免在海洋中间产生数据边界（如果按照“orbit”来组织数据就会产生边界）。一个“pass”是卫星revolution的一半，从最南纬到最北纬。

对于OSTM/Jason-2，一个上升的pass（Ascending Pass）是从南纬66.15度到北纬66.15度。一个[下降的pass](undefined)（Descending Pass）与之相反是从北纬66.15度到南纬66.15度。在OSTM/Jason-2中的每个cycle中，这些pass从1到254编号。上升的pass是奇数，下降的pass是偶数。

经过一个完整周期（cycle）后（有**254**个pass），OSTM/Jason-2开始重复地面轨迹，但是有**±1 km**左右的偏差。这意味着每个沿轨迹上的点重复测量的周期大概是**9.9**天。

##### 1.4.1.3.参考椭球

参考椭球是地球非球面形状的一阶定义，赤道半径为6378.1363km，扁平系数是1/298.257（与T/P和jason-1的参考椭球一样）。

#### 1.4.2.改正约定

所有的环境和仪器改正都被计算了，所以它们应该被加到改正量里面去。也就是，将改正应用到测量值上：

改正后的量 = 测量值 +　改正数

这意味着对测高距离的改正（影响信号路径的延长）（例如：湿对流层改正）算出来是负数。把这个负数加到未改正的距离上来减小原来的测距值。例如：改正后的range = 测量range + range改正项。

#### 1.4.3.时间约定

时间是UTC时间，参考2000年1月1日00:00:00.00。

一个UTC跳秒发生在一年的7月30日或者12月31日。跳秒的[详细介绍](http://baike.baidu.com/link?url=VjgpSQOdscBX74NT4KnIZJQjRzHqSV4Nyer-s6vOHP1GQk30HtdWwYPgNh54thDg4bMPaefVZan3K2EkplyPqISPzaVpOM55YRZ5qyYmrYVIqkNie6jZUUuhIZtXy30wKolQ9DGtLij76PTWad5QtK)。

#### 1.4.4.单位约定

所有的距离和距离改正都是以0.1mm为单位。（？）

#### 1.4.5.标记和编辑

一般来说，标记（flagging）由3部分组成：[仪器标记](undefined)（开/关），[遥测标记](undefined)（初步的标记和编辑），数据[质量标记](undefined)（地球物理处理标记）。

仪器标记（Instrument flags）提供卫星上各种仪器的状态信息。

[遥测](undefined)标记（Telemetry flags）是基于仪器模型的并检测遥测数据质量。只有质量非常差的数据才不会被处理（例如：地面数据不能被读取）。标记设置（Flag setting）被设计出来是为了获得最多的传感器数据记录（SensorData Records）（SGDR数据集的一部分）。只有当高度计在跟踪模式下才会处理科学数据。

质量标记（Quality flags）由各种各样的经过平滑或者残差统计检验决定。当数据的缺口（gap）被检测到的时候就会[设置标记](undefined)，残差超过了预设的阈值也会设置标记，数据的梯度超过了预设的阈值也会设置标记。

## 2.OSTM/Jason-2任务概述

### 2.1.背景

前面的2个高精度测高任务：TOPEX/POSEIDON（T/P，于1992年8月发射，2005年10月结束使命），Jason-1（2001年12月发射）从科学研究和应用两个方面来看，已经成为物理海洋学主要转折点的主要元素。T/P由于在精度方面超越了最初的规格，已经成了唯一理解海洋环流，并对其进行建模方面做出显著提升的工具。T/P任务的成功主要是由于系统的适当优化：仪器、卫星、轨道参数都是为了完成任务而专门设计的。T/P的后续任务jason-1在确定了提供与T/P相同表现的目标后马上就约定好了，jason-1的重量更小，需要的能量也更少，因此任务花费也更低。此外，这个任务的主要目的包括了准实时的应用。

两个任务分别或者结合起来对其他领域做出了重要的贡献。比如，平均海平面测量，潮汐，海洋气象学，地球物理学，测地学。最显著的是串行任务阶段，jason-1和T/P在同一个轨道上飞行，时间间隔是70s，这样提供了一个独特的机会来对各自的系统相互校准，提高将来科学数据的质量。高精度的雷达测高任务是进行全球连续的海洋观测、更好地理解海洋循环短周期和长周期变化的唯一可行方式。它们现在被认为是是全球海洋观测系统至关重要的组成部分。这些系统集成测高的、其他卫星的以及实地观测的数据到模型里面，需要海洋测量的连续性和持续性来生成数十年的时间序列。

Jason1准实时、周期短（ENVISAT也是），为展示运转上的（operational）海洋观测产品和用于各种应用（船舶线路定制、环境危机、支持海洋产业）的短期海洋预测日俱增的重要性提供了几个试点实验。

值得提到的是，测高系统提供的海洋上的波浪和风的数据的重要性。这个信息在气象学、研究实体（research entities）、其他数据的校准、天气和海浪模型同化、统计分析等方面是常规应用。

### 2.2.OSTM/Jason-2任务

OSTM/Jason-2任务是T/P 和 Jason-1的继承，有以下2个drivingambitions（太难翻译了）：

-  确保海洋科学高质量测量的连续性。

-  为同化和预测应用提供经营产品（operationalproducts）。

主要参与的机构如下：

-  CNES提供了平台（platform）和荷载模块（[见2.4节](undefined)）。

-  NASA和CNES一直提供荷载仪器（见2.4节）。

-  NASA提供卫星的发射设备。

-  CNES提供卫星命令和控制中心（欧洲地球终端和数据处理中心），[任务的归档（archiving）和发布的基础设施。](undefined)

-  NOAA提供卫星的控制中心，指令和数据汇集（CDA）站和数据处理，任务的归档（archiving）和发布的基础设施。

-  EUMETSAT提供一个调节欧洲地球终端的站和基础设施，为的是将其整合进EUMETSAT地面段基础设施和数据处理，任务的归档和准实时产品发布基础设施。

OSTM/Jason-2于2008年6月20日 7:46发射，于2008年7月4日到达标称重复轨道（nominal repetitive orbit）。Cycle 1的开始时间是2008年7月12日 01:20:05 GMT(*).

> 格林威治时间 (GMT)，指位于英国伦敦郊区的皇家格林尼治天文台的标准时间，因为本初子午线被定义在通过那里的经线。自1924年2月5日开始，格林尼治天文台每隔一小时会向全世界发放调时信息。

Jason任务支持像气候变化和可预测性计划（ClimateVariability and Predictability program (CLIVAR)）、全球海洋资料同化实验（Global Ocean Data Assimilation Experiment (GODAE)）一样的研究计划。

### 2.3.OSTM/Jason-2要求（Requirements）

任务的主要部分包括：

- 一个带有测高系统的地球轨道卫星，测量卫星到海面的高度。
- 一个精确的定轨系统，测高值参考大地坐标。
- 一个数据分析和发布系统，处理卫星数据，检验它的精度，让它们可以用于科学界。
- 一个首席研究员计划，从基于卫星观测的运行程序、科学研究中获得反馈。

海面高测量值的精度必须在3.4cm或者更高（1HZ中）才能满足任务目标。OSTM/JASON-2卫星是为了满足任务目标（AD1）、继承jason1任务专门设计的。至于jason1，测高产品（没有经过验证）准实时的发布，是在计划中的。临时的（IGDR）和最后的（GDR）科学产品的发布要延迟一些（6节的talbe15），与jason1的模型一样。

为了确保OSTM/Jason-2科学和任务目标的完成，建立了如下的要求：

#### 2.3.1.海平面测量精度

       一般来说OSTM/Jason-2

#### 2.3.2.采样策略

#### 2.3.3.潮汐的别名（Aliases）

#### 2.3.4.持续时间和覆盖范围

### 2.4.卫星描述

#### 2.4.1.卫星的特点

#### 2.4.2.传感器

##### 2.4.2.1.Poseidon-3高度计

##### 2.4.2.2.增强的微波辐射计（AMR）

##### 2.4.2.3. DORIS系统

##### 2.4.2.4. 激光反射器阵列

##### 2.4.2.5. GPS接收机

##### 2.4.2.6. CARMEN-2辐射探测器

##### 2.4.2.7. LPT检测单元

##### 2.4.2.8. T2L2探测器

#### 2.4.3.轨道

##### 2.4.3.1.穿过赤道的经度（按pass大小排序）

##### 2.4.3.2.穿过赤道的经度（按经度大小排序）

#### 2.4.4.OSTM/Jason-2项目的各个阶段

### 2.5.数据处理和分布

#### 2.5.1.访问NRT数据

#### 2.5.2.访问离线数据

#### 2.5.3.文档和读取数据的软件

### 2.6.通过NOAA访问数据

#### 2.6.1.访问NRT数据

#### 2.6.2.访问离线数据

### 2.7.通过EUMETSAT访问数据

#### 2.7.1.访问NRT数据

#### 2.7.2.访问归档数据（archiveddata）

### 2.8.通过GTS访问数据

##### 2.8.1.1.GTS access for OGDR-BUFR files

### 2.9.CNES数据分布

### 2.10.Jason-1在AVISO和PODAAC的数据产品

#### 2.10.1.AVISO的ftp服务器上的Jason-1产品

#### 2.10.2.PODAAC的ftp服务器上的Jason-1产品

## 3.产品发展历史

### 3.1.“模型”和“标准”的历史

#### 3.1.1.GDR产品版本“T”和O/IGDR产品版本“C”

##### 3.1.1.1.模型和标准

##### 3.1.1.2.海面高度偏差

##### 3.1.1.3.Time-tag偏差

#### 3.1.2.“d”版本产品

##### 3.1.2.1.模型和标准

##### 3.1.2.2.海面高度偏差

### 3.2.“d”版本产品的模型和编辑

#### 3.2.1.轨道模型

#### 3.2.2.平均海平面

#### 3.2.3.平均动力地形

#### 3.2.4.大地水准面

#### 3.2.5.海洋测深学

#### 3.2.6.海洋潮汐

##### 3.2.6.1.GOT4.8海潮模型

##### 3.2.6.2.FES2004海潮模型

#### 3.2.7.数据编辑标准

## 4.使用(O)(I)GDR数据

### 4.1.概述

### 4.2.典型的测高数据计算

#### 4.2.1.改正的测高距离

#### 4.2.2.海面高度和海平面异常

##### 4.2.2.1.潮汐影响

##### 4.2.2.2.地球物理表面-平均海平面或者大地水准面

#### 4.2.3.平均海平面和交叉轨道梯度的改正（Adjustment）

#### 4.2.4.平滑电离层改正

#### 4.2.5.电离层改正所有的电子含量

#### 4.2.6.距离压缩

## 5.测高数据

### 5.1.精密星历

### 5.2.测高距离

### 5.3.大地水准面

### 5.4.平均海平面

### 5.5.平均动力地形

### 5.6.地球物理改正

#### 5.6.1.对流层（干、湿分量）

#### 5.6.2.电离层

#### 5.6.3.海浪（海况偏差）

### 5.7.雨（flag）标记

### 5.8.冰（Ice）标记

### 5.9.潮汐

#### 5.9.1.地心海洋潮汐

#### 5.9.2.长周期海洋潮汐

#### 5.9.3.固体潮

#### 5.9.4.极潮

### 5.10.逆气压效应

#### 5.10.1.逆气压改正

#### 5.10.2.对大气外力作用的正压和斜压响应

### 5.11.sigma 0

### 5.12.风速

### 5.13.测深信息

## 6.数据描述

### 6.1.数据格式

#### 6.1.1.NetCdf格式和CF约定

#### 6.1.2.NetCDF数据模型

##### 6.1.2.1.Dimensions（维度）

##### 6.1.2.2.变量

##### 6.1.2.3.坐标变量和辅助坐标变量

##### 6.1.2.4.属性

#### 6.1.3.公共数据语言（TheCommon Data Language）

### 6.2.全局属性

### 6.3.数据集

### 6.4.软件

#### 6.4.1.“ncdump”

#### 6.4.2.额外的通用软件

##### 6.4.2.1.ncbrowse

##### 6.4.2.2.netCDF Operator (NCO)

#### 6.4.3.额外的特殊的软件：”BRAT”

### 6.5.OGDR BUFR产品

## 附录 A - 参考文献

## 附录 B - 缩略语

## 附录 C - 联络方式
