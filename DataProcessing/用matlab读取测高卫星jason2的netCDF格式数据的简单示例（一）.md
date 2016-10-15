默认本文读者已经浏览过Jason2的HandBook，至少对测高有一定的了解。

本文先介绍Jason2数据格式，再附matlab读取数据的示例代码。

# 1.Jason2数据格式-NetCDF

如果连数据格式都不知道，那如何读取数据呢？所以先要了解数据格式。

Jason2数据采用NetCDF格式存储数据，数据文件以.nc结尾。百度百科的解释：[netCDF](http://baike.baidu.com/link?url=aVPZmx5dS3FYAnRpNih5B_mruucERycWCwFjCW0ZcR1q5PO1jFeFHP5Ksl6Y4FbsTqGhdL7IU7JnRtZLvReLIa) 。在《OSTMJason-2 Products Handbook 》的第6节中，有对数据格式的介绍，可以查看。

为对其有直观认识，用**HDF Explorer**（如果你不想自己百度点[这里](http://pan.baidu.com/s/1ge8Nk4N)）软件打开一个nc文件，（如果你没有数据，[这里](http://pan.baidu.com/s/1i46Hntr)有一个示例数据）。打开后可以看到如下内容：

 ![1](http://oei6q3yqv.bkt.clouddn.com/2016/10/15/netcdf/1.png)

可以看到一个nc文件是由**Dimensions**、**Variables**、**Attributes**三个部分组成，这也就是netCDF的数据结构了。下面针对三个部分分别介绍。

## 1.1 Dimensions（维度）

Dimensions里面存储的是维度信息：

 ![2](http://oei6q3yqv.bkt.clouddn.com/2016/10/15/netcdf/2.png)

jason2数据最大维度为3维，分别是time 、time_ind、wvf_ind。下面这个表格是jason2的handbook里面的（55页），此处的示例数据为SGDR。

| 维度名字     | 值                                      | SSHA    Data Set | GDR    Data Set | SGDR    Data Set |
| -------- | -------------------------------------- | ---------------- | --------------- | ---------------- |
| time     | Number of measurements in the file     | √                | √               | √                |
| meas_ind | 20 (number of elementary measurements) |                  | √               | √                |
| wvf_ind  | 104 (number of waveform samples)       |                  |                 | √                |

简单的理解，数据是存储在变量中的，将其看作N维矩阵即可。要说明的是，不同的变量对应的维度是不同的。

1.一维

如1hz的测距值，只有一个维度（time），也就是它由time作为索引唯一确定。

2.二维

如20hz的测距值，有二个维（time, meas_ind），它由time, meas_ind两个值共同唯一确定。

3.三维

从“wvf_ind”字面就可以看出，第三维是专门为波形设计的，一个波形点的数据由time, meas_ind, wvf_ind三个值共同唯一确定。

## 1.2 Variables（变量） 

变量是用来存数据的，所以经纬度、测距值、改正数等等数据都在这里面，在**HDF Explorer**可以看得很清楚。

## 1.3 Attributes（属性） 

属性给出了变量或数据集本身的辅助信息属性，比如传感器名称、cycle number、pass number等等都在这里面，在**HDF Explorer**可以看得很清楚。

# 2.数据读取

高版本（2012以上？）的Matlab自带nc工具箱，里面有很多函数，如下：

## 2.1 `ncdisp`

查看nc文件的信息，使用如下命令：

```matlab
ncdisp('D:\JA2_GPS_2PdP006_240_20080908_230802_20080909_000414.nc')
```

将会给出所有维度、变量、属性的信息。

## 2.2 `ncread`

读取变量，用法：

```matlab
vardata = ncread(source,varname)
```

`source`为nc文件路径，相对绝对路径都行，`varname`为需要读取的变量名称。

示例，使用如下命令：

```matlab
ncread('D:\JA2_GPS_2PdP006_240_20080908_230802_20080909_000414.nc','lon')
```

将给出所有的经度变量，其他变量读取方式相同。

## 2.3 `ncreadatt`

读取属性，用法：

```matlab
attvalue = ncreadatt(source,location,attname)
```

`source`为nc文件路径，相对绝对路径都行，`varname`为需要读取的变量名称。需要要说明的是`location`，由于属性分全局和局部的，对应的`location`不同，像cycle number、pass number都是全局属性，全局属性的`location`是'/' (forward slash)。

示例，使用如下命令：

```matlab
ncreadatt('D:\JA2_GPS_2PdP006_240_20080908_230802_20080909_000414.nc','/','cycle_number')
```

将给出该nc文件的cycle number。

到此，可以进行一些数据的读取和计算了。
