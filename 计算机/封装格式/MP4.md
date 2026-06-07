MP4文件由多个box组成，每个box存储不同的信息，且box之间是树状结构，如下图所示。
![[Pasted image 20240929133259.png]]
box类型有很多，下面是3个比较重要的顶层box：

- ftyp：File Type Box，描述文件遵从的MP4规范与版本；
- moov：Movie Box，媒体的metadata信息，有且仅有一个。
- mdat：Media Data Box，存放实际的媒体数据，一般有多个；

![[Pasted image 20240929133339.png]]

## MP4 Box简介

1个box由两部分组成：box header、box body。

1. box header：box的元数据，比如box type、box size。
2. box body：box的数据部分，实际存储的内容跟box类型有关，比如mdat中body部分存储的媒体数据。

box header中，只有type、size是必选字段。当size==0时，存在largesize字段。在部分box中，还存在version、flags字段，这样的box叫做Full Box。当box body中嵌套其他box时，这样的box叫做container box。
![[Pasted image 20240929133417.png]]
### Box Header

字段定义如下：

- type：box类型，包括 “预定义类型”、“自定义扩展类型”，占4个字节；
    - 预定义类型：比如ftyp、moov、mdat等预定义好的类型；
    - 自定义扩展类型：如果type==uuid，则表示是自定义扩展类型。size（或largesize）随后的16字节，为自定义类型的值（extended_type）
- size：包含box header在内的整个box的大小，单位是字节。当size为0或1时，需要特殊处理：
    - size等于0：box的大小由后续的largesize确定（一般只有装载媒体数据的mdat box会用到largesize）；
    - size等于1：当前box为文件的最后一个box，通常包含在mdat box中；
- largesize：box的大小，占8个字节；
- extended_type：自定义扩展类型，占16个字节；

### Box vs FullBox

在Box的基础上，扩展出了FullBox类型。相比Box，FullBox 多了 version、flags 字段。

- version：当前box的版本，为扩展做准备，占1个字节；
- flags：标志位，占24位，含义由具体的box自己定义；

## ftyp（File Type Box）

### 什么是isom

isom（ISO Base Media file）是在 MPEG-4 Part 12 中定义的一种基础文件格式，MP4、3gp、QT 等常见的封装格式，都是基于这种基础文件格式衍生的。

ftyp 的几个字段的含义：

- major_brand：比如常见的 isom、mp41、mp42、avc1、qt等。它表示“最好”基于哪种格式来解析当前的文件。举例，major_brand 是 A，compatible_brands 是 A1，当解码器同时支持 A、A1 规范时，最好使用A规范来解码当前媒体文件，如果不支持A规范，但支持A1规范，那么，可以使用A1规范来解码；
- minor_version：提供 major_brand 的说明信息，比如版本号，不得用来判断媒体文件是否符合某个标准/规范；
- compatible_brands：文件兼容的brand列表。比如 mp41 的兼容 brand 为 isom。通过兼容列表里的 brand 规范，可以将文件 部分（或全部）解码出来；

## moov（Movie Box）

Movie Box，存储 mp4 的 metadata，一般位于mp4文件的开头。

moov中，最重要的两个box是 mvhd 和 trak：

- mvhd：Movie Header Box，mp4文件的整体信息，比如创建时间、文件时长等；
- trak：Track Box，一个mp4可以包含一个或多个轨道（比如视频轨道、音频轨道），轨道相关的信息就在trak里。trak是container box，至少包含两个box，tkhd、mdia；






