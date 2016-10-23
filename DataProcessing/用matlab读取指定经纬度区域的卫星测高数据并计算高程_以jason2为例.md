# 1.确定研究区域使用的数据

本文以jason2数据为例。看过handbook就应该知道jason2卫星的重访周期大约是10天，飞完一个完整的周期会有254条pass（一条pass就是一个半圆），如何确定自己的研究区域在哪条pass上？

在AVISO+网站上提供了多颗卫星的地面轨迹文件（也就是pass文件），地址在[这里](http://www.aviso.altimetry.fr/en/data/tools/pass-locator.html),（目录树如下：DATA-> Products guide->Tools->Pass locator），该文件是kml格式，用Google Earth打开，可以看到如下视图：

![1](http://oei6q3yqv.bkt.clouddn.com/2016/10/23/jason2/1.png)

找到自己需要的地区，观察有哪些pass经过，并对地面轨迹线条（红色）单击鼠标右键，查看名称，如：“Ground Track 153”则代表该轨迹的pass号为153.

找到pass号后，去下载对应的数据，此处以sgdr数据为例。

# 2.截取数据

一条pass差不多是半个地球周长了，也许我们并不需要这么多的数据，只是某个局部地区的，截取数据有如下做法：

1. 用matlab读数据的时候进行筛选。
2. 将所有数据读取出来再做筛选。

二者并无本质区别，但是明显用方法1要明智一些，此处也只介绍方法1.

## 2.1高程计算原理

此处简要介绍如何计算高程，一张图片就明白：

 ![2](http://oei6q3yqv.bkt.clouddn.com/2016/10/23/jason2/2.png)

可见，

Height=Altitude-Range

Height为水面到参考椭球的距离，此为大地高（概念不清者，请百度高程系统），Altitude为卫星轨道至参考椭球的距离，Range为卫星至水位的距离。

## 2.2 计算需要读取哪些数据？

根据计算原理，可知，至少我们需要Height、Altitude、Range这几个数据，才能计算高程，同时还需要知道经纬度、cycle号，pass号，测距改正信息（此处先不做改正，力求简单），波形信息（此处也不谈）等等。

为了能做最简单的演示读取了如下数据：

```matlab
cycle_number
pass_number
time
lat
lon
alt
range
```

## 2.3 使用`find`函数截取指定区域的数据

在matlab中`find`函数的作用是：返回满足指定条件的数据的索引（index），详细信息使用`help find`查看。

例如，在文件“index_data.txt”中有如下数据：

```matlab
1 2 3 4
1 1 3 3
1 5 5 1
1 12 6 3
1 17 4 4
1 1 3 3
```

运行如下代码：

```matlab
data = load('index_data.txt')
in   = find(data(:,2)>10)    % 条件：第2列数据大于10
data(in,:)                   % 输出满足条件的数据
```

得：

```matlab
in =
     4
     5
ans =
     1    12     6     3
     1    17     4     4
```

可以看到，使用`find`函数后，返回了第2列数据大于10的行数，此即`find`函数的作用。

## 2.4 一段代码

如果没有数据，示例数据在此：链接：http://pan.baidu.com/s/1o7R6nqE 密码：29b4。此段代码包括了数据读取、计算、输出：

```matlab
fname_in     = 'JA2_GPS_2PdP006_240_20080908_230802_20080909_000414.nc';
cycle_number = ncreadatt(fname_in,'/','cycle_number');
pass_number  = ncreadatt(fname_in, '/', 'pass_number');
time         = ncread(fname_in,'time'); % 时间
lat          = ncread(fname_in,'lat');  % 纬度
lon          = ncread(fname_in,'lon');  % 经度
alt          = ncread(fname_in,'alt');  % 轨道高
range        = ncread(fname_in, 'range_ku');

lat_index     = find(lat >= 22.2484 & lat <= 22.3975); % 选出纬度在该区域的数据
is_range_nan  = isnan(range);          % 返回与range一样大小的向量，判断range的值是否为NaN，是则为1，不是则为0
out_file_id   = fopen('data.txt','w'); % 存放数据文件

for j = min(lat_index) : max(lat_index)
    if(is_range_nan(j) == 0)        % 不为缺省值才做计算
        height = alt(j) - range(j); % 计算水位高
        fprintf(out_file_id, '%d\t%d\t%.3f\t%.3f\t', cycle_number, pass_number, lon(j), lat(j));
        fprintf(out_file_id, '%.3f\t%.3f\t%.3f\t%.8f\n', alt(j), range(j), height, time(j));
    end
end
fclose(out_file_id);
```

注：纬度的选取，一般只用纬度限制就可以了，在Google Earth中可以查看经纬度。

运行代码，得到的结果在"data.txt"文件中：

```matlab
6	240	123.979	22.356	1341770.279	1341764.618	5.661	274231709.82634497
6	240	124.001	22.307	1341759.464	1341735.933	23.531	274231710.84649801
6	240	124.022	22.258	1341748.669	1341725.692	22.977	274231711.86665201
```

每列依次为：cycle、pass、经度（lon）、纬度（lat）、轨道高（alt）、测距值（range）、**计算得到的高程（height）**、时间（time）。

**说明**：

此为最简单的读取与计算。此处的range值为1hz的，还有20hz的数据，1hz的数据为20hz数据的线性回归。

如果考虑20hz的不同range值、改正数据、波形数据，代码会有一些改变。

